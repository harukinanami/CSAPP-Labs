实验报告lab1 曾浩然 10235101562

DATALAB

1. **bitAnd**

本题要求算出x&y而只能用~符号和|符号，联想到德摩根律即可解出

```cpp
int bitAnd(int x, int y) { 
    return ~(~x|~y);
}
```

2. **getByte**

	本题要求获取x的n个字节,要取多少个字节就是将原数向右移8*n个单位，之后用0xff与其作and运算即可完成运算

```cpp
int getByte(int x, int n) {
    return (x >> (n << 3)) & 0xFF;
}
```

3. **logicalShift**
	本题要求实现逻辑右移，首先要区分逻辑右移和算术右移。算数右移是补符号位，而逻辑右移则是补0，所以实际上就是将右移位全部置零。
```cpp
int logicalShift(int x, int n) {
    return (x >> n) & ~ (((1 << 31) >> n) <<1 );
}
```
4. **bitCount**

	本题要求计算1的个数，可以转换为计算x的二进制串的所有位的和，这道题我自己没有想到
    很好的处理方法，故是看网上采用了分而治之的方法，首先将int型数据x的32位分成16组，并进行X31+X30，X29+X28，…，X3+X2，X1+X0的运算；然后将x分成8组，并进行X31+X30+X29+X28，…，X3+X2+X1+X0的运算。依次类推，接着将x分成4组，2组并进行相应的运算。最后只剩下1组，此时将所有的位进行相加即得到了最终结果。
```cpp
int bitCount(int x) {
  int mask1,mask2,mask4,mask8,mask16;
  mask1 = 0x55 | 0x55 << 8;
  mask1 = mask1 | mask1 << 16;
  mask2 = 0x33 | 0x33 << 8;
  mask2 = mask2 | mask2 << 16; 
  mask4 = 0x0F | 0x0F << 8;
  mask4 = mask4 | mask4 << 16;
  mask8 = 0xFF | 0xFF << 16;
  mask16 = 0xFF | 0xFF << 8;
  x = (x & mask1) + ((x >> 1) &mask1);
  x = (x & mask2) + ((x >> 2) &mask2);
  x = (x + (x >> 4)) & mask4;
  x = (x + (x >> 8)) & mask8;
  x = (x+ (x >> 16)) & mask16;
  return x;
}
```
5. **bang**

本题要求不用！而做到！符的作用，可以采用与相反数作|操作即可

```cpp
int bang(int x) { 
    return (((~ x + 1)|x) >> 31) + 1; 
}
```
6. **tmin**

	没什么可说的，直接左移31位
```cpp
int tmin(void) {
     return 1 << 31; 
}
```
7. **fitsBits**

	本题是说如果int型数据x可以表示为n位二进制补码整数（其中1 <= n <= 32）,则返回1，否则返回0。因为n位二进制能表示的最大整数为最高位为0，其他位为1；最小数为最高位为1，其他位为0。所以我们可以将x右移n-1位（n+(~0)）后，再将其与符号位比较，两者相同说明x可以表示位n位二进制数。

```cpp
int fitsBits(int x, int n) {
    int y,z,t;
	y=x>>31;
	z=x>>(n+(~0));
	t=!(y^z);
	return t;
}
```
8. **divpwr2**

	本题要求进行除法运算，对于非负数是默认向0取整；但对负数来说，需要字啊移位之前加一个偏置量处理一下。
```cpp
int divpwr2(int x, int n) {
  return (x+(((x>>31)&1)<<n)+(~0)+(!((x>>31)&1)))>>n;
}
```
9. **negate** 
	
    x相反数就是x取反+1
```cpp
int negate(int x) {
  return ~x+1;
}
```
10. **isPositive**

	容易发现
    ```
    当x>0时，isPositive(x)=1，flag(x)=0，!x=0；
    当x<0时，isPositive(x)=0，flag(x)=1，!x=0；
    当x=0时，isPositive(x)=0，flag(x)=0，!x=1；
    ```
    所以可以将flag和!x进行 | 运算，然后将得到的结果取 !
```cpp
int isPositive(int x) { 
    return !((x >> 31 & 1) | !x); 
    }
```
11. **isLessOrEqual**
	
    和10题类似，先求差值即可
```cpp
    int isLessOrEqual(int x, int y) {
  	int m = 0, n = 0, r = 0, result = 0,t = 0;
	m = (x >> 31) & 0x1;
	n = (y >> 31) & 0x1;
	r = !(m ^ n);
	result = x + (~y) + 1;
	t=(r & ((result >> 31) | (!result))) | ((!r) & m);
	return t;
}

```
12. **ilog2**

本题要求log2x

用公式
```
log2(x)=16×a+8×b+4×c+2×d+1×e
```

字母值是右移n位后判断是否为零的真值
```cpp
int ilog2(int x) {
  int ans = 0;
  ans = ans + ((!!(x>>(16 + ans)))<<4);
  ans = ans + ((!!(x>>(8 + ans)))<<3);
  ans = ans + ((!!(x>>(4 + ans)))<<2);
  ans = ans + ((!!(x>>(2 + ans)))<<1);
  ans = ans + ((!!(x>>(1 + ans)))<<0);

  return ans;
}
```
13. **float_neg**
	
    本题是返回浮点参数f的表达式-f的位等效项。注意特殊情况即可（NaN）
```cpp
unsigned float_neg(unsigned uf) {
  unsigned tmp = uf&(0x7fffffff);
  unsigned result = uf^(0x80000000);
  if(tmp>0x7f800000) result=uf;
  return result; 
}
```

14. **float_i2f** 
	本题要求返回表达式（浮点数）x的等价位。浮点数由符号位，阶码和小数部分组成。特别注意的是舍入部分：向偶数舍入。用`(f > 128) || ((f == 128) && (tail & 1))` 来表示（f是被截掉的部分）。（本人没有想到解决方法，查阅互联网后才解决）
```cpp

unsigned float_i2f(int x) {
    unsigned ans;
    int tmpx = x;
    int f = 0;
    int delta = 0;
    int tail = 0;
    int E = 0;
	//特殊情况
    if (x == 0)
        return x;
    if (x == 0x80000000)
        return 0xcf000000;
//符号位
    ans = x & 0x80000000;
//取绝对值
    if (ans)
        tmpx = -x;
//计算移位数
    while ((tmpx >> E))
        E++;
    E = E - 1;
//移位
    tmpx = tmpx << (31 - E);
//尾数截掉八位
    tail = (tmpx >> 8) & 0x007FFFFF;
//被截掉部分
    f = tmpx & 0xff;
//向上舍入的情况
    delta = (f > 128) || ((f == 128) && (tail & 1));
    tail += delta;

//阶码计算
    E = E + 127;
//溢出判断
    if (tail >> 23) {
        tail = tail & 0x007FFFFF;
        E += 1;
    }
    ans = ans | E << 23 | tail;
    return ans;
}
```
15. **float_twice**

与14题类似
```cpp

unsigned float_twice(unsigned uf) {
    unsigned sign = uf >> 31;
    unsigned exp = uf >> 23 & 0xFF;
    unsigned frac = uf & 0x7FFFFF;

    if (exp == 0 && frac < 0x7FFFFF)
        frac <<= 1;
    else if (exp == 0 && frac == 0x7FFFFF) {
        exp += 1;
        frac = 0x7FFFFE;
    } //非规格化
    else if (exp == 0xFE) {
        exp = 0xFF;
        frac = 0;
    } else if (exp == 0xFF)
        return uf;
    else
        exp += 1;
    return (sign << 31) | (exp << 23) | frac;
}
```
