### Number

 > Number是一个抽象类,是`BigDecimal,BigInteger,Byte,Double,Float,Integer,Long,Short`的超类,它的实现类必须提供将表示的数值转换为`byte,double,float,int,long`和`short`六大基本类型的方法。
    
```java
public abstract class Number implements 
java.io.Serializable {

    public abstract int intValue();
    
    public abstract long longValue();

    public abstract float floatValue();

    public abstract double doubleValue();

    public byte byteValue() {
        return (byte)intValue();
    }
    
    public short shortValue() {
        return (short)intValue();
    }

}
```
  从源码可以看到,转换为`byte`和`short`类型的方法,如果子类不重写,其实是先转换成`int`类型值再强转,所以可能会有精度丢失问题。

