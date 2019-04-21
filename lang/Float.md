> `Float`是基础类型`float`的包装类，包装了一个`float`类型的值

类的声明
```java
public final class Float extends Number implements Comparable<Float> {}
```
同样有`final`修饰，不能被继承，继承了`Number`类，也实现了`Comparable`接口
```java
    public byte byteValue() {
        return (byte)value;
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
        return value;
    }
    public double doubleValue() {
        return (double)value;
    }
    public int compareTo(Float anotherFloat) {
        return Float.compare(value, anotherFloat.value);
    }
    public static int compare(float f1, float f2) {
        if (f1 < f2)
            return -1;           // Neither val is NaN, thisVal is smaller
        if (f1 > f2)
            return 1;            // Neither val is NaN, thisVal is larger

        // Cannot use floatToRawIntBits because of possibility of NaNs.
        int thisBits    = Float.floatToIntBits(f1);
        int anotherBits = Float.floatToIntBits(f2);

        return (thisBits == anotherBits ?  0 : // Values are equal
                (thisBits < anotherBits ? -1 : // (-0.0, 0.0) or (!NaN, NaN)
                 1));                          // (0.0, -0.0) or (NaN, !NaN)
    }
    public static int floatToIntBits(float value) {
        int result = floatToRawIntBits(value);
        // Check for NaN based on values of bit fields, maximum
        // exponent and nonzero significand.
        if ( ((result & FloatConsts.EXP_BIT_MASK) ==
              FloatConsts.EXP_BIT_MASK) &&
             (result & FloatConsts.SIGNIF_BIT_MASK) != 0)
            result = 0x7fc00000;
        return result;
    }
    public static native int floatToRawIntBits(float value);
```
`FLoat`的`compare`方法有自己的实现方式，通过调用`floatToIntBits()`方法来获取浮点数的位再转换成10进制`int`返回，再对`float`值进行比较。
`Float`的字段
```java
  //保存类型正无穷的常量
  public static final float POSITIVE_INFINITY = 1.0f / 0.0f;
  //保存类型负无穷的常量
  public static final float NEGATIVE_INFINITY = -1.0f / 0.0f;
  //保存NaN类型值得常量
  public static final float NaN = 0.0f / 0.0f;
  //float能表示的最大正值
  public static final float MAX_VALUE = 0x1.fffffeP+127f; // 3.4028235e+38f
  //float能表示的最小值
  public static final float MIN_NORMAL = 0x1.0p-126f; // 1.17549435E-38f
  //float能表示的最小正值,我理解成精度
  public static final float MIN_VALUE = 0x0.000002P-126f; // 1.4e-45f
  //最大指数值
  public static final int MAX_EXPONENT = 127;
  //最小指数值
  public static final int MIN_EXPONENT = -126;
  //一个float数值占的位数
  public static final int SIZE = 32;
  //字节数
  public static final int BYTES = SIZE / Byte.SIZE;
  //类实例
  public static final Class<Float> TYPE = (Class<Float>) Class.getPrimitiveClass("float");
  //包装的float值
  private final float value;
  //序列化
  private static final long serialVersionUID = -2671257302660747028L;
```
构造方法
```java
    public Float(float value) {
        this.value = value;
    }
    public Float(double value) {
        this.value = (float)value;
    }
    public Float(String s) throws NumberFormatException {
        value = parseFloat(s);
    }
```
`parseFloat`是调用的`FloatingDecimal`类的`parseFloat()`方法
```java
    public static float parseFloat(String s) throws NumberFormatException {
        return FloatingDecimal.parseFloat(s);
    }
```
`valueOf`方法
```java
    public static Float valueOf(String s) throws NumberFormatException {
        return new Float(parseFloat(s));
    }
    public static Float valueOf(float f) {
        return new Float(f);
    }
```
`toString`方法
```java
    public String toString() {
        return Float.toString(value);
    }
    public static String toString(float f) {
        return FloatingDecimal.toJavaFormatString(f);
    }
    //返回十六进制字符串表示形式
    public static String toHexString(float f) {
        if (Math.abs(f) < FloatConsts.MIN_NORMAL
            &&  f != 0.0f ) {// float subnormal
            // Adjust exponent to create subnormal double, then
            // replace subnormal double exponent with subnormal float
            // exponent
            String s = Double.toHexString(Math.scalb((double)f,
                                                     /* -1022+126 */
                                                     DoubleConsts.MIN_EXPONENT-
                                                     FloatConsts.MIN_EXPONENT));
            return s.replaceFirst("p-1022$", "p-126");
        }
        else // double string will be the same as float string
            return Double.toHexString(f);
    }
```
`hashCode`和`equals`方法
```java
    public int hashCode() {
        return Float.hashCode(value);
    }
    public static int hashCode(float value) {
        return floatToIntBits(value);
    }
    public boolean equals(Object obj) {
        return (obj instanceof Float)
               && (floatToIntBits(((Float)obj).value) == floatToIntBits(value));
    }
```
`Float`的其他方法
```java
    //判断参数是否是一个数值，因为NaN与任何数值比较都返回false，包括NaN但是如果用Float的compare方法比较会返回true
    public static boolean isNaN(float v) {
        return (v != v);
    }
    public boolean isNaN() {
        return isNaN(value);
    }
    //判断参数是否是无穷大
    public static boolean isInfinite(float v) {
        return (v == POSITIVE_INFINITY) || (v == NEGATIVE_INFINITY);
    }
    public boolean isInfinite() {
        return isInfinite(value);
    }
    //是否小于最小精度
    public static boolean isFinite(float f) {
        return Math.abs(f) <= FloatConsts.MAX_VALUE;
    }
    //返回给定值对应的float值
    public static native float intBitsToFloat(int bits);
    //求和
    public static float sum(float a, float b) {
        return a + b;
    }
    //求两数中的大值
    public static float max(float a, float b) {
        return Math.max(a, b);
    }
    //求两数中的小值
    public static float min(float a, float b) {
        return Math.min(a, b);
    }
```
