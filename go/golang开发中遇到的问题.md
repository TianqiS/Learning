### golang开发中遇到的问题

- 在GoLand Debug中遇到`plugin was built with a different version of package`

  解决方法：(摘自https://www.cnblogs.com/migoo/p/13036067.html)

  > 在编译plugin的时候添加参数`-gcflags="all=-N -l"`
  >
  > 例如：`go build -gcflags="all=-N -l" ...`
  >
  > 打开go的编译参数帮助可以看到参数含义`go tool compile -help`
  >
  > -N 关闭编译器优化
  >
  > -l 取消内联
  >
  > all= 作用于所有包
  >
  > 再次使用GoLand Debug，问题解决

