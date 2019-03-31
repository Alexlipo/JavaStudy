### Byte

 > `Byte`是基础类型`byte`的包装类，包装了一个`byte`类型的值

类的声明
```java
public final class Byte extends Number implements Comparable<Byte> {}
```
和`Integer`一样，有`final`修饰，不能被继承，继承了`Number`类，也实现了`Comparable`接口，所以需要实现以下这些方法
```java
    public byte byteValue() {
        return value;
    }
    public short shortValue() {
        return (short)value;
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
    public int compareTo(Byte anotherByte) {
        return compare(this.value, anotherByte.value);
    }
    public static int compare(byte x, byte y) {
        return x - y;
    }
```
字段
```java
    //包装的byte值
    private final byte value;
    //取值范围-128~127
    public static final byte   MIN_VALUE = -128;
    public static final byte   MAX_VALUE = 127;
    //类实例
    public static final Class<Byte>     TYPE = (Class<Byte>) Class.getPrimitiveClass("byte");
    //二进制补码形式表示byte类型值所需要的字节数
    public static final int SIZE = 8;
    //序列化
    private static final long serialVersionUID = -7183698231559129828L;
```
构造方法
```java
    public Byte(byte value) {
        this.value = value;
    }
    public Byte(String s) throws NumberFormatException {
        this.value = parseByte(s, 10);
    }
```
和`Integer`一样，看看`parseByte`具体实现
```java
    public static byte parseByte(String s) throws NumberFormatException {
        return parseByte(s, 10);
    }
    public static byte parseByte(String s, int radix)
        throws NumberFormatException {
        int i = Integer.parseInt(s, radix);
        if (i < MIN_VALUE || i > MAX_VALUE)
            throw new NumberFormatException(
                "Value out of range. Value:\"" + s + "\" Radix:" + radix);
        return (byte)i;
    }
```
是调用的`Integer.parseInt()`来实现的，然后判断值是否在`byte`类型值的取值范围，强转成`Byte`对象。

`decode()`方法，和上面一样也是调用的`Integet.decode()`方法。
```java
    public static Byte decode(String nm) throws NumberFormatException {
        int i = Integer.decode(nm);
        if (i < MIN_VALUE || i > MAX_VALUE)
            throw new NumberFormatException(
                    "Value " + i + " out of range from input " + nm);
        return valueOf((byte)i);
    }
```
`valueOf()`具体实现，调用的`parseByte()`方法，`Byte`也有一个用来存放缓存数组的内部类`ByteCache`
```java
    public static Byte valueOf(String s) throws NumberFormatException {
        return valueOf(s, 10);
    }
    public static Byte valueOf(String s, int radix)
        throws NumberFormatException {
        return valueOf(parseByte(s, radix));
    }
    public static Byte valueOf(byte b) {
        final int offset = 128;
        return ByteCache.cache[(int)b + offset];
    }
    private static class ByteCache {
        private ByteCache(){}

        static final Byte cache[] = new Byte[-(-128) + 127 + 1];

        static {
            for(int i = 0; i < cache.length; i++)
                cache[i] = new Byte((byte)(i - 128));
        }
    }
```
`toString()`方法，也是调用的`Integer`的`toString()`方法
```java
    public static String toString(byte b) {
        return Integer.toString((int)b, 10);
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
        if (obj instanceof Byte) {
            return value == ((Byte)obj).byteValue();
        }
        return false;
    }
```
