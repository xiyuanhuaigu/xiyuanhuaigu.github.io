---
title: "buuctfRE"
date: 2023-04-24 00:39:18
---
# Ascii码表

ASCII ((American Standard Code for Information Interchange): 美国信息交换标准代码）是基于拉丁字母的一套电脑编码系统，主要用于显示现代英语和其他西欧语言。它是最通用的信息交换标准，并等同于国际标准ISO/IEC 646。ASCII第一次以规范标准的类型发表是在1967年，最后一次更新则是在1986年，到目前为止共定义了128个字符。

ASCII码表具体如下所示：

| Bin (二进制) | Oct (八进制) | Dec (十进制) | Hex (十六进制) | 缩写/字符 | 解释 |
| --- | --- | --- | --- | --- | --- |
| 0000 0000 | 00 | 0 | 0x00 | NUL(null) | 空字符 |
| 0000 0001 | 01 | 1 | 0x01 | SOH(start of headline) | 标题开始 |
| 0000 0010 | 02 | 2 | 0x02 | STX (start of text) | 正文开始 |
| 0000 0011 | 03 | 3 | 0x03 | ETX (end of text) | 正文结束 |
| 0000 0100 | 04 | 4 | 0x04 | EOT (end of transmission) | 传输结束 |
| 0000 0101 | 05 | 5 | 0x05 | ENQ (enquiry) | 请求 |
| 0000 0110 | 06 | 6 | 0x06 | ACK (acknowledge) | 收到通知 |
| 0000 0111 | 07 | 7 | 0x07 | BEL (bell) | 响铃 |
| 0000 1000 | 010 | 8 | 0x08 | BS (backspace) | 退格 |
| 0000 1001 | 011 | 9 | 0x09 | HT (horizontal tab) | 水平制表符 |
| 0000 1010 | 012 | 10 | 0x0A | LF (NL line feed, new line) | 换行键 |
| 0000 1011 | 013 | 11 | 0x0B | VT (vertical tab) | 垂直制表符 |
| 0000 1100 | 014 | 12 | 0x0C | FF (NP form feed, new page) | 换页键 |
| 0000 1101 | 015 | 13 | 0x0D | CR (carriage return) | 回车键 |
| 0000 1110 | 016 | 14 | 0x0E | SO (shift out) | 不用切换 |
| 0000 1111 | 017 | 15 | 0x0F | SI (shift in) | 启用切换 |
| 0001 0000 | 020 | 16 | 0x10 | DLE (data link escape) | 数据链路转义 |
| 0001 0001 | 021 | 17 | 0x11 | DC1 (device control 1) | 设备控制1 |
| 0001 0010 | 022 | 18 | 0x12 | DC2 (device control 2) | 设备控制2 |
| 0001 0011 | 023 | 19 | 0x13 | DC3 (device control 3) | 设备控制3 |
| 0001 0100 | 024 | 20 | 0x14 | DC4 (device control 4) | 设备控制4 |
| 0001 0101 | 025 | 21 | 0x15 | NAK (negative acknowledge) | 拒绝接收 |
| 0001 0110 | 026 | 22 | 0x16 | SYN (synchronous idle) | 同步空闲 |
| 0001 0111 | 027 | 23 | 0x17 | ETB (end of trans. block) | 结束传输块 |
| 0001 1000 | 030 | 24 | 0x18 | CAN (cancel) | 取消 |
| 0001 1001 | 031 | 25 | 0x19 | EM (end of medium) | 媒介结束 |
| 0001 1010 | 032 | 26 | 0x1A | SUB (substitute) | 代替 |
| 0001 1011 | 033 | 27 | 0x1B | ESC (escape) | 换码(溢出) |
| 0001 1100 | 034 | 28 | 0x1C | FS (file separator) | 文件分隔符 |
| 0001 1101 | 035 | 29 | 0x1D | GS (group separator) | 分组符 |
| 0001 1110 | 036 | 30 | 0x1E | RS (record separator) | 记录分隔符 |
| 0001 1111 | 037 | 31 | 0x1F | US (unit separator) | 单元分隔符 |
| 0010 0000 | 040 | 32 | 0x20 | (space) | 空格 |
| 0010 0001 | 041 | 33 | 0x21 | ! | 叹号 |
| 0010 0010 | 042 | 34 | 0x22 | “ | 双引号 |
| 0010 0011 | 043 | 35 | 0x23 | # | 井号 |
| 0010 0100 | 044 | 36 | 0x24 | $ | 美元符 |
| 0010 0101 | 045 | 37 | 0x25 | % | 百分号 |
| 0010 0110 | 046 | 38 | 0x26 | & | 和号 |
| 0010 0111 | 047 | 39 | 0x27 | ‘ | 闭单引号 |
| 0010 1000 | 050 | 40 | 0x28 | ( | 开括号 |
| 0010 1001 | 051 | 41 | 0x29 | ) | 闭括号 |
| 0010 1010 | 052 | 42 | 0x2A | * | 星号 |
| 0010 1011 | 053 | 43 | 0x2B | + | 加号 |
| 0010 1100 | 054 | 44 | 0x2C | , | 逗号 |
| 0010 1101 | 055 | 45 | 0x2D | - | 减号/破折号 |
| 0010 1110 | 056 | 46 | 0x2E | . | 句号 |
| 0010 1111 | 057 | 47 | 0x2F | / | 斜杠 |
| 0011 0000 | 060 | 48 | 0x30 | 0 | 字符0 |
| 0011 0001 | 061 | 49 | 0x31 | 1 | 字符1 |
| 0011 0010 | 062 | 50 | 0x32 | 2 | 字符2 |
| 0011 0011 | 063 | 51 | 0x33 | 3 | 字符3 |
| 0011 0100 | 064 | 52 | 0x34 | 4 | 字符4 |
| 0011 0101 | 065 | 53 | 0x35 | 5 | 字符5 |
| 0011 0110 | 066 | 54 | 0x36 | 6 | 字符6 |
| 0011 0111 | 067 | 55 | 0x37 | 7 | 字符7 |
| 0011 1000 | 070 | 56 | 0x38 | 8 | 字符8 |
| 0011 1001 | 071 | 57 | 0x39 | 9 | 字符9 |
| 0011 1010 | 072 | 58 | 0x3A | : | 冒号 |
| 0011 1011 | 073 | 59 | 0x3B | ; | 分号 |
| 0011 1100 | 074 | 60 | 0x3C | < | 小于 |
| 0011 1101 | 075 | 61 | 0x3D | = | 等号 |
| 0011 1110 | 076 | 62 | 0x3E | > | 大于 |
| 0011 1111 | 077 | 63 | 0x3F | ? | 问号 |
| 0100 0000 | 0100 | 64 | 0x40 | @ | 电子邮件符号 |
| 0100 0001 | 0101 | 65 | 0x41 | A | 大写字母A |
| 0100 0010 | 0102 | 66 | 0x42 | B | 大写字母B |
| 0100 0011 | 0103 | 67 | 0x43 | C | 大写字母C |
| 0100 0100 | 0104 | 68 | 0x44 | D | 大写字母D |
| 0100 0101 | 0105 | 69 | 0x45 | E | 大写字母E |
| 0100 0110 | 0106 | 70 | 0x46 | F | 大写字母F |
| 0100 0111 | 0107 | 71 | 0x47 | G | 大写字母G |
| 0100 1000 | 0110 | 72 | 0x48 | H | 大写字母H |
| 0100 1001 | 0111 | 73 | 0x49 | I | 大写字母I |
| 01001010 | 0112 | 74 | 0x4A | J | 大写字母J |
| 0100 1011 | 0113 | 75 | 0x4B | K | 大写字母K |
| 0100 1100 | 0114 | 76 | 0x4C | L | 大写字母L |
| 0100 1101 | 0115 | 77 | 0x4D | M | 大写字母M |
| 0100 1110 | 0116 | 78 | 0x4E | N | 大写字母N |
| 0100 1111 | 0117 | 79 | 0x4F | O | 大写字母O |
| 0101 0000 | 0120 | 80 | 0x50 | P | 大写字母P |
| 0101 0001 | 0121 | 81 | 0x51 | Q | 大写字母Q |
| 0101 0010 | 0122 | 82 | 0x52 | R | 大写字母R |
| 0101 0011 | 0123 | 83 | 0x53 | S | 大写字母S |
| 0101 0100 | 0124 | 84 | 0x54 | T | 大写字母T |
| 0101 0101 | 0125 | 85 | 0x55 | U | 大写字母U |
| 0101 0110 | 0126 | 86 | 0x56 | V | 大写字母V |
| 0101 0111 | 0127 | 87 | 0x57 | W | 大写字母W |
| 0101 1000 | 0130 | 88 | 0x58 | X | 大写字母X |
| 0101 1001 | 0131 | 89 | 0x59 | Y | 大写字母Y |
| 0101 1010 | 0132 | 90 | 0x5A | Z | 大写字母Z |
| 0101 1011 | 0133 | 91 | 0x5B | [ | 开方括号 |
| 0101 1100 | 0134 | 92 | 0x5C | \ | 反斜杠 |
| 0101 1101 | 0135 | 93 | 0x5D | ] | 闭方括号 |
| 0101 1110 | 0136 | 94 | 0x5E | ^ | 脱字符 |
| 0101 1111 | 0137 | 95 | 0x5F | _ | 下划线 |
| 0110 0000 | 0140 | 96 | 0x60 | ` | 开单引号 |
| 0110 0001 | 0141 | 97 | 0x61 | a | 小写字母a |
| 0110 0010 | 0142 | 98 | 0x62 | b | 小写字母b |
| 0110 0011 | 0143 | 99 | 0x63 | c | 小写字母c |
| 0110 0100 | 0144 | 100 | 0x64 | d | 小写字母d |
| 0110 0101 | 0145 | 101 | 0x65 | e | 小写字母e |
| 0110 0110 | 0146 | 102 | 0x66 | f | 小写字母f |
| 0110 0111 | 0147 | 103 | 0x67 | g | 小写字母g |
| 0110 1000 | 0150 | 104 | 0x68 | h | 小写字母h |
| 0110 1001 | 0151 | 105 | 0x69 | i | 小写字母i |
| 0110 1010 | 0152 | 106 | 0x6A | j | 小写字母j |
| 0110 1011 | 0153 | 107 | 0x6B | k | 小写字母k |
| 0110 1100 | 0154 | 108 | 0x6C | l | 小写字母l |
| 0110 1101 | 0155 | 109 | 0x6D | m | 小写字母m |
| 0110 1110 | 0156 | 110 | 0x6E | n | 小写字母n |
| 0110 1111 | 0157 | 111 | 0x6F | o | 小写字母o |
| 0111 0000 | 0160 | 112 | 0x70 | p | 小写字母p |
| 0111 0001 | 0161 | 113 | 0x71 | q | 小写字母q |
| 0111 0010 | 0162 | 114 | 0x72 | r | 小写字母r |
| 0111 0011 | 0163 | 115 | 0x73 | s | 小写字母s |
| 0111 0100 | 0164 | 116 | 0x74 | t | 小写字母t |
| 0111 0101 | 0165 | 117 | 0x75 | u | 小写字母u |
| 0111 0110 | 0166 | 118 | 0x76 | v | 小写字母v |
| 0111 0111 | 0167 | 119 | 0x77 | w | 小写字母w |
| 0111 1000 | 0170 | 120 | 0x78 | x | 小写字母x |
| 0111 1001 | 0171 | 121 | 0x79 | y | 小写字母y |
| 0111 1010 | 0172 | 122 | 0x7A | z | 小写字母z |
| 0111 1011 | 0173 | 123 | 0x7B | { | 开花括号 |
| 0111 1100 | 0174 | 124 | 0x7C | | | 垂线 |
| 0111 1101 | 0175 | 125 | 0x7D | } | 闭花括号 |
| 0111 1110 | 0176 | 126 | 0x7E | ~ | 波浪号 |
| 0111 1111 | 0177 | 127 | 0x7F | DEL (delete) | 删除 |

# easyre

直接拖进IDA中F5就出flag

# reverse1

搜索flag发现相应的代码

```plaintext
int __cdecl main_0(int argc, const char **argv, const char **envp)
{
  char *v3; // rdi
  __int64 i; // rcx
  size_t v5; // rax
  char v7; // [rsp+0h] [rbp-20h] BYREF
  int j; // [rsp+24h] [rbp+4h]
  char Str1[224]; // [rsp+48h] [rbp+28h] BYREF
  __int64 v10; // [rsp+128h] [rbp+108h]

  v3 = &v7;
  for ( i = 82i64; i; --i )
  {
    *(_DWORD *)v3 = -858993460;
    v3 += 4;
  }
  for ( j = 0; ; ++j )
  {
    v10 = j;
    if ( j > j_strlen(Str2) )
      break;
    if ( Str2[j] == 'o' )
      Str2[j] = '0';
  }
  sub_1400111D1("input the flag:");
  sub_14001128F("%20s", Str1);
  v5 = j_strlen(Str2);
  if ( !strncmp(Str1, Str2, v5) )
    sub_1400111D1("this is the right flag!\n");
  else
    sub_1400111D1("wrong flag\n");
  return 0;
}
```

可以看到Str1与 Str2进行了比较，直接找Str2,发现flag,但是上面的for循环把o变成了0

# reverse2

拖进IDA中F5：

```plaintext
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int stat_loc; // [rsp+4h] [rbp-3Ch] BYREF
  int i; // [rsp+8h] [rbp-38h]
  __pid_t pid; // [rsp+Ch] [rbp-34h]
  char s2[24]; // [rsp+10h] [rbp-30h] BYREF
  unsigned __int64 v8; // [rsp+28h] [rbp-18h]

  v8 = __readfsqword(0x28u);
  pid = fork();
  if ( pid )
  {
    waitpid(pid, &stat_loc, 0);
  }
  else
  {
    for ( i = 0; i <= strlen(&flag); ++i )
    {
      if ( *(&flag + i) == 105 || *(&flag + i) == 114 )
        *(&flag + i) = 49;
    }
  }
  printf("input the flag:");
  __isoc99_scanf("%20s", s2);
  if ( !strcmp(&flag, s2) )
    return puts("this is the right flag!");
  else
    return puts("wrong flag!");
}
```

找到flag的位置，发现flag但是上面的程序将i，r变为了1

# 内涵的软件

拖入OD，发现可疑的字符，dbapp{49d3c93df25caad81232130f3d2ebfad}，将其改为flag

# 新年快乐

先拖进IDA发现文件有点异常，怀疑是加壳了，然后进行脱壳工作。

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230827184532.png)

脱完壳之后得到以下程序：

```plaintext
int __cdecl main(int argc, const char **argv, const char **envp)
{
  char Str2[14]; // [esp+12h] [ebp-3Ah] BYREF
  char Str1[44]; // [esp+20h] [ebp-2Ch] BYREF

  sub_401910();
  strcpy(Str2, "HappyNewYear!");
  *(_WORD *)Str1 = word_40306B;
  memset(&Str1[2], 0, 0x1Eu);
  printf("please input the true flag:");
  scanf("%s", Str1);
  if ( !strncmp(Str1, Str2, strlen(Str2)) )
    return puts(aThisIsTrueFlag);
  else
    return puts(Buffer);
}
```

就是一个比较字符串的程序，得到flag：flag{HappyNewYear!}

# xor

```plaintext
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int i; // [rsp+2Ch] [rbp-124h]
  char __b[264]; // [rsp+40h] [rbp-110h] BYREF

  memset(__b, 0, 0x100uLL);
  printf("Input your flag:\n");
  get_line(__b, 256LL);
  if ( strlen(__b) != 33 )
    goto LABEL_7;
  for ( i = 1; i < 33; ++i )
    __b[i] ^= __b[i - 1];
  if ( !strncmp(__b, global, 0x21uLL) )
    printf("Success");
  else
