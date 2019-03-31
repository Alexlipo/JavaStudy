### Short

 > `Short`是基础类型`short`的包装类，包装了一个`short`类型的值

类的声明
```java
public final class Short extends Number implements Comparable<Short> {...}
```
`Short`和`Byte`很相似，只是有些值不一样，同样继承了`Number`类，也实现了`Comparable`接口，所以需要实现以下这些方法
```java
    public byte byteValue() {
        return (byte)value;
    }
    public short shortValue() {
        return value;
    }
    public int intValue() {
        return (int)value;
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
    //返回调用者与传入值的差
    public int compareTo(Short anotherShort) {
        return compare(this.value, anotherShort.value);
    }
    public static int compare(short x, short y) {
        return x - y;
    }
```
字段
```java
    //包装的short值
    private final short value;
    //取值范围-2^15~2^15-1
    public static final short   MIN_VALUE = -32768;
    public static final short   MAX_VALUE = 32767;
    //类实例
    public static final Class<Short>    TYPE = (Class<Short>) Class.getPrimitiveClass("short");
    //二进制补码形式表示byte类型值所需要的字节数
    public static final int SIZE = 16;
    //序列化
    private static final long serialVersionUID = 7515723908773894738L;
```
构造方法
```java
    public Short(short value) {
        this.value = value;
    }
    public Short(String s) throws NumberFormatException {
        this.value = parseShort(s, 10);
    }
```
`parseShort`具体实现
```java
    public static short parseShort(String s) throws NumberFormatException {
        return parseShort(s, 10);
    }
    public static short parseShort(String s, int radix)
        throws NumberFormatException {
        int i = Integer.parseInt(s, radix);
        if (i < MIN_VALUE || i > MAX_VALUE)
            throw new NumberFormatException(
                "Value out of range. Value:\"" + s + "\" Radix:" + radix);
        return (short)i;
    }
```
也是调用的`Integer.parseInt()`来实现的，然后判断值是否在`short`类型值的取值范围，强转成`Short`对象。

`decode()`方法，和上面一样也是调用的`Integet.decode()`方法。
```java
    public static Short decode(String nm) throws NumberFormatException {
        int i = Integer.decode(nm);
        if (i < MIN_VALUE || i > MAX_VALUE)
            throw new NumberFormatException(
                    "Value " + i + " out of range from input " + nm);
        return valueOf((short)i);
    }
```
`valueOf()`具体实现，调用的`parseByte()`方法，`Short`也有一个用来存放缓存数组的内部类`ShortCache`，存放一个字节大小的数值
```java
    public static Short valueOf(String s) throws NumberFormatException {
        return valueOf(s, 10);
    }
    public static Short valueOf(String s, int radix)
        throws NumberFormatException {
        return valueOf(parseShort(s, radix));
    }
    public static Short valueOf(short s) {
        final int offset = 128;
        int sAsInt = s;
        if (sAsInt >= -128 && sAsInt <= 127) { // must cache
            return ShortCache.cache[sAsInt + offset];
        }
        return new Short(s);
    }
    private static class ShortCache {
        private ShortCache(){}

        static final Short cache[] = new Short[-(-128) + 127 + 1];

        static {
            for(int i = 0; i < cache.length; i++)
                cache[i] = new Short((short)(i - 128));
        }
    }
```
`toString()`方法，也是调用的`Integer`的`toString()`方法
```java
    public static String toString(short s) {
        return Integer.toString((int)s, 10);
    }
    public String toString() {
        return Integer.toString((int)value);
    }
```
`hashCode()`和`equals()`方法
```java
    public int hashCode() {
        return (int)value;
    }

    public boolean equals(Object obj) {
        if (obj instanceof Short) {
            return value == ((Short)obj).shortValue();
        }
        return false;
    }
```
`Short`还有一个`reverseBytes`方法，和`Integer`的`reverseBytes`方法一样，因为`Short`类型占两个字节，所以将二进制补码形式表示的第一个字节和第二个字节内容交换位置
```java
    public static short reverseBytes(short i) {
        return (short) (((i & 0xFF00) >> 8) | (i << 8));
    }
```
