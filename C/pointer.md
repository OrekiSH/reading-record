- 间接寻址运算符(`*`): `int *a`, `int* a`(这种写法的缺陷: `int* b, c, d`)
- 取地址运算符(`&`): `int i, *p = &i;`
- `char *message = "hello world!"`, 将message声明为一个指向字符的指针，并用字符串常量中的第1个字符的地址对该指针进行初始化
- 若指针变量`p`声明后未初始化，`*p`是未定义的，给`*p`赋值后p可能指向内存中的任何地方(可能属于程序或操作系统)
- `typedef char *ptr_to_char;`, 该声明将`ptr_to_char`作为指向字符的指针类型的新名字。 `ptr_to_char a`，变量`a`是一个指向字符的指针
- `#define d_ptr_to_char char*`, `d_ptr_to_char a, b;`, 变量`b`将被声明为一个字符而非指针
- `int const a; const int a;`。 `int const *pci;`(pci是一个指向整型常量的指针)，`int * const cpi`(cpi是一个指向整型的常量指针，指针的值无法修改，但可以修改它指向的整型的值)
- 当组成程序的各个源文件分别被编译后，所有目标文件以及那些从一个或多个函数库中引用的函数被链接在一起，相同的标识符出现在不同的源文件中时表示的是同一个实体还是不同的实体?
- 链接属性: external(位于不同源文件的声明被当做同一实体), internal(同一源文件中声明被当做同一实体), none(声明被当做不同的实体)
- 声明的链接属性为external时, 在其前面加上`static`关键字可以将其链接属性变为internal
- 变量的存储类型(storage class)指的是存储变量值的内存类型。代码块之外声明的变量或代码块内声明但被加上`static`关键字的变量都被存储在静态内存中，被称为静态变量，在程序运行之前创建，在程序执行期间始终存在; 代码块内声明的非`static`变量都被存储在堆栈内存中，被称为自动变量，当程序运行到声明变量的代码块时创建；代码块内声明的`register`变量部分被存储在硬件寄存器中，被称为寄存器变量
- 为了存储更大的值，我们将两个以上的字节合为一个更大的内存单位--字, 尽管一个字包含4个字节，但它仍然只有一个地址(最左/右边的字节位置)
- 若使用整型算术指令，比特序列将被解释为整数，浮点型指令同理
- 指针的指针: `int a = 1; int *b = &a; int **c = &b;`

```c
// b, c, f链接属性为external
// a, d, e, g为none
typedef char *a;
int b;
int c(int d) {
  int e;
  int f(int g);
}

// b链接属性为internal
// c, f为external
// a, d, e, g为none
typedef char *a;
static int b;
int c(int d) {
  static int e;
  int f(int g);
}
```

```c
static int i;
int func() {
  int j;
  extern int k; // external
  extern int i; // internal
}
```
```c
char ch = 'a';
char *cp = &ch;
*cp + 1
*(cp + 1)
++cp
cp++
*++cp
*cp++
++*cp
(*cp)++
++*++cp

size_t strlen(char *string) {
  int length = 0;
  while(*string++ != '\0') {
    length += 1;
  }
  return length;
}
```