LABEL_7:
    printf("Failed");
  return 0;
}
```

代码逻辑比较简单，就是后一项与前一项异或。

直接跟进global，然后保存16进制的数据，然后编写逆向脚本即可。

```plaintext
a= [0x66, 0x0A, 0x6B, 0x0C, 0x77, 0x26,
    0x4F, 0x2E, 0x40, 0x11, 0x78, 0x0D,
    0x5A, 0x3B, 0x55, 0x11, 
    0x70, 0x19, 0x46, 0x1F, 0x76, 0x22,
    0x4D, 0x23, 0x44, 0x0E, 0x67,
    0x06, 0x68, 0x0F, 0x47, 0x32,0x4F]

flag = "f"
for i in range(1,len(a)):
    flag += chr(a[i] ^ a[i-1])
print(flag)
```

# helloword

发现是个安卓文件，拖进JADX中查找flag即可得到flag

![](https://xyhutc.oss-cn-qingdao.aliyuncs.com/giteetuchuang/20230827192257.png)

# reverse3

```plaintext
int __cdecl main_0(int argc, const char **argv, const char **envp)
{
  size_t v3; // eax
  const char *v4; // eax
  size_t v5; // eax
  char v7; // [esp+0h] [ebp-188h]
  char v8; // [esp+0h] [ebp-188h]
  signed int j; // [esp+DCh] [ebp-ACh]
  int i; // [esp+E8h] [ebp-A0h]
  signed int v11; // [esp+E8h] [ebp-A0h]
  char Destination[108]; // [esp+F4h] [ebp-94h] BYREF
  char Str[28]; // [esp+160h] [ebp-28h] BYREF
  char v14[8]; // [esp+17Ch] [ebp-Ch] BYREF

  for ( i = 0; i < 100; ++i )
  {
    if ( (unsigned int)i >= 0x64 )
      j____report_rangecheckfailure();
    Destination[i] = 0;
  }
  sub_41132F("please enter the flag:", v7);
  sub_411375("%20s", (char)Str);
  v3 = j_strlen(Str);
  v4 = (const char *)sub_4110BE(Str, v3, v14);      // 跟进此函数发现是个base64的编码函数
  strncpy(Destination, v4, 0x28u);
  v11 = j_strlen(Destination);
  for ( j = 0; j < v11; ++j )
    Destination[j] += j;                           // 主要就是逆向这个！
  v5 = j_strlen(Destination);
  if ( !strncmp(Destination, Str2, v5) )
    sub_41132F("rigth flag!\n", v8);
  else
    sub_41132F("wrong flag!\n", v8);
  return 0;
}
```

就是将输入的字符串base64加密之后再每一位减对应的数，编写逆向代码：

```plaintext
import base64
data = "e3nifIH9b_C@n@dH"
flag = ""
for i in range(len(data)):
    flag += chr(ord(data[i]) - i)
