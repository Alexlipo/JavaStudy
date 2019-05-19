### Boolean
> `Boolean`是基础类型`boolean`的包装类。
类的声明
```java
    public final class Boolean implements java.io.Serializable,Comparable<Boolean>{}
```
`Boolean`类的属性
```java
    //对应的boolean类型值true和false
    public static final Boolean TRUE = new Boolean(true);
    public static final Boolean FALSE = new Boolean(false);
    //类实例
    @SuppressWarnings("unchecked")
    public static final Class<Boolean> TYPE = (Class<Boolean>) Class.getPrimitiveClass("boolean");
    //Boolean类型对象的值
    private final boolean value;
    //序列化
    private static final long serialVersionUID = -3665804199014368530L;
```
`Boolean`类的方法
```java
    //构造方法，可以传一个boolean类型的值也可以传字符串类型
    public Boolean(boolean value) {
        this.value = value;
    }
    public Boolean(String s) {
       this(parseBoolean(s));
   }
   //将字符串转成boolean类型值，如果字符串不为空且内容为忽视大小写的"true"，返回true，否则返回false
   public static boolean parseBoolean(String s) {
        return ((s != null) && s.equalsIgnoreCase("true"));
    }
    //返回Boolean对象所包装的boolean值
    public boolean booleanValue() {
        return value;
    }
    //返回一个包含传入boolean值的Boolean对象
    public static Boolean valueOf(boolean b) {
        return (b ? TRUE : FALSE);
    }
    //返回传入String调用parseBoolean方法之后返回的值得Boolean对象
    public static Boolean valueOf(String s) {
        return parseBoolean(s) ? TRUE : FALSE;
    }
    //toString方法
    public static String toString(boolean b) {
        return b ? "true" : "false";
    }
    public String toString() {
        return value ? "true" : "false";
    }
    //hashCode和equals方法
    @Override
    public int hashCode() {
        return Boolean.hashCode(value);
    }
    public static int hashCode(boolean value) {
        return value ? 1231 : 1237;
    }
    public boolean equals(Object obj) {
        if (obj instanceof Boolean) {
            return value == ((Boolean)obj).booleanValue();
        }
        return false;
    }
    //如果系统参数名为传入字符串的参数存在且值为"true"字符串，返回true
    public static boolean getBoolean(String name) {
        boolean result = false;
        try {
            result = parseBoolean(System.getProperty(name));
        } catch (IllegalArgumentException | NullPointerException e) {
        }
        return result;
    }
    //conpare方法
    public int compareTo(Boolean b) {
        return compare(this.value, b.value);
    }
    public static int compare(boolean x, boolean y) {
        return (x == y) ? 0 : (x ? 1 : -1);
    }
    //逻辑运算
    public static boolean logicalAnd(boolean a, boolean b) {
        return a && b;
    }
    public static boolean logicalOr(boolean a, boolean b) {
        return a || b;
    }
    public static boolean logicalXor(boolean a, boolean b) {
        return a ^ b;
    }
```
