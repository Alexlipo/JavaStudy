### Math

> `Math`是`java.lang`包下的一个类，包装了一些基本数值的操作方法和常量

```java
    public final class Math {}
```
有`final`修饰，不可继承，再关注它的字段以及构造方法
```java
    //自然对数底
    public static final double E = 2.7182818284590452354;
    //π
    public static final double PI = 3.14159265358979323846;
    //对保证的非nan参数使用原始的逐位转换。
    private static long negativeZeroFloatBits  = Float.floatToRawIntBits(-0.0f);
    private static long negativeZeroDoubleBits = Double.doubleToRawLongBits(-0.0d);
    //用于标量的常熟
    static double twoToTheDoubleScaleUp = powerOfTwoD(512);
    static double twoToTheDoubleScaleDown = powerOfTwoD(-512);
    //构造方法私有化，不能直接创建对象
    private Math() {}
```
`Math`类中包装的所有方法都有`static`修饰，所以可以直接用`Math`调用
```java
    //三角函数，默认是StrictMath的实现
    public static double sin(double a) {
        return StrictMath.sin(a);
    }
    public static double cos(double a) {
        return StrictMath.cos(a);
    }
    public static double tan(double a) {
        return StrictMath.tan(a);
    }
    //反三角函数
    public static double asin(double a) {
        return StrictMath.asin(a);
    }
    public static double acos(double a) {
        return StrictMath.acos(a);
    }
    public static double atan(double a) {
        return StrictMath.atan(a);
    }
    //角度转弧度
    public static double toRadians(double angdeg) {
        return angdeg / 180.0 * PI;
    }
    //弧度转角度
    public static double toDegrees(double angrad) {
        return angrad * 180.0 / PI;
    }
    //e为底的指数函数
    public static double exp(double a) {
        return StrictMath.exp(a);
    }
    //e为底的对数函数
    public static double log(double a) {
        return StrictMath.log(a);
    }
    //10为底的对数函数
    public static double log10(double a) {
        return StrictMath.log10(a);
    }
    //平方根
    public static double sqrt(double a) {
        return StrictMath.sqrt(a);
    }
    //立方根
    public static double cbrt(double a) {
        return StrictMath.cbrt(a);
    }
    //求两数的余数，返回f1 - f2 × n，n = f1 / f2
    public static double IEEEremainder(double f1, double f2) {
        return StrictMath.IEEEremainder(f1, f2); // delegate to StrictMath
    }
    //向上取整
    public static double ceil(double a) {
        return StrictMath.ceil(a);
    }
    //向下取整
    public static double floor(double a) {
        return StrictMath.floor(a);
    }
    //四舍五入，返回double类型值
    public static double rint(double a) {
        return StrictMath.rint(a);
    }
    //反正切值；x：横坐标，y：纵坐标
    public static double atan2(double y, double x) {
        return StrictMath.atan2(y, x);
    }
    //a的b次幂
    public static double pow(double a, double b) {
        return StrictMath.pow(a, b);
    }
    //四舍五入，传float会返回int
    public static int round(float a) {
        int intBits = Float.floatToRawIntBits(a);
        int biasedExp = (intBits & FloatConsts.EXP_BIT_MASK)
                >> (FloatConsts.SIGNIFICAND_WIDTH - 1);
        int shift = (FloatConsts.SIGNIFICAND_WIDTH - 2
                + FloatConsts.EXP_BIAS) - biasedExp;
        if ((shift & -32) == 0) { // shift >= 0 && shift < 32
            // a is a finite number such that pow(2,-32) <= ulp(a) < 1
            int r = ((intBits & FloatConsts.SIGNIF_BIT_MASK)
                    | (FloatConsts.SIGNIF_BIT_MASK + 1));
            if (intBits < 0) {
                r = -r;
            }
            // In the comments below each Java expression evaluates to the value
            // the corresponding mathematical expression:
            // (r) evaluates to a / ulp(a)
            // (r >> shift) evaluates to floor(a * 2)
            // ((r >> shift) + 1) evaluates to floor((a + 1/2) * 2)
            // (((r >> shift) + 1) >> 1) evaluates to floor(a + 1/2)
            return ((r >> shift) + 1) >> 1;
        } else {
            // a is either
            // - a finite number with abs(a) < exp(2,FloatConsts.SIGNIFICAND_WIDTH-32) < 1/2
            // - a finite number with ulp(a) >= 1 and hence a is a mathematical integer
            // - an infinity or NaN
            return (int) a;
        }
    }
    //四舍五入，传double会返回long
    public static long round(double a) {
        long longBits = Double.doubleToRawLongBits(a);
        long biasedExp = (longBits & DoubleConsts.EXP_BIT_MASK)
                >> (DoubleConsts.SIGNIFICAND_WIDTH - 1);
        long shift = (DoubleConsts.SIGNIFICAND_WIDTH - 2
                + DoubleConsts.EXP_BIAS) - biasedExp;
        if ((shift & -64) == 0) {
            long r = ((longBits & DoubleConsts.SIGNIF_BIT_MASK)
                    | (DoubleConsts.SIGNIF_BIT_MASK + 1));
            if (longBits < 0) {
                r = -r;
            }
            return ((r >> shift) + 1) >> 1;
        } else {
            return (long) a;
        }
    }
    //取随机数
    public static double random() {
        return RandomNumberGeneratorHolder.randomNumberGenerator.nextDouble();
    }
    //生成随机数内部类
    private static final class RandomNumberGeneratorHolder {
        static final Random randomNumberGenerator = new Random();
    }
    //求和
    public static int addExact(int x, int y) {
        int r = x + y;
        // HD 2-12 Overflow iff both arguments have the opposite sign of the result
        if (((x ^ r) & (y ^ r)) < 0) {
            throw new ArithmeticException("integer overflow");
        }
        return r;
    }
    public static long addExact(long x, long y) {
        long r = x + y;
        // HD 2-12 Overflow iff both arguments have the opposite sign of the result
        if (((x ^ r) & (y ^ r)) < 0) {
            throw new ArithmeticException("long overflow");
        }
        return r;
    }
    //求差
    public static int subtractExact(int x, int y) {
        int r = x - y;
        // HD 2-12 Overflow iff the arguments have different signs and
        // the sign of the result is different than the sign of x
        if (((x ^ y) & (x ^ r)) < 0) {
            throw new ArithmeticException("integer overflow");
        }
        return r;
    }
    public static long subtractExact(long x, long y) {
        long r = x - y;
        if (((x ^ y) & (x ^ r)) < 0) {
            throw new ArithmeticException("long overflow");
        }
        return r;
    }
    //求积
    public static int multiplyExact(int x, int y) {
        long r = (long)x * (long)y;
        if ((int)r != r) {
            throw new ArithmeticException("integer overflow");
        }
        return (int)r;
    }
    public static long multiplyExact(long x, long y) {
        long r = x * y;
        long ax = Math.abs(x);
        long ay = Math.abs(y);
        if (((ax | ay) >>> 31 != 0)) {
            // Some bits greater than 2^31 that might cause overflow
            // Check the result using the divide operator
            // and check for the special case of Long.MIN_VALUE * -1
           if (((y != 0) && (r / y != x)) ||
               (x == Long.MIN_VALUE && y == -1)) {
                throw new ArithmeticException("long overflow");
            }
        }
        return r;
    }
    //自增
    public static int incrementExact(int a) {
        if (a == Integer.MAX_VALUE) {
            throw new ArithmeticException("integer overflow");
        }
        return a + 1;
    }
    public static long incrementExact(long a) {
        if (a == Long.MAX_VALUE) {
            throw new ArithmeticException("long overflow");
        }
        return a + 1L;
    }
    //自减
    public static int decrementExact(int a) {
        if (a == Integer.MIN_VALUE) {
            throw new ArithmeticException("integer overflow");
        }
        return a - 1;
    }
    public static long decrementExact(long a) {
        if (a == Long.MIN_VALUE) {
            throw new ArithmeticException("long overflow");
        }
        return a - 1L;
    }
    //相反数
    public static int negateExact(int a) {
        if (a == Integer.MIN_VALUE) {
            throw new ArithmeticException("integer overflow");
        }
        return -a;
    }
    public static long negateExact(long a) {
        if (a == Long.MIN_VALUE) {
            throw new ArithmeticException("long overflow");
        }
        return -a;
    }
    //long强转成int，如果溢出抛异常
    public static int toIntExact(long value) {
        if ((int)value != value) {
            throw new ArithmeticException("integer overflow");
        }
        return (int)value;
    }
    //返回小于等于代数商的最大值
    public static int floorDiv(int x, int y) {
        int r = x / y;
        // if the signs are different and modulo not zero, round down
        if ((x ^ y) < 0 && (r * y != x)) {
            r--;
        }
        return r;
    }
    public static long floorDiv(long x, long y) {
        long r = x / y;
        // if the signs are different and modulo not zero, round down
        if ((x ^ y) < 0 && (r * y != x)) {
            r--;
        }
        return r;
    }
    //取模
    public static int floorMod(int x, int y) {
        int r = x - floorDiv(x, y) * y;
        return r;
    }
    public static long floorMod(long x, long y) {
       return x - floorDiv(x, y) * y;
   }
   //绝对值
   public static int abs(int a) {
        return (a < 0) ? -a : a;
    }
    public static long abs(long a) {
        return (a < 0) ? -a : a;
    }
    public static float abs(float a) {
        return (a <= 0.0F) ? 0.0F - a : a;
    }
    //取两数中的大值
    public static int max(int a, int b) {
        return (a >= b) ? a : b;
    }
    public static long max(long a, long b) {
        return (a >= b) ? a : b;
    }
    public static float max(float a, float b) {
        if (a != a)
            return a;   // a is NaN
        if ((a == 0.0f) &&
            (b == 0.0f) &&
            (Float.floatToRawIntBits(a) == negativeZeroFloatBits)) {
            // Raw conversion ok since NaN can't map to -0.0.
            return b;
        }
        return (a >= b) ? a : b;
    }
    public static double max(double a, double b) {
        if (a != a)
            return a;   // a is NaN
        if ((a == 0.0d) &&
            (b == 0.0d) &&
            (Double.doubleToRawLongBits(a) == negativeZeroDoubleBits)) {
            // Raw conversion ok since NaN can't map to -0.0.
            return b;
        }
        return (a >= b) ? a : b;
    }
    //取两数中的小值
    public static int min(int a, int b) {
        return (a <= b) ? a : b;
    }
    public static long min(long a, long b) {
        return (a <= b) ? a : b;
    }
    public static float min(float a, float b) {
        if (a != a)
            return a;   // a is NaN
        if ((a == 0.0f) &&
            (b == 0.0f) &&
            (Float.floatToRawIntBits(b) == negativeZeroFloatBits)) {
            // Raw conversion ok since NaN can't map to -0.0.
            return b;
        }
        return (a <= b) ? a : b;
    }
    public static double min(double a, double b) {
        if (a != a)
            return a;   // a is NaN
        if ((a == 0.0d) &&
            (b == 0.0d) &&
            (Double.doubleToRawLongBits(b) == negativeZeroDoubleBits)) {
            // Raw conversion ok since NaN can't map to -0.0.
            return b;
        }
        return (a <= b) ? a : b;
    }
    //阶越函数，整数返回1.0负数返回-1.0
    public static double signum(double d) {
        return (d == 0.0 || Double.isNaN(d))?d:copySign(1.0, d);
    }
    public static float signum(float f) {
        return (f == 0.0f || Float.isNaN(f))?f:copySign(1.0f, f);
    }
    //双曲正弦函数
    public static double sinh(double x) {
        return StrictMath.sinh(x);
    }
    //双曲余弦函数
    public static double cosh(double x) {
        return StrictMath.cosh(x);
    }
    //双曲正切函数
    public static double tanh(double x) {
        return StrictMath.tanh(x);
    }
    //返回欧几里德范数，直角三角形的两条直角边求斜边长度
    public static double hypot(double x, double y) {
        return StrictMath.hypot(x, y);
    }
    //返回e的x次方-1，当x接近0，expm1(x) + 1的准确和比exp(x)更接近 e x 的真实结果。
    public static double expm1(double x) {
        return StrictMath.expm1(x);
    }
    //expm1函数的逆运算
    public static double log1p(double x) {
        return StrictMath.log1p(x);
    }
    //以第二个参数的符号（正或负）返回第一个参数,就是把sign的符号给magnitude
    public static double copySign(double magnitude, double sign) {
        return Double.longBitsToDouble((Double.doubleToRawLongBits(sign) &
                                        (DoubleConsts.SIGN_BIT_MASK)) |
                                       (Double.doubleToRawLongBits(magnitude) &
                                        (DoubleConsts.EXP_BIT_MASK |
                                         DoubleConsts.SIGNIF_BIT_MASK)));
    }
    public static float copySign(float magnitude, float sign) {
        return Float.intBitsToFloat((Float.floatToRawIntBits(sign) &
                                     (FloatConsts.SIGN_BIT_MASK)) |
                                    (Float.floatToRawIntBits(magnitude) &
                                     (FloatConsts.EXP_BIT_MASK |
                                      FloatConsts.SIGNIF_BIT_MASK)));
    }
    //返回第一个参数和第二个参数之间与第一个参数相邻的浮点数。如果两个参数比较起来相等，则返回第二个参数
    public static double nextAfter(double start, double direction) {
        if (Double.isNaN(start) || Double.isNaN(direction)) {
            return start + direction;
        } else if (start == direction) {
            return direction;
        } else {
            long transducer = Double.doubleToRawLongBits(start + 0.0d);
            if (direction > start) { // Calculate next greater value
                transducer = transducer + (transducer >= 0L ? 1L:-1L);
            } else  { // Calculate next lesser value
                assert direction < start;
                if (transducer > 0L)
                    --transducer;
                else
                    if (transducer < 0L )
                        ++transducer;
                    else
                        transducer = DoubleConsts.SIGN_BIT_MASK | 1L;
            }
            return Double.longBitsToDouble(transducer);
        }
    }
    public static float nextAfter(float start, double direction) {
        if (Float.isNaN(start) || Double.isNaN(direction)) {
            return start + (float)direction;
        } else if (start == direction) {
            return (float)direction;
        } else {
            int transducer = Float.floatToRawIntBits(start + 0.0f);
            if (direction > start) {// Calculate next greater value
                transducer = transducer + (transducer >= 0 ? 1:-1);
            } else  { // Calculate next lesser value
                assert direction < start;
                if (transducer > 0)
                    --transducer;
                else
                    if (transducer < 0 )
                        ++transducer;
                    else
                        transducer = FloatConsts.SIGN_BIT_MASK | 1;
            }
            return Float.intBitsToFloat(transducer);
        }
    }
    //返回在正无穷方向上与d相邻的浮点值
    public static double nextUp(double d) {
        if( Double.isNaN(d) || d == Double.POSITIVE_INFINITY)
            return d;
        else {
            d += 0.0d;
            return Double.longBitsToDouble(Double.doubleToRawLongBits(d) +
                                           ((d >= 0.0d)?+1L:-1L));
        }
    }
    public static float nextUp(float f) {
        if( Float.isNaN(f) || f == FloatConsts.POSITIVE_INFINITY)
            return f;
        else {
            f += 0.0f;
            return Float.intBitsToFloat(Float.floatToRawIntBits(f) +
                                        ((f >= 0.0f)?+1:-1));
        }
    }
    //返回在负无穷方向上与d相邻的浮点值
    public static double nextDown(double d) {
        if (Double.isNaN(d) || d == Double.NEGATIVE_INFINITY)
            return d;
        else {
            if (d == 0.0)
                return -Double.MIN_VALUE;
            else
                return Double.longBitsToDouble(Double.doubleToRawLongBits(d) +
                                               ((d > 0.0d)?-1L:+1L));
        }
    }
    public static float nextDown(float f) {
        if (Float.isNaN(f) || f == Float.NEGATIVE_INFINITY)
            return f;
        else {
            if (f == 0.0f)
                return -Float.MIN_VALUE;
            else
                return Float.intBitsToFloat(Float.floatToRawIntBits(f) +
                                            ((f > 0.0f)?-1:+1));
        }
    }
    //返回正常范围内浮点数幂为2的值
    static double powerOfTwoD(int n) {
        assert(n >= DoubleConsts.MIN_EXPONENT && n <= DoubleConsts.MAX_EXPONENT);
        return Double.longBitsToDouble((((long)n + (long)DoubleConsts.EXP_BIAS) <<
                                        (DoubleConsts.SIGNIFICAND_WIDTH-1))
                                       & DoubleConsts.EXP_BIT_MASK);
    }
    static float powerOfTwoF(int n) {
        assert(n >= FloatConsts.MIN_EXPONENT && n <= FloatConsts.MAX_EXPONENT);
        return Float.intBitsToFloat(((n + FloatConsts.EXP_BIAS) <<
                                     (FloatConsts.SIGNIFICAND_WIDTH-1))
                                    & FloatConsts.EXP_BIT_MASK);
    }
```
```java
    //???
    public static double ulp(double d) {
        int exp = getExponent(d);

        switch(exp) {
        case DoubleConsts.MAX_EXPONENT+1:       // NaN or infinity
            return Math.abs(d);

        case DoubleConsts.MIN_EXPONENT-1:       // zero or subnormal
            return Double.MIN_VALUE;

        default:
            assert exp <= DoubleConsts.MAX_EXPONENT && exp >= DoubleConsts.MIN_EXPONENT;

            // ulp(x) is usually 2^(SIGNIFICAND_WIDTH-1)*(2^ilogb(x))
            exp = exp - (DoubleConsts.SIGNIFICAND_WIDTH-1);
            if (exp >= DoubleConsts.MIN_EXPONENT) {
                return powerOfTwoD(exp);
            }
            else {
                // return a subnormal result; left shift integer
                // representation of Double.MIN_VALUE appropriate
                // number of positions
                return Double.longBitsToDouble(1L <<
                (exp - (DoubleConsts.MIN_EXPONENT - (DoubleConsts.SIGNIFICAND_WIDTH-1)) ));
            }
        }
    }
    public static float ulp(float f) {
        int exp = getExponent(f);

        switch(exp) {
        case FloatConsts.MAX_EXPONENT+1:        // NaN or infinity
            return Math.abs(f);
        case FloatConsts.MIN_EXPONENT-1:        // zero or subnormal
            return FloatConsts.MIN_VALUE;
        default:
            assert exp <= FloatConsts.MAX_EXPONENT && exp >= FloatConsts.MIN_EXPONENT;
            exp = exp - (FloatConsts.SIGNIFICAND_WIDTH-1);
            if (exp >= FloatConsts.MIN_EXPONENT) {
                return powerOfTwoF(exp);
            }
            else {
                return Float.intBitsToFloat(1 <<
                (exp - (FloatConsts.MIN_EXPONENT - (FloatConsts.SIGNIFICAND_WIDTH-1)) ));
            }
        }
    }
    public static int getExponent(float f) {
        return ((Float.floatToRawIntBits(f) & FloatConsts.EXP_BIT_MASK) >>
                (FloatConsts.SIGNIFICAND_WIDTH - 1)) - FloatConsts.EXP_BIAS;
    }
    public static int getExponent(double d) {
        return (int)(((Double.doubleToRawLongBits(d) & DoubleConsts.EXP_BIT_MASK) >>
                      (DoubleConsts.SIGNIFICAND_WIDTH - 1)) - DoubleConsts.EXP_BIAS);
    }
    public static double scalb(double d, int scaleFactor) {
        final int MAX_SCALE = DoubleConsts.MAX_EXPONENT + -DoubleConsts.MIN_EXPONENT +
                              DoubleConsts.SIGNIFICAND_WIDTH + 1;
        int exp_adjust = 0;
        int scale_increment = 0;
        double exp_delta = Double.NaN;
        if(scaleFactor < 0) {
            scaleFactor = Math.max(scaleFactor, -MAX_SCALE);
            scale_increment = -512;
            exp_delta = twoToTheDoubleScaleDown;
        }
        else {
            scaleFactor = Math.min(scaleFactor, MAX_SCALE);
            scale_increment = 512;
            exp_delta = twoToTheDoubleScaleUp;
        }
        int t = (scaleFactor >> 9-1) >>> 32 - 9;
        exp_adjust = ((scaleFactor + t) & (512 -1)) - t;
        d *= powerOfTwoD(exp_adjust);
        scaleFactor -= exp_adjust;
        while(scaleFactor != 0) {
            d *= exp_delta;
            scaleFactor -= scale_increment;
        }
        return d;
    }
    public static float scalb(float f, int scaleFactor) {
        final int MAX_SCALE = FloatConsts.MAX_EXPONENT + -FloatConsts.MIN_EXPONENT +
                              FloatConsts.SIGNIFICAND_WIDTH + 1;

        scaleFactor = Math.max(Math.min(scaleFactor, MAX_SCALE), -MAX_SCALE);
        return (float)((double)f*powerOfTwoD(scaleFactor));
    }
```
