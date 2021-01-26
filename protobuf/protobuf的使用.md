### protobuf的使用

---

#### 基本类型定义

```protobuf
message SearchRequest {
  required string query = 1;
  optional int32 page_number = 2;
  optional int32 result_per_page = 3;
}
```

#### field number的作用

As you can see, each field in the message definition has a **unique number**. These numbers are used to identify your fields in the [message binary format](https://developers.google.com/protocol-buffers/docs/encoding), and should not be changed once your message type is in use. Note that field numbers in the range 1 through 15 take one byte to encode, including the field number and the field's type (you can find out more about this in [Protocol Buffer Encoding](https://developers.google.com/protocol-buffers/docs/encoding#structure)). Field numbers in the range 16 through 2047 take two bytes. So you should reserve the field numbers 1 through 15 for very frequently occurring message elements. Remember to leave some room for frequently occurring elements that might be added in the future.

The smallest field number you can specify is 1, and the largest is 229 - 1, or 536,870,911. You also cannot use the numbers 19000 through 19999 (`FieldDescriptor::kFirstReservedNumber` through `FieldDescriptor::kLastReservedNumber`), as they are reserved for the Protocol Buffers implementation - the protocol buffer compiler will complain if you use one of these reserved numbers in your `.proto`. Similarly, you cannot use any previously [reserved](https://developers.google.com/protocol-buffers/docs/overview#reserved) field numbers.

#### Specifying field Rules

You specify that message fields are one of the following:

- `required`: a well-formed message **must have exactly one** of this field.
- `optional`: a well-formed message can **have zero or one** of this field (but not more than one).
- `repeated`: this field can be **repeated any number of times** (including zero) in a well-formed message. The order of the repeated values will be preserved.

For historical reasons, `repeated` fields of scalar numeric types aren't encoded as efficiently as they could be. New code should use the special option `[packed=true]` to get a more efficient encoding. For example:

```protobuf
repeated int32 samples = 4 [packed=true];
```

#### 注意使用required类型的数据

**Required Is Forever** You should be very careful about marking fields as `required`. If at some point you wish to stop writing or sending a required field, it will be problematic to change the field to an optional field – old readers will consider messages without this field to be incomplete and may reject or drop them unintentionally. You should consider writing application-specific custom validation routines for your buffers instead. Some engineers at Google have come to the conclusion that using `required` does more harm than good; they prefer to use only `optional` and `repeated`. However, this view is not universal.

#### Reserved Fields

If you [update](https://developers.google.com/protocol-buffers/docs/overview#updating) a message type by entirely removing a field, or commenting it out, future users can reuse the field number when making their own updates to the type. This can cause severe issues if they later load old versions of the same `.proto`, including data corruption, privacy bugs, and so on. One way to make sure this doesn't happen is to specify that the field numbers (and/or names, which can also cause issues for JSON serialization) of your deleted fields are `reserved`. The protocol buffer compiler will complain if any future users try to use these field identifiers.

```protobuf
message Foo {
  reserved 2, 15, 9 to 11;
  reserved "foo", "bar";
}
```

Note that you can't mix field names and field numbers in the same `reserved` statement.

#### Scalar Value Types

A scalar message field can have one of the following types – the table shows the type specified in the `.proto` file, and the corresponding type in the automatically generated class:

| .proto Type | Notes                                                        | C++ Type | Java Type  | Python Type[2]                       | Go Type  |
| :---------- | :----------------------------------------------------------- | :------- | :--------- | :----------------------------------- | :------- |
| double      |                                                              | double   | double     | float                                | *float64 |
| float       |                                                              | float    | float      | float                                | *float32 |
| int32       | Uses variable-length encoding. Inefficient for encoding negative numbers – if your field is likely to have negative values, use sint32 instead. | int32    | int        | int                                  | *int32   |
| int64       | Uses variable-length encoding. Inefficient for encoding negative numbers – if your field is likely to have negative values, use sint64 instead. | int64    | long       | int/long[3]                          | *int64   |
| uint32      | Uses variable-length encoding.                               | uint32   | int[1]     | int/long[3]                          | *uint32  |
| uint64      | Uses variable-length encoding.                               | uint64   | long[1]    | int/long[3]                          | *uint64  |
| sint32      | Uses variable-length encoding. Signed int value. These more efficiently encode negative numbers than regular int32s. | int32    | int        | int                                  | *int32   |
| sint64      | Uses variable-length encoding. Signed int value. These more efficiently encode negative numbers than regular int64s. | int64    | long       | int/long[3]                          | *int64   |
| fixed32     | Always four bytes. More efficient than uint32 if values are often greater than 228. | uint32   | int[1]     | int/long[3]                          | *uint32  |
| fixed64     | Always eight bytes. More efficient than uint64 if values are often greater than 256. | uint64   | long[1]    | int/long[3]                          | *uint64  |
| sfixed32    | Always four bytes.                                           | int32    | int        | int                                  | *int32   |
| sfixed64    | Always eight bytes.                                          | int64    | long       | int/long[3]                          | *int64   |
| bool        |                                                              | bool     | boolean    | bool                                 | *bool    |
| string      | A string must always contain UTF-8 encoded or 7-bit ASCII text. | string   | String     | unicode (Python 2) or str (Python 3) | *string  |
| bytes       | May contain any arbitrary sequence of bytes.                 | string   | ByteString | bytes                                | []byte   |

#### Optional Fields 和 Default Values

As mentioned above, elements in a message description can be labeled `optional`. A well-formed message may or may not contain an optional element. When a message is parsed, if it does not contain an optional element, the corresponding field in the parsed object is set to the default value for that field. The default value can be specified as part of the message description. For example, let's say you want to provide a default value of 10 for a `SearchRequest`'s `result_per_page` value.

```protobuf
optional int32 result_per_page = 3 [default = 10];
```

If the default value is not specified for an optional element, a type-specific default value is used instead: for strings, the default value is the empty string. For bytes, the default value is the empty byte string. For bools, the default value is false. For numeric types, the default value is zero. For enums, the default value is the first value listed in the enum's type definition. This means care must be taken when adding a value to the beginning of an enum value list.

