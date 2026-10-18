---
title: "攻防世界RE"
date: 2023-10-20 20:38:32
---
# Reversing-x64Elf-100

追踪main 函数发现

```plaintext
__int64 __fastcall main(int a1, char **a2, char **a3)
{
  char s[264]; // [rsp+0h] [rbp-110h] BYREF
  unsigned __int64 v5; // [rsp+108h] [rbp-8h]

  v5 = __readfsqword(0x28u);
  printf("Enter the password: ");
  if ( !fgets(s, 255, stdin) )
    return 0LL;
  if ( (unsigned int)sub_4006FD((__int64)s) )   // 关键函数实现对比
  {
    puts("Incorrect password!");
    return 1LL;
  }
  else
  {
    puts("Nice!");
    return 0LL;
  }
}
```

追踪sub_4006FD函数可得

```plaintext
__int64 __fastcall sub_4006FD(__int64 a1)
{
  int i; // [rsp+14h] [rbp-24h]
  __int64 v3[4]; // [rsp+18h] [rbp-20h]

  v3[0] = (__int64)"Dufhbmf";
  v3[1] = (__int64)"pG`imos";
  v3[2] = (__int64)"ewUglpt";
  for ( i = 0; i <= 11; ++i )
  {
    if ( *(char *)(v3[i % 3] + 2 * (i / 3)) - *(char *)(i + a1) != 1 )  // 具体意思就是访问数组中的特定的字符串的特定的值与输入的字符串的指定的值相减等于1
      return 1LL;
  }
  return 0LL;
}
```

```plaintext
这段代码是在进行一个条件判断，如果条件满足，则返回 1；否则继续执行。具体来说，该语句检查 v3 是一个长度为 3 的字符指针数组（或者是一个包含三个字符数组的二维数组），其中第 i % 3 个元素是一个字符数组，长度为偶数。并且，在索引为 i / 3 的位置上的字符减去地址为 i + a1 的字符的 ASCII 码的差不等于 1。

下面来逐个解释一下每个部分的含义：

    v3 是一个字符指针数组（或者是一个包含三个字符数组的二维数组），其下标从 0 开始，因此 i % 3 会得到 v3 中第 i 个元素。该元素应该是一个字符数组，长度为偶数。

    2 * (i / 3) 是通过整除来计算出字符数组中要访问的下标。由于每个数组长度为偶数，因此 (i / 3) 得到的结果也是偶数。所以 2 * (i / 3) 就是用来访问字符数组中特定位置的下标。

    *(char *)(v3[i % 3] + 2 * (i / 3)) 表示根据上述计算得到字符数组中特定位置的值。

    i + a1 表示以地址 a1 为起始位置的指针偏移 i 个字节的位置得到的值。

    *(char *)(i + a1) 表示以地址 a1 为起始位置的指针偏移 i 个字节的位置得到的值。

    *(char *)(v3[i % 3] + 2 * (i / 3)) - *(char *)(i + a1) 表示计算上述两个值的差，也就是字符数组中特定位置的值减去 a1 指针偏移后的值。

    最后，检查这个差是否等于 1。如果不相等，返回 1；否则继续执行。注意，该函数返回一个 long long 类型的整数（1LL），表示函数执行成功或失败的状态。
```

```plaintext
key1="Dufhbmf"
key2="pG`imos"
key3="ewUglpt"
flag=""
key4=[key1,key2,key3]
for i in range(12):
	flag+=chr(ord(key4[i%3][(2*int(i/3))]) -1)
print(flag)
```

# 666

没有加壳

```plaintext
int __cdecl main(int argc, const char **argv, const char **envp)
{
  char s[240]; // [rsp+0h] [rbp-1E0h] BYREF
  char v5[240]; // [rsp+F0h] [rbp-F0h] BYREF

  memset(s, 0, 0x1EuLL);
  printf("Please Input Key: ");
  __isoc99_scanf("%s", v5);
  encode(v5, s);
  if ( strlen(v5) == key )
  {
    if ( !strcmp(s, enflag) )
      puts("You are Right");
    else
      puts("flag{This_1s_f4cker_flag}");
  }
  return 0;
}
```

```plaintext
memset(s, 0, 0x1EuLL) 是一个内存操作函数，用于将内存块 s 的前 0x1E 个字节设置为0。具体来说，它的参数包括：

    s：指向要操作的内存块的指针。
    0：用于填充内存块的值，这里是0。
    0x1EuLL：指定要设置为0的字节数。