print(base64.b64decode(flag))
```

# 不一样的flag

```plaintext
int __cdecl __noreturn main(int argc, const char **argv, const char **envp)
{
  _BYTE v3[29]; // [esp+17h] [ebp-35h] BYREF
  int v4; // [esp+34h] [ebp-18h]
  int v5; // [esp+38h] [ebp-14h] BYREF
  int i; // [esp+3Ch] [ebp-10h]
  _BYTE v7[12]; // [esp+40h] [ebp-Ch] BYREF

  __main();
  v3[26] = 0;
  *(_WORD *)&v3[27] = 0;
  v4 = 0;
  strcpy(v3, "*11110100001010000101111#");   // 赋值操作
  while ( 1 )
  {
    puts("you can choose one action to execute");
    puts("1 up");
    puts("2 down");
    puts("3 left");
    printf("4 right\n:");
    scanf("%d", &v5);
    if ( v5 == 2 )
    {
      ++*(_DWORD *)&v3[25];
    }
    else if ( v5 > 2 )
    {
      if ( v5 == 3 )
      {
        --v4;
      }
      else
      {
        if ( v5 != 4 )
LABEL_13:
          exit(1);
        ++v4;
      }
    }
    else
    {
      if ( v5 != 1 )
        goto LABEL_13;
      --*(_DWORD *)&v3[25];
    }
    for ( i = 0; i <= 1; ++i )
    {
      if ( *(_DWORD *)&v3[4 * i + 25] >= 5u )
        exit(1);
    }
    if ( v7[5 * *(_DWORD *)&v3[25] - 41 + v4] == 49 )   // 1  关键代码
      exit(1);
    if ( v7[5 * *(_DWORD *)&v3[25] - 41 + v4] == 35 )   // #  输入的顺序就是flag
    {
      puts("\nok, the order you enter is the flag!");
      exit(0);
    }
  }
}
```

对可疑的字符串进行分析，发现这串字符可以分为5行5列：

```plaintext
*1111
01000
01010
00010
1111#
```

发现他有0和1，和#  
 这个应该就是迷宫了  
 要求我们从*走到#就  
 不走向1，否则退出

所以我们的顺序是：222441144222

所以得到flag:  
 flag{222441144222}

# SimpleRev

以下是main函数

```plaintext
int __cdecl __noreturn main(int argc, const char **argv, const char **envp)
{
  int v3; // eax
  char v4; // [rsp+Fh] [rbp-1h]

  while ( 1 )
  {
    while ( 1 )
    {
      printf("Welcome to CTF game!\nPlease input d/D to start or input q/Q to quit this program: ");
      v4 = getchar();
      if ( v4 != 100 && v4 != 68 )
        break;
      Decry("Welcome to CTF game!\nPlease input d/D to start or input q/Q to quit this program: ", argv);
    }
    if ( v4 == 113 || v4 == 81 )
      Exit("Welcome to CTF game!\nPlease input d/D to start or input q/Q to quit this program: ", argv);
    puts("Input fault format!");
    v3 = getchar();
    putchar(v3);
  }
}
```

主要的函数是以下函数

```plaintext
unsigned __int64 Decry()
{
  char v1; // [rsp+Fh] [rbp-51h]
  int v2; // [rsp+10h] [rbp-50h]
  int v3; // [rsp+14h] [rbp-4Ch]
  int i; // [rsp+18h] [rbp-48h]
  int v5; // [rsp+1Ch] [rbp-44h]
  char src[8]; // [rsp+20h] [rbp-40h] BYREF
  __int64 v7; // [rsp+28h] [rbp-38h]
  int v8; // [rsp+30h] [rbp-30h]
  __int64 v9[2]; // [rsp+40h] [rbp-20h] BYREF
  int v10; // [rsp+50h] [rbp-10h]
  unsigned __int64 v11; // [rsp+58h] [rbp-8h]

  v11 = __readfsqword(0x28u);
  *(_QWORD *)src = 'SLCDN';
  v7 = 0LL;
  v8 = 0;
  v9[0] = 'wodah';
  v9[1] = 0LL;
  v10 = 0;
  text = (char *)join(key3, v9);
  strcpy(key, key1);
  strcat(key, src);
  v2 = 0;
  v3 = 0;
  getchar();
  v5 = strlen(key);
  for ( i = 0; i < v5; ++i )
  {
    if ( key[v3 % v5] > '@' && key[v3 % v5] <= 'Z' )
      key[i] = key[v3 % v5] + 32;
    ++v3;
  }
  printf("Please input your flag:");
  while ( 1 )
  {
    v1 = getchar();
    if ( v1 == '\n' )
      break;
    if ( v1 == ' ' )
    {
      ++v2;
    }
    else
    {
      if ( v1 <= 96 || v1 > 122 )   // 主要函数
      {
        if ( v1 > 64 && v1 <= 90 )
        {
          str2[v2] = (v1 - '\'' - key[v3 % v5] + 'a') % 26 + 97;
          ++v3;
        }
      }
      else
      {
        str2[v2] = (v1 - 39 - key[v3 % v5] + 97) % 26 + 97;
        ++v3;
      }
      if ( !(v3 % v5) )
        putchar(32);
      ++v2;
    }
  }
  if ( !strcmp(text, str2) )
    puts("Congratulation!\n");
  else
    puts("Try again!\n");
  return __readfsqword(0x28u) ^ v11;
}
```

编写攻击脚本：

```c
#include<stdio.h> 
int main()
{
	char key[] = "adsfkndcls";
	char text[] = "killshadow";
	int i;
	int v3=10;//长度 
	for (int i = 0; i < 10; i++)
	{
		for (int j = 0; j < 128; j++)
		{
			if (j < 'A' || j > 'z' || j > 'Z' && j < 'a')
			{
				continue;
			}
			if ((j - 39 - key[v3 % 10] + 97) % 26 + 97 == text[i])
			{
				printf("%c",j);
				v3++;
				break;
			}
		}
	}
}
```

# Java逆向解密

```java
package p000;

