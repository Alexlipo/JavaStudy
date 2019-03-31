### Integer

 > `Integer`是`int`的包装类,包含一个`int`类型的字段,它还提供了一些`String`和`int`类型之间相互转换的方法,还提供了一些位运算。

 类的声明
```java
 public final class Integer extends Number implements Comparable<Integer>{}
```
该类有`final`修饰，不能被继承；他自己继承了抽象类`Number`类，实现了`byteValue()`、`shortValue()`、`intValue()`、`longValue()`、`floatValue()`和`doubleValue()`方法。具体实现方法都是通过强转完成的，所以转换为`byte`和`short`类型的时候可能会有精度丢失问题，代码如下。
```java
    public byte byteValue() {
        return (byte)value;
    }
    public short shortValue() {
        return (short)value;
    }
    public int intValue() {
        return value;
    }
    public long longValue() {
        return (long)value;
    }
    public float floatValue() {
        return (float)value;
    }
    public double doubleValue() {
        return (double)value;
    }
```
这个类还实现了`Comparable`接口，实现了`compareTo()`方法，调用`compare()`方法进行比较，若传入的值大于对象自己的值返回-1，等于返回0，小于则返回1，具体实现代码如下。
```java
    public int compareTo(Integer anotherInteger) {
        return compare(this.value, anotherInteger.value);
    }
    public static int compare(int x, int y) {
        return (x < y) ? -1 : ((x == y) ? 0 : 1);
    }
```
维护的字段
```java
   //非静态的，Integer包装类包装的int值
    private final int value;
   //value的取值范围(-2^31~2^31-1)
    public static final int   MIN_VALUE = 0x80000000;
    public static final int   MAX_VALUE = 0x7fffffff;
   //类实例
    public static final Class<Integer>  TYPE = (Class<Integer>) Class.getPrimitiveClass("int");
    //将数字表示为字符串的所有可能字符数组
    final static char[] digits = {
        '0' , '1' , '2' , '3' , '4' , '5' ,
        '6' , '7' , '8' , '9' , 'a' , 'b' ,
        'c' , 'd' , 'e' , 'f' , 'g' , 'h' ,
        'i' , 'j' , 'k' , 'l' , 'm' , 'n' ,
        'o' , 'p' , 'q' , 'r' , 's' , 't' ,
        'u' , 'v' , 'w' , 'x' , 'y' , 'z'
    };
    //二进制补码形式表示int类型值所需要的字节数
    public static final int SIZE = 32;
    //序列化
    private static final long serialVersionUID = 1360826667806852920L;
```
构造方法
```java
    public Integer(int value) {
        this.value = value;
    }
    public Integer(String s) throws NumberFormatException {
        this.value = parseInt(s, 10);
    }
```
第一个是方法是将传入的`int`类型的值赋给`value`字段，第二个是将传入的字符串通过`parseInt(String s, int radix)`方法转换为`int`类型值后赋给`value`字段。