因此，执行 memset(s, 0, 0x1EuLL) 后，内存块 s 的前 0x1E 个字节都会被设置为0。这种操作常用于初始化内存块或清空敏感数据。注意，0x1EuLL 是一个长整型字面量，表示十进制的 30。
```

追踪函数encode，发现关键代码

```plaintext
int __fastcall encode(const char *a1, __int64 a2)
{
  char v3[104]; // [rsp+10h] [rbp-70h]
  int v4; // [rsp+78h] [rbp-8h]
  int i; // [rsp+7Ch] [rbp-4h]

  i = 0;
  v4 = 0;
  if ( strlen(a1) != key )
    return puts("Your Length is Wrong");
  for ( i = 0; i < key; i += 3 )
  {
    v3[i + 64] = key ^ (a1[i] + 6);
    v3[i + 33] = (a1[i + 1] - 6) ^ key;
    v3[i + 2] = a1[i + 2] ^ 6 ^ key;   
    *(_BYTE *)(a2 + i) = v3[i + 64];   // 内存复制操作
    *(_BYTE *)(a2 + i + 1LL) = v3[i + 33];
    *(_BYTE *)(a2 + i + 2LL) = v3[i + 2];
  }
  return a2;
}
```

```plaintext
*(_BYTE *)(a2 + i) 是一个内存访问操作，用于获取位于内存地址 (a2 + i) 的字节值。

具体解释如下：

    (a2 + i) 表示内存地址 a2 与偏移量 i 的和，即指向内存中的某个位置。
    (_BYTE *) 是类型转换操作符，将得到的地址转换为指向字节的指针。
    * 是间接引用操作符，通过指针访问该地址处的值。

因此，*(_BYTE *)(a2 + i) 表示获取指针 (a2 + i) 指向的内存地址处的字节值。这种语法常用于访问和处理特定地址上的数据。
 	
```

读取key的值为18，将enflag的ascii码转为10进制数。

脚本：

```python
str = [
  0x69, 0x7A, 0x77, 0x68, 0x72, 0x6F, 0x7A, 0x22, 0x22, 0x77,
  0x22, 0x76, 0x2E, 0x4B, 0x22, 0x2E, 0x4E, 0x69, 0x00
]
flag = ""
for i in range(0 , 18 ,3):
    flag += chr((int(str[i]) ^ 18) - 6)
    flag += chr((int(str[i+1]) ^ 18) + 6)
    flag += chr(int(str[i+2]) ^ 18 ^ 6)
print(flag)
```

# easyRE1

没加壳

```plaintext
int __cdecl main(int argc, const char **argv, const char **envp)
{
  char s1[264]; // [rsp+10h] [rbp-110h] BYREF
  unsigned __int64 v5; // [rsp+118h] [rbp-8h]

  v5 = __readfsqword(0x28u);
  puts("What is the password?");
  gets(s1);
  if ( !strcmp(s1, "the password") )
    puts("FLAG:db2f62a36a018bce28e46d976e3f9864");
  else
    puts("Wrong!!");
  return 0;
}
```

一眼就看出来

# lucknum

```plaintext
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int v4; // [rsp+Ch] [rbp-34h] BYREF
  char s[48]; // [rsp+10h] [rbp-30h] BYREF

  strcpy(s, "flag{c0ngr@tul@ti0n_f0r_luck_numb3r}");
  v4 = 0;
  __isoc99_scanf(&unk_2004, &v4);
  if ( v4 == 7 )
    return puts(s);
  else
    return puts("sorry!");
}
```

一眼看出来

# reverse_re3

```plaintext
__int64 sub_940()
{
  int v0; // eax
  int v2; // [rsp+8h] [rbp-218h]
  int v3; // [rsp+Ch] [rbp-214h]
  char v4[520]; // [rsp+10h] [rbp-210h] BYREF
  unsigned __int64 v5; // [rsp+218h] [rbp-8h]

  v5 = __readfsqword(0x28u);
  v3 = 0;
  memset(v4, 0, 0x200uLL);
  _isoc99_scanf(&unk_1278, v4, v4);
  while ( 1 )
  {
    do
    {
      v2 = 0;
      sub_86C();
      v0 = v4[v3];
      if ( v0 == 100 )
      {
        v2 = sub_E23();
      }
      else if ( v0 > 100 )
      {
        if ( v0 == 115 )
        {
          v2 = sub_C5A();
        }
        else if ( v0 == 119 )
        {
          v2 = sub_A92();
        }
      }
      else
      {
        if ( v0 == 27 )
          return 0xFFFFFFFFLL;
        if ( v0 == 97 )
          v2 = sub_FEC();
      }
      ++v3;
    }
    while ( v2 != 1 );
    if ( dword_202AB0 == 2 )
      break;
    ++dword_202AB0;
  }
  puts("success! the flag is flag{md5(your input)}");
  return 1LL;
}
```

追踪 sub_86C();函数，发现数组

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20231024184402.png)

将数据导出

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20231024190349.png)

删去前后的数组名和括号

使用脚本处理为3个15*15的矩阵

```plaintext
# 打开文本文件
with open('data.txt', 'r') as file:
    # 读取文件内容
    content = file.read()