import java.util.ArrayList;
import java.util.Scanner;

/* renamed from: Reverse */
/* loaded from: Reverse.class */
public class Reverse {
    public static void main(String[] args) {
        Scanner s = new Scanner(System.in);
        System.out.println("Please input the flag ：");
        String str = s.next();
        System.out.println("Your input is ：");
        System.out.println(str);
        char[] stringArr = str.toCharArray();
        Encrypt(stringArr);
    }

    public static void Encrypt(char[] arr) {
        ArrayList<Integer> Resultlist = new ArrayList<>();
        for (char c : arr) {
            int result = (c + '@') ^ 32;
            Resultlist.add(Integer.valueOf(result));
        }
        int[] KEY = {180, 136, 137, 147, 191, 137, 147, 191, 148, 136, 133, 191, 134, 140, 129, 135, 191, 65};
        ArrayList<Integer> KEYList = new ArrayList<>();
        for (int i : KEY) {
            KEYList.add(Integer.valueOf(i));
        }
        System.out.println("Result:");
        if (Resultlist.equals(KEYList)) {
            System.out.println("Congratulations！");
        } else {
            System.err.println("Error！");
        }
    }
}
```

主要就是对传入的字符串进行加后再进行相与

编写逆向脚本可得：

```python
list = [180, 136, 137, 147, 191, 137, 147, 191, 148, 136, 133, 191, 134, 140, 129, 135, 191, 65]

