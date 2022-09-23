# csapp_lab
data lab

写在前面
make命令编译文件
btest程序测试程序实现的正确性，
dlc测试程序使用的操作符的个数和正确性
如 测试bitXor 
./make
./btest -f bitXor
./dlc -e bit.c

第一题异或的实现利用的德摩根定理
~（a + b) = ~a &  ~b,~(a & b) = ~a + ~b,~ ~(a+b) = a+b,~ ~(a&b) = a&b
itXor - x^y using only ~ and & 
 *   Example: bitXor(4, 5) = 1
 *   Legal ops: ~ &
 *   Max ops: 14
 *   Rating: 1
 */
int bitXor(int x, int y) {
  return ~(x&y)&~(~x&~y);
}

第二题是补码的知识，最高位即使符号位也是数字位，多提一句补码可以看成一个圆
/* 
 * tmin - return minimum two's complement integer 
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 4
 *   Rating: 1
 */
int tmin(void) {
  return (1 << 31);
}

第三题是利用到Tmax 0x7fffffff +1 后0x800000000 的补码与自己相同，而ox0也存在这个特性，
所以contion1是自己与自己补码相同的判断，contion2是判断是不是0，其中利用规格化的方法
！！x = x！=0？ 1：0；
/*
 * isTmax - returns 1 if x is the maximum, two's complement number,
 *     and 0 otherwise 
 *   Legal ops: ! ~ & ^ | +
 *   Max ops: 10  
 *   Rating: 1
 */   
int isTmax(int x) {
  int mask1,mask2;
  mask1 = x+1;
  mask2 = ~mask1 +1 ;
  return ((!(mask1^mask2)) & !!(mask1));
}
/* 
 * allOddBits - return 1 if all odd-numbered bits in word set to 1
 *   where bits are numbered from 0 (least significant) to 31 (most significant)
 *   Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 2
 */
 
 第四题是构建了一个mask（掩码），还有一点利用！（x^y) 来判断x与y是否相等。
int allOddBits(int x) {
  int mask = (0xaa << 24) | (0xaa << 16) |(0xaa << 8) | 0xaa ;
  return !((x&mask)^mask);
}

 第五题取负就是取反加一，这题也可以用补码是一个圆来解释，补码就是原码分一半来表示负数，而因为0的存在，所以|Tmin| = Tmax + 1，所以取负就是取反+1.
/* 
 * negate - return -x 
 *   Example: negate(1) = -1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 5
 *   Rating: 2
 */
int negate(int x) {
  return (~x + 1);
}

第六题根据0x30 ~ 0x39的二进制 分成俩类，0000 0000 0000 0000 0000 0000 0011 0011 0x30
                                       0000 0000 0000 0000 0000 0000 0011 0100 0x31
                                       ...
                                       0000 0000 0000 0000 0000 0000 0011 0111 0x37
                                       0000 0000 0000 0000 0000 0000 0011 0xxx 0x30  - 0x37
                                       
                                       0000 0000 0000 0000 0000 0000 0011 1000 0x38
                                       0000 0000 0000 0000 0000 0000 0011 1001 0x39
                                       0000 0000 0000 0000 0000 0000 0011 100x 0x38 - 0x39
 mask1就是第一种，mask2就是第二种
/* 
 * isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')
 *   Example: isAsciiDigit(0x35) = 1.
 *            isAsciiDigit(0x3a) = 0.
 *            isAsciiDigit(0x05) = 0.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 15
 *   Rating: 3
 */
int isAsciiDigit(int x) { 
  int mask1 = !((x >> 3)^0x6);
  int mask2 = !((x >> 1)^0x1c);
  return mask1 | mask2;
}

第七题 就是利用规格化的然后利用0的补码是全0，1的补码是全1
！！x = x！=0？ 1：0；（规格化）
/* 
 * conditional - same as x ? y : z 
 *   Example: conditional(2,4,5) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 16
 *   Rating: 3
 */
int conditional(int x, int y, int z) {
  x = !!x;
  x = ~x + 1;
  return (x & y) | (~x & z);
}

第八题 分为俩种情况，同号与异号 
异号可以直接根据x的符号判断，同号更简单直接减法求符号。
/* 
 * isLessOrEqual - if x <= y  then return 1, else return 0 
 *   Example: isLessOrEqual(4,5) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3
 */
int isLessOrEqual(int x, int y) {
  int signx = !!(x >> 31);
  int signy = y >> 31;
  int mask = signx ^ signy;
  return ((mask&signx) | ((~mask) & !((y + (~ x + 1 )) >> 31)));
}

第九题 我的做法跟第三题一样，只不过mask2判断条件变成了不是0x7fffffff
/* 
 * logicalNeg - implement the ! operator, using all of 
 *              the legal operators except !
 *   Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
 *   Legal ops: ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 4 
 */
int logicalNeg(int x) {
  int mask1 = (~((x ^ (~x + 1)) >> 31)) & 0x1;
  int mask2 = ~((x >> 31) & 1);
  return mask1&mask2;
}

第十题 依然是分俩种情况，正数和负数。要表示12       8 4 2 1
                                                0  1 1 0 0
要5位因为前面还要加一位符号数表示正数，所以位数是找第一个1的位数在+1
要表示负数-5                                      -8 4 2 1
                                                  1  0 1 1
而负数的位数就是第一个0的位数+1，而负数不会出现连续的1在开头，因为连续的1可以表示成1个1  -32 16 8 4 2 1 =   -16 8 4 2 1  = -8 4 2 1        
                                                                   1 1  1 0 0 1      1  1  0 0 1    1 0 0 1 = -7
