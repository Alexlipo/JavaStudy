### Double

 > `Double`是`double`的包装类,包含一个`double`类型的字段。

 类的声明
```java
  public final class Double extends Number implements Comparable<Double> {}
```
实现方法，直接看`compare`方法
```java
    public int compareTo(Double anotherDouble) {
        return Double.compare(value, anotherDouble.value);
    }
    public static int compare(double d1, double d2) {
        if (d1 < d2)
            return -1;           // Neither val is NaN, thisVal is smaller
        if (d1 > d2)
            return 1;            // Neither val is NaN, thisVal is larger

        // Cannot use doubleToRawLongBits because of possibility of NaNs.
        long thisBits    = Double.doubleToLongBits(d1);
        long anotherBits = Double.doubleToLongBits(d2);

        return (thisBits == anotherBits ?  0 : // Values are equal
                (thisBits < anotherBits ? -1 : // (-0.0, 0.0) or (!NaN, NaN)
                 1));                          // (0.0, -0.0) or (NaN, !NaN)
    }
    public static long doubleToLongBits(double value) {
        long result = doubleToRawLongBits(value);
        // Check for NaN based on values of bit fields, maximum
        // exponent and nonzero significand.
        if ( ((result & DoubleConsts.EXP_BIT_MASK) ==
              DoubleConsts.EXP_BIT_MASK) &&
             (result & DoubleConsts.SIGNIF_BIT_MASK) != 0L)
            result = 0x7ff8000000000000L;
        return result;
    }
    public static native long doubleToRawLongBits(double value);
```
字段，一些字段`Float`中也有
```java
    public static final double POSITIVE_INFINITY = 1.0 / 0.0;
    public static final double NEGATIVE_INFINITY = -1.0 / 0.0;
    public static final double NaN = 0.0d / 0.0;
    public static final double MAX_VALUE = 0x1.fffffffffffffP+1023;
    public static final double MIN_NORMAL = 0x1.0p-1022;
    public static final double MIN_VALUE = 0x0.0000000000001P-1022;
    public static final int MAX_EXPONENT = 1023;
    public static final int MIN_EXPONENT = -1022;
    public static final int SIZE = 64;
    public static final int BYTES = SIZE / Byte.SIZE;
    @SuppressWarnings("unchecked")
    public static final Class<Double>   TYPE = (Class<Double>) Class.getPrimitiveClass("double");
    private final double value;
    private static final long serialVersionUID = -9172774392245257468L;
```
构造方法
```java
    public Double(double value) {
        this.value = value;
    }
    public Double(String s) throws NumberFormatException {
        value = parseDouble(s);
    }
    public Float(String s) throws NumberFormatException {
        value = parseFloat(s);
    }
```
`parseDouble`是调用的`FloatingDecimal`类的`parseDouble()`方法
```java
    public static double parseDouble(String s) throws NumberFormatException {
        return FloatingDecimal.parseDouble(s);
    }
```
`valueOf`方法
```java
    public static Double valueOf(String s) throws NumberFormatException {
        return new Double(parseDouble(s));
    }
    public static Double valueOf(double d) {
        return new Double(d);
    }
```
`toString`方法
```java
    public String toString() {
        return toString(value);
    }
    public static String toString(double d) {
        return FloatingDecimal.toJavaFormatString(d);
    }
    //返回十六进制字符串表示形式
    public static String toHexString(double d) {
        if (!isFinite(d) )
            // For infinity and NaN, use the decimal output.
            return Double.toString(d);
        else {
            // Initialized to maximum size of output.
            StringBuilder answer = new StringBuilder(24);
            if (Math.copySign(1.0, d) == -1.0)    // value is negative,
                answer.append("-");                  // so append sign info
            answer.append("0x");
            d = Math.abs(d);
            if(d == 0.0) {
                answer.append("0.0p0");
            } else {
                boolean subnormal = (d < DoubleConsts.MIN_NORMAL);
                // Isolate significand bits and OR in a high-order bit
                // so that the string representation has a known
                // length.
                long signifBits = (Double.doubleToLongBits(d)
                                   & DoubleConsts.SIGNIF_BIT_MASK) |
                    0x1000000000000000L;
                // Subnormal values have a 0 implicit bit; normal
                // values have a 1 implicit bit.
                answer.append(subnormal ? "0." : "1.");

                // Isolate the low-order 13 digits of the hex
                // representation.  If all the digits are zero,
                // replace with a single 0; otherwise, remove all
                // trailing zeros.
                String signif = Long.toHexString(signifBits).substring(3,16);
                answer.append(signif.equals("0000000000000") ? // 13 zeros
                              "0":
                              signif.replaceFirst("0{1,12}$", ""));
                answer.append('p');
                // If the value is subnormal, use the E_min exponent
                // value for double; otherwise, extract and report d's
                // exponent (the representation of a subnormal uses
                // E_min -1).
                answer.append(subnormal ?
                              DoubleConsts.MIN_EXPONENT:
                              Math.getExponent(d));
            }
            return answer.toString();
        }
    }
```
`hashCode`和`equals`方法
```java
    public int hashCode() {
        return Double.hashCode(value);
    }
    public static int hashCode(double value) {
        long bits = doubleToLongBits(value);
        return (int)(bits ^ (bits >>> 32));
    }
    public boolean equals(Object obj) {
        return (obj instanceof Double)
               && (doubleToLongBits(((Double)obj).value) ==
                      doubleToLongBits(value));
    }
```
`Double`的其他方法
```java
    //判断参数是否是一个数值，因为NaN与任何数值比较都返回false，包括NaN但是如果用Double的compare方法比较会返回true
    public static boolean isNaN(double v) {
        return (v != v);
    }
    public boolean isNaN() {
        return isNaN(value);
    }
    //判断参数是否是无穷大
    public boolean isInfinite() {
        return isInfinite(value);
    }
    public static boolean isInfinite(double v) {
        return (v == POSITIVE_INFINITY) || (v == NEGATIVE_INFINITY);
    }
    //是否小于最小精度
    public static boolean isFinite(double d) {
        return Math.abs(d) <= DoubleConsts.MAX_VALUE;
    }
    //返回给定值对应的double值
    public static native double longBitsToDouble(long bits);
    //转long类型值
    public static long doubleToLongBits(double value) {
        long result = doubleToRawLongBits(value);
        // Check for NaN based on values of bit fields, maximum
        // exponent and nonzero significand.
        if ( ((result & DoubleConsts.EXP_BIT_MASK) ==
              DoubleConsts.EXP_BIT_MASK) &&
             (result & DoubleConsts.SIGNIF_BIT_MASK) != 0L)
            result = 0x7ff8000000000000L;
        return result;
    }
    public static native long doubleToRawLongBits(double value);
    //求和
    public static double sum(double a, double b) {
        return a + b;
    }
    //求两数中的大值
    public static double max(double a, double b) {
        return Math.max(a, b);
    }
    //求两数中的小值
    public static double min(double a, double b) {
        return Math.min(a, b);
    }
```