`parseInt`具体实现
```java
    //默认转换成10进制数
    public static int parseInt(String s) throws NumberFormatException {
        return parseInt(s,10);
    }
    public static int parseInt(String s, int radix)
                throws NumberFormatException
    {
        /*
         * WARNING: This method may be invoked early during VM initialization
         * before IntegerCache is initialized. Care must be taken to not use
         * the valueOf method.
         *此方法在初始化IntegerCache之前的VM初始化过程中调用
         */
        if (s == null) {
            throw new NumberFormatException("null");
        }
        //进制最小值2
        if (radix < Character.MIN_RADIX) {
            throw new NumberFormatException("radix " + radix +
                                            " less than Character.MIN_RADIX");
        }
        //进制最大值36
        if (radix > Character.MAX_RADIX) {
            throw new NumberFormatException("radix " + radix +
                                            " greater than Character.MAX_RADIX");
        }

        int result = 0;
        boolean negative = false;
        int i = 0, len = s.length();
        int limit = -Integer.MAX_VALUE;
        int multmin;
        int digit;

        if (len > 0) {
            char firstChar = s.charAt(0);
            if (firstChar < '0') { // Possible leading "+" or "-"
                if (firstChar == '-') {
                    negative = true;
                    limit = Integer.MIN_VALUE;
                } else if (firstChar != '+')
                    throw NumberFormatException.forInputString(s);
                if (len == 1) // Cannot have lone "+" or "-"
                    throw NumberFormatException.forInputString(s);
                i++;
            }
            multmin = limit / radix;
             //将字符串中的字符按顺序转换为相应进制的int值
            while (i < len) {
                // Accumulating negatively avoids surprises near MAX_VALUE
                digit = Character.digit(s.charAt(i++),radix);
                if (digit < 0) {
                    throw NumberFormatException.forInputString(s);
                }
                //溢出检查
                if (result < multmin) {
                    throw NumberFormatException.forInputString(s);
                }
                result *= radix;
                //溢出检查
                if (result < limit + digit) {
                    throw NumberFormatException.forInputString(s);
                }
                //负数累加避免溢出
                result -= digit;
            }
        } else {
            throw NumberFormatException.forInputString(s);
        }
        return negative ? result : -result;
    }
```
除了`parseInt()`，还有其他`string`转`int`的方法；
```java
    public static Integer decode(String nm) throws NumberFormatException {
        int radix = 10;
        int index = 0;
        boolean negative = false;
        Integer result;

        if (nm.length() == 0)
            throw new NumberFormatException("Zero length string");
        char firstChar = nm.charAt(0);
        // Handle sign, if present
        if (firstChar == '-') {
            negative = true;
            index++;
        } else if (firstChar == '+')
            index++;

        // Handle radix specifier, if present
        if (nm.startsWith("0x", index) || nm.startsWith("0X", index)) {
            index += 2;
            radix = 16;
        }
        else if (nm.startsWith("#", index)) {
            index ++;
            radix = 16;
        }
        else if (nm.startsWith("0", index) && nm.length() > 1 + index) {
            index ++;
            radix = 8;
        }

        if (nm.startsWith("-", index) || nm.startsWith("+", index))
            throw new NumberFormatException("Sign character in wrong position");
        try {
            result = Integer.valueOf(nm.substring(index), radix);
            result = negative ? Integer.valueOf(-result.intValue()) : result;
        } catch (NumberFormatException e) {
            // If number is Integer.MIN_VALUE, we'll end up here. The next line
            // handles this case, and causes any genuine format error to be
            // rethrown.
            String constant = negative ? ("-" + nm.substring(index))
                                       : nm.substring(index);
            result = Integer.valueOf(constant, radix);
        }
        return result;
    }
```
`decode(String nm)`方法将字符串解码成`int`值，接受十进制、十六进制和八进制形式的字符串，以`0x`、`0X`、`#`开头的表示十六进制数字符串，以`0`开头的表示八进制数字符串，其他默认为十进制数，确定进制之后去调用`Integer.valueOf(String s, int radix)`方法。
```java
    public static Integer valueOf(String s, int radix) throws NumberFormatException {
        return Integer.valueOf(parseInt(s,radix));
    }
    public static Integer valueOf(String s) throws NumberFormatException {
        //默认为十进制
        return Integer.valueOf(parseInt(s, 10));
    }
```
还是调用`parseInt()`方法进行转换的，得到对应的`int`值后，通过`
Integer.valueOf(int)`方法得到`Integer`对象。
```java
public static Integer valueOf(int i) {
        assert IntegerCache.high >= 127;
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
```
如果`int`值在`IntegerCache.low`和` IntegerCache.high`之间，将返回缓存中已经创建好的`Integer`对象，若不在，则每次都会创建新的对象。
`IntegerCache`是`Integer`的内部类，用来存放缓存数组。`IntegerCache.low`的值为-128， `IntegerCache.high`的值默认为127可以修改。
```java
    private static class IntegerCache {
        static final int low = -128;
        static final int high;
        static final Integer cache[];

        static {
            // high value may be configured by property
            int h = 127;
            String integerCacheHighPropValue =
               sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                int i = parseInt(integerCacheHighPropValue);
                i = Math.max(i, 127);
                // Maximum array size is Integer.MAX_VALUE
                h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
            }
            high = h;

            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);
        }
        private IntegerCache() {}
    }
```
`int`转`string`的方法
```java
    public String toString() {
        return toString(value);
    }

    public static String toString(int i) {
        if (i == Integer.MIN_VALUE)
            return "-2147483648";
         //获取需要的字符串长度
        int size = (i < 0) ? stringSize(-i) + 1 : stringSize(i);
        char[] buf = new char[size];
        getChars(i, size, buf);
        return new String(buf, true);
    }

    final static int [] sizeTable = { 9, 99, 999, 9999, 99999, 999999, 9999999,
                                      99999999, 999999999, Integer.MAX_VALUE };

    // Requires positive x
    static int stringSize(int x) {
        for (int i=0; ; i++)
            if (x <= sizeTable[i])
                return i+1;
    }

    static void getChars(int i, int index, char[] buf) {
        int q, r;
        int charPos = index;
        char sign = 0;

        if (i < 0) {
            sign = '-';
            i = -i;
        }
        // Generate two digits per iteration
        //当i>=65536时，每次取最后两位放入数组
        while (i >= 65536) {
            q = i / 100;
        // really: r = i - (q * 100);
            r = i - ((q << 6) + (q << 5) + (q << 2));
            i = q;
            buf [--charPos] = DigitOnes[r];
            buf [--charPos] = DigitTens[r];
        }
        // Fall thru to fast mode for smaller numbers
        // assert(i <= 65536, i);
        //i小于65536时，每次取最后一位放入数组
        for (;;) {
            q = (i * 52429) >>> (16+3);
            r = i - ((q << 3) + (q << 1));  // r = i-(q*10) ...
            buf [--charPos] = digits [r];
            i = q;
            if (i == 0) break;
        }
        if (sign != 0) {
            buf [--charPos] = sign;
        }
    }

    //转成指定进制字符串
    public static String toString(int i, int radix) {
    //如果进制小于Character的最小进制或者大于最大进制,将取十进制
        if (radix < Character.MIN_RADIX || radix >
Character.MAX_RADIX)
            radix = 10;

        /* Use the faster version */
        if (radix == 10) {
            return toString(i);
        }

        char buf[] = new char[33];
        boolean negative = (i < 0);
        int charPos = 32;

        if (!negative) {
            i = -i;
        }
        //循环对radix取余
        while (i <= -radix) {
            buf[charPos--] = digits[-(i % radix)];
            i = i / radix;
        }
        buf[charPos] = digits[-i];

        if (negative) {
            buf[--charPos] = '-';
        }
        return new String(buf, charPos, (33 - charPos));
    }

    //转无符号字符串
    private static String toUnsignedString(int i, int shift) {
        char[] buf = new char[32];
        int charPos = 32;
        int radix = 1 << shift;
        int mask = radix - 1;
        do {
            buf[--charPos] = digits[i & mask];
            i >>>= shift;
        } while (i != 0);

        return new String(buf, charPos, (32 - charPos));
    }
```
`hashCoe()`和`equals()`
```java
    public int hashCode() {
        return value;
    }
    public boolean equals(Object obj) {
        if (obj instanceof Integer) {
            return value == ((Integer)obj).intValue();
        }
        return false;
    }
```
 `getInteger()`，这个方法是用于获取系统属性指定的`int`值
 ```java
    public static Integer getInteger(String nm) {
        return getInteger(nm, null);
    }
    public static Integer getInteger(String nm, int val) {
        Integer result = getInteger(nm, null);
        return (result == null) ? Integer.valueOf(val) : result;
    }
    public static Integer getInteger(String nm, Integer val) {
        String v = null;
        try {
            v = System.getProperty(nm);
        } catch (IllegalArgumentException e) {
        } catch (NullPointerException e) {
        }
        if (v != null) {
            try {
                return Integer.decode(v);
            } catch (NumberFormatException e) {
            }
        }
        return val;
    }
```
 位运算
 ```java
    //以二进制补码形式表示，取为1的最高位，右边全部为0表示的int值
    //测试了一下，好像负数都会返回Integer.MIN_VALUE
    public static int highestOneBit(int i) {
        // HD, Figure 3-1
        i |= (i >>  1);
        i |= (i >>  2);
        i |= (i >>  4);
        i |= (i >>  8);
        i |= (i >> 16);
        return i - (i >>> 1);
    }
    //以二进制补码形式表示，取为1的最低位，其余全部为0
    //应该除了Integer.MIN_VALUE和0会返回自身，其余值都返回正数
    public static int lowestOneBit(int i) {
        // HD, Section 2-1
        return i & -i;
    }
    //返回左边为1最高位之前的0的个数，负数都返回0
    public static int numberOfLeadingZeros(int i) {
        // HD, Figure 5-6
        if (i == 0)
            return 32;
        int n = 1;
        if (i >>> 16 == 0) { n += 16; i <<= 16; }
        if (i >>> 24 == 0) { n +=  8; i <<=  8; }
        if (i >>> 28 == 0) { n +=  4; i <<=  4; }
        if (i >>> 30 == 0) { n +=  2; i <<=  2; }
        n -= i >>> 31;
        return n;
    }
    //返回右边为1最低位之后的0的个数
    public static int numberOfTrailingZeros(int i) {
        // HD, Figure 5-14
        int y;
        if (i == 0) return 32;
        int n = 31;
        y = i <<16; if (y != 0) { n = n -16; i = y; }
        y = i << 8; if (y != 0) { n = n - 8; i = y; }
        y = i << 4; if (y != 0) { n = n - 4; i = y; }
        y = i << 2; if (y != 0) { n = n - 2; i = y; }
        return n - ((i << 1) >>> 31);
    }
    //二进制补码形式表示1的个数
    public static int bitCount(int i) {
        // HD, Figure 5-2
        i = i - ((i >>> 1) & 0x55555555);
        i = (i & 0x33333333) + ((i >>> 2) & 0x33333333);
        i = (i + (i >>> 4)) & 0x0f0f0f0f;
        i = i + (i >>> 8);
        i = i + (i >>> 16);
        return i & 0x3f;
    }
    //将i的二进制补码循环左移distance，左边的数移出后补到右边
    public static int rotateLeft(int i, int distance) {
        return (i << distance) | (i >>> -distance);
    }
    //与上面的方法相反
    public static int rotateRight(int i, int distance) {
        return (i >>> distance) | (i << -distance);
    }
    //返回i的二进制补码反转表示的数
    public static int reverse(int i) {
        // HD, Figure 7-1
        i = (i & 0x55555555) << 1 | (i >>> 1) & 0x55555555;
        i = (i & 0x33333333) << 2 | (i >>> 2) & 0x33333333;
        i = (i & 0x0f0f0f0f) << 4 | (i >>> 4) & 0x0f0f0f0f;
        i = (i << 24) | ((i & 0xff00) << 8) |
            ((i >>> 8) & 0xff00) | (i >>> 24);
        return i;
    }
    //正数返回1；负数-1；0返回0
    public static int signum(int i) {
        // HD, Section 2-7
        return (i >> 31) | (-i >>> 31);
    }
    //二进制补码形式表示，占四个字节，第一个字节的八位和最后一个字节的八位    //交换位置，中间两个字节交换位置
    public static int reverseBytes(int i) {
        return ((i >>> 24)           ) |
               ((i >>   8) &   0xFF00) |
               ((i <<   8) & 0xFF0000) |
               ((i << 24));
    }
```