所以我们把负数取反，这样负数和正数的规则就统一了，就是找最高位的1 + 1位。

而具体怎么找就采用二分法
32位
16 16
    8 8
      4 4
        2 2
          1
bit16=前16位是否有1？ 16：0；
x再右移bit16位（因为如果前16位有1，后16位就没用了，x就可以右移16，如果前16位没1，后16位就用了，x就可以右移0，
                                                             |                                         |
                                                             -------------------------------------------- bit16
8位同理
/* howManyBits - return the minimum number of bits required to represent x in
 *             two's complement
 *  Examples: howManyBits(12) = 5
 *            howManyBits(298) = 10
 *            howManyBits(-5) = 4
 *            howManyBits(0)  = 1
 *            howManyBits(-1) = 1
 *            howManyBits(0x80000000) = 32
 *  Legal ops: ! ~ & ^ | + << >>
 *  Max ops: 90
 *  Rating: 4
 */
int howManyBits(int x) {
  int flag;
  int bit16,bit8,bit4,bit2,bit1;
  flag = !!(x >> 31); 
  flag = ~flag + 1;
  x = ((~flag&x) | (flag&(~x)));

  
  bit16 = !!(x >> 16) << 4;
  x = x >> bit16;
  bit8 = !!(x >> 8) << 3;
  x = x >> bit8;
  bit4 = !!(x >> 4) << 2;
  x = x >> bit4;
  bit2 = !!(x >> 2) << 1;
  x = x >> bit2;
  bit1 = !!(x >> 1);
  x = x >> bit1;
  return bit16 + bit8 + bit4 + bit2 + bit1 + x + 1;
}
//float

这里要说以下float的表示
32位分为
1 8 23
s e f 

采取科学技术法
  101.1111111 
->   | 
  1.011111111
   | |------|
   |    f（小数部分）
     |
     
   |-|
    e表示小数点的浮动位数   
s表示符号0为正
0

再根据e，分为4种
规格化
s e
  ！= 255 & ！= 0
 val = -1^s * 1.f * 2^(e - bias)
非规格化
 s e
   ==0
val = 0.f * 2^(1-bias)
无穷
s e      f
  ==255  ==0
 NAN
s e      f
  =255   ！=0
 第十一题 算2*f
 算出s，e，f，再然后分类讨论
 首先NAN和无穷return uf；
 非规划数直接把f左移1位，不用关阶码因为只有俩种可能，一是2f超过1，阶码是1，二是阶码是0。f左移全都包含进去了
 规格化数只要把阶码加1
/* 
 * floatScale2 - Return bit-level equivalent of expression 2*f for
 *   floating point argument f.
 *   Both the argument and result are passed as unsigned int's, but
 *   they are to be interpreted as the bit-level representation of
 *   single-precision floating point values.
 *   When argument is NaN, return argument
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 * 
 */
unsigned floatScale2(unsigned uf) {
  int s = uf >> 31 & 1;
  unsigned e = (uf >> 23) & 0xff;
  unsigned f = uf ^ (s << 31) ^ (e << 23);
 if(!(e ^ 0xff)){
		return uf;
	}
	if(!e)
	{
		return ((s<<31)|(f << 1));
	}
	return ((s << 31) | ((e+1)<<23) | f);
}

第十二题 float-> int 还是照旧得出s，e，f，再分类
首先NAN和无穷return Tmin
非规格化数（太小了）return 0
规格化再分成减去bias后小于0          1.f * 2 ^ -x return 0
                      大于0 <=23    说明要截断f
                      <23           return Tmin
最后根据s位分正负
/* 
 * floatFloat2Int - Return bit-level equivalent of expression (int) f
 *   for floating point argument f.
 *   Argument is passed as unsigned int, but
 *   it is to be interpreted as the bit-level representation of a
 *   single-precision floating point value.
 *   Anything out of range (including NaN and infinity) should return
 *   0x80000000u.
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
int floatFloat2Int(unsigned uf) {
  int s = uf >> 31 & 1;
  int e = (uf >> 23) & 0xff;
  int f = uf ^ (s << 31) ^ (e << 23);

  if(!(e ^ 0xff))
  {
    return 0x80000000u;
  }
  else if(e == 0)
  {
    return 0;
  }
  else if ((e - 0x7f) < 0)
  {
    return 0;
  }
  else if ((e - 0x7f) > 23)
  {
    return 0x80000000u;
  }
  f = ((1 << 23) | f) >> (23 - (e -127));
  if (!s)
  {
    return f;
  }
  return -f;
}

第十三题 2^x -> float
首先x作为阶码加上bias
 x < 0  return 0
 x > 255 return Tmax
 0<x <=255 直接把x当成阶码就行
/* 
 * floatPower2 - Return bit-level equivalent of the expression 2.0^x
 *   (2.0 raised to the power x) for any 32-bit integer x.
 *
 *   The unsigned value that is returned should have the identical bit
 *   representation as the single-precision floating-point number 2.0^x.
 *   If the result is too small to be represented as a denorm, return
 *   0. If too large, return +INF.
 * 
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. Also if, while 
 *   Max ops: 30 
 *   Rating: 4
 */
unsigned floatPower2(int x) {
    x  = x + 127;
    if(x < 0) return 0;
    return x <= 255? x << 23 : 0x7f800000;
}