flag = ""

for i in range (len(list)):
    flag += chr(list[i] - ord('@') ^ 0x20)
    
print(flag)
```

# luck_guy

```plaintext
unsigned __int64 get_flag()
{
  unsigned int v0; // eax
  int i; // [rsp+4h] [rbp-3Ch]
  int j; // [rsp+8h] [rbp-38h]
  __int64 s; // [rsp+10h] [rbp-30h] BYREF
  char v5; // [rsp+18h] [rbp-28h]
  unsigned __int64 v6; // [rsp+38h] [rbp-8h]

  v6 = __readfsqword(0x28u);
  v0 = time(0LL);
  srand(v0);
  for ( i = 0; i <= 4; ++i )
  {
    switch ( rand() % 200 )
    {
      case 1:
        puts("OK, it's flag:");
        memset(&s, 0, 0x28uLL);
        strcat((char *)&s, f1);
        strcat((char *)&s, &f2);
        printf("%s", (const char *)&s);
        break;
      case 2:
        printf("Solar not like you");
        break;
      case 3:
        printf("Solar want a girlfriend");
        break;
      case 4:
        s = 0x7F666F6067756369LL;
        v5 = 0;
        strcat(&f2, (const char *)&s);
        break;
      case 5:
        for ( j = 0; j <= 7; ++j )
        {
          if ( j % 2 == 1 )
            *(&f2 + j) -= 2;
          else
            --*(&f2 + j);
        }
        break;
      default:
        puts("emmm,you can't find flag 23333");
        break;
    }
  }
  return __readfsqword(0x28u) ^ v6;
}
```
