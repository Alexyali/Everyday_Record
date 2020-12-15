# Sprout代码分析

`sproutbt2.cc sproutconn.cc network.cc`

### 类构造函数

类的一类特殊成员函数，在每次创建类的新对象时执行。构造函数的名称和类名完全相同，不会返回任何类型。构造函数可以用于为某些成员变量设置初始值。

```
# new a object and give the value
net = new Network::SproutConnection( NULL, argv[ 1 ] );
# definition of SproutConnection
SproutConnection::SproutConnection( const char *desired_ip, const char *desired_port ) : conn( desired_ip, desired_port ){}
```

### 函数的重载

重载函数是指多个函数拥有相同的函数名，但是拥有不同的形参表。在调用该函数时，系统会根据情况自动选择恰当的函数来执行。

类的构造函数也可以重载。构造函数的重载允许对象采用多个方法来初始化。

```
SproutConnection::SproutConnection( const char *desired_ip, const char *desired_port )

SproutConnection::SproutConnection( const char *key_str, const char *ip, int port )
```

### Assert()

在C++中，我们经常需要做出假设，如果出错则程序不能正常运行。`assert`用于判断输入的条件是否成立，如果不成立，则终止程序并展示错误信息。

```
assert( bytes_to_send >= 0 );
```