# 将字符串分割为数字列表
data = list(map(int, content.replace(',', '').split()))

# 转换为矩阵
matrix = []
row = []

for i in range(len(data)):
    row.append(data[i])
    if (i + 1) % 15 == 0:
        matrix.append(row)
        row = []

# 输出矩阵
for i, row in enumerate(matrix):
    print(row)
    if (i + 1) % 15 == 0:
        print()
```

```plaintext
[1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
[1, 1, 1, 1, 1, 0, 3, 1, 1, 0, 0, 0, 0, 0, 0]
[1, 1, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0]
[1, 1, 1, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0]
[1, 1, 1, 1, 1, 0, 0, 0, 1, 1, 1, 1, 1, 0, 0]
[1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0]
[1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0]
[1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 1, 1, 0]
[1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0]
[1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 4, 0]
[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]

[1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
[1, 1, 0, 3, 1, 1, 1, 1, 1, 0, 0, 0, 0, 0, 0]
[1, 1, 0, 1, 1, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0]
[1, 1, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0]
[1, 1, 0, 1, 1, 0, 0, 0, 1, 1, 1, 1, 1, 0, 0]
[1, 1, 0, 1, 1, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0]
[1, 1, 0, 1, 1, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0]
[1, 1, 0, 1, 1, 0, 0, 0, 0, 0, 1, 1, 1, 1, 0]
[1, 1, 0, 1, 1, 0, 0, 0, 0, 0, 1, 0, 0, 1, 0]
[1, 1, 0, 1, 1, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0]
[1, 1, 0, 1, 1, 1, 1, 1, 1, 0, 1, 0, 1, 1, 0]
[1, 1, 0, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 0]
[1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 4, 0]
[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]
[1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1]

[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
[0, 3, 1, 1, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
[0, 0, 0, 1, 0, 1, 1, 1, 0, 0, 0, 0, 0, 0, 0]
[0, 0, 0, 1, 1, 1, 0, 1, 0, 0, 0, 0, 0, 0, 0]
[0, 0, 0, 0, 1, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0]
[0, 1, 1, 0, 1, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0]
[0, 0, 1, 1, 1, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0]
[0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0, 0, 0, 0]
[0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 0, 0, 0, 0]
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0]
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0]
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 0, 0, 0]
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 1, 1, 1, 0]
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0]
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 4, 0]
```

“3”是起点，“4”是终点，“1”是可走的，“0”是不可走的

最后将三个迷宫的解法，并在一起

```plaintext
ddsssddddsssdssdddddsssddddsssaassssdddsddssddwddssssssdddssssdddss
```

md5加密一下得到flag

# 1000Click

shift + F12搜索得到很多flag,发现只有一个调用的，该flag即为正确的flag

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20231024193956.png)

# crypt

下载附件得到crypt.exe，拖入exeinfope分析得64bit,无壳。拖入ida64，打开搜索main函数，f5反编译，发现了对字符串Str的复制。看到下方和Str有关联的是v9，点击**sub_140001120**，以及**sub_140001240**(v9, (__int64)v10, v4);可以得到两段函数。

[![img](https://eneldo.fun/wp-content/uploads/2022/11/image-1-300x171.png)](https://eneldo.fun/wp-content/uploads/2022/11/image-1.png) 【初始化S和T，以及初始化排列S】

[![img](https://eneldo.fun/wp-content/uploads/2022/11/image-300x229.png)](https://eneldo.fun/wp-content/uploads/2022/11/image.png) 【初始化密钥流】

那么经过分析，密钥就是Str字符串。看完两段函数后，返回main函数，最终找到if ( ((unsigned **int8)v10[i] ^ 34) != (unsigned** int8)byte_14013B000[i] )，这里对byte_14013B000[i] 进行了异或处理。自此我们可以判断加密方式是**RC4加密**

***对明文使用同一个密钥异或两次最后是得到原文的。**

因此只需要对byte_14013B000[i]进行rc4解密就可以了。解密脚本如下：

```plaintext
unsigned char ida_chars[] =
{
  0x9E, 0xE7, 0x30, 0x5F, 0xA7, 0x01, 0xA6, 0x53, 0x59, 0x1B, 
  0x0A, 0x20, 0xF1, 0x73, 0xD1, 0x0E, 0xAB, 0x09, 0x84, 0x0E, 
  0x8D, 0x2B, 0x00, 0x00
}
```

解密运算：

```plaintext
from Cryptodome.Cipher import ARC4

encrypted_data = bytes([0x9E, 0xE7, 0x30, 0x5F, 0xA7, 0x01, 0xA6, 0x53, 0x59, 0x1B, 0x0A, 0x20, 0xF1, 0x73, 0xD1, 0x0E, 0xAB, 0x09, 0x84, 0x0E, 0x8D, 0x2B])
key = b"12345678abcdefghijklmnopqrspxyz"  # 替换为你的密钥

cipher = ARC4.new(key)
decrypted_data = cipher.decrypt(encrypted_data)

# 转换为可变字节数组
decrypted_data = bytearray(decrypted_data)

# 对每个字节进行位异或运算
for i in range(len(decrypted_data)):
    decrypted_data[i] ^= 34

plaintext = decrypted_data.decode()

print(plaintext)
```

# happyctf

无壳拖入IDA

```plaintext
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int v4; // [esp+5Ch] [ebp-70h]
  char *v5; // [esp+60h] [ebp-6Ch]
  char v6[27]; // [esp+6Ch] [ebp-60h] BYREF
  char v7; // [esp+87h] [ebp-45h]
  char *v8; // [esp+88h] [ebp-44h]
  char *v9; // [esp+8Ch] [ebp-40h]
  char *v10; // [esp+90h] [ebp-3Ch]
  char v11[12]; // [esp+98h] [ebp-34h] BYREF
  char v12[24]; // [esp+A4h] [ebp-28h] BYREF
  int v13; // [esp+C8h] [ebp-4h]

  sub_402930();
  v13 = 0;
  sub_401530((int)&unk_4DDAF8, "please input flag");
  sub_4039B0(sub_402310);
  sub_401500(&dword_4DDA80, v12);
  if ( sub_405DE0(v12) == 24 )
  {
    sub_402A20(v11);
    LOBYTE(v13) = 1;
    sub_402570(v11);
    v10 = v12;
    v9 = (char *)sub_405270(v12);
    v8 = (char *)sub_4052B0(v12);
    while ( v9 != v8 )
    {
      v7 = *v9;
      sub_403B70(v7);   // 关键函数
      ++v9;
    }
    qmemcpy(v6, "rxusoCqxw{yqK`{KZqag{r`i", 24);
    sub_402590(v6);
    v5 = (char *)sub_405290(v11);
    v4 = sub_4052E0(v11);
    while ( v5 != (char *)v4 )
    {
      if ( !(unsigned __int8)sub_403BB0(*v5) )
      {
        sub_401530((int)&unk_4DDAF8, "error");
        sub_4039B0(sub_402310);
        LOBYTE(v13) = 0;
        sub_4034E0(v11);
        v13 = -1;
        sub_403450(v12);
        return 0;
      }
      ++v5;
    }
    sub_401530((int)&unk_4DDAF8, "good job");
    sub_4039B0(sub_402310);
    LOBYTE(v13) = 0;
    sub_4034E0(v11);
    v13 = -1;
    sub_403450(v12);
    return 0;
  }
  else
  {
    sub_401530((int)&unk_4DDAF8, "not enought");
    sub_4039B0(sub_402310);
    v13 = -1;
    sub_403450(v12);
    return 0;
  }
}
```

```plaintext
int __thiscall sub_403B70(void *this, char a2)
{
  char v3[65]; // [esp+Fh] [ebp-45h] BYREF
  void *v4; // [esp+50h] [ebp-4h]

  v4 = this;
  v3[0] = a2 ^ 0x14;   //关键代码与或运算
  sub_406170(v3);
  return ++dword_4DD8F8;
}
```

此代码主要实现了对输入的字符的每一个字母进行与或运算，最终与

```plaintext
rxusoCqxw{yqK`{KZqag{r`i
```

进行对比，所以直接对字符串每个字母进行与或即可。

```plaintext
str = "rxusoCqxw{yqK`{KZqag{r`i"
flag = ''
for i in range(len(str)):
    flag += chr(ord(str[i]) ^ 0x14)
print(flag)
```

# xxxorrr
