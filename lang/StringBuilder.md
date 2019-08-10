### StringBuilder
`StringBuilder`和`StringBuffer`都是可变字符串，主要用于对字符串的拼接操作，都继承了`AbstractStringBuilder`类,`AbstractStringBuilder`是一个抽象类，封装了一个`char`数组和一些操作字符串的方法
```Java
abstract class AbstractStringBuilder implements Appendable, CharSequence {
    char[] value;
    int count;
    //最大长度
    private static final int MAX_ARRAY_SIZE = Integer.MAX_VALUE - 8;
    //获取长度
    public int length() {
        return count;
    }
    //获取容量
    public int capacity() {
        return value.length;
    }
    //扩容增长策略
    private int newCapacity(int minCapacity) {
        // overflow-conscious code
        int newCapacity = (value.length << 1) + 2;
        if (newCapacity - minCapacity < 0) {
            newCapacity = minCapacity;
        }
        return (newCapacity <= 0 || MAX_ARRAY_SIZE - newCapacity < 0)
            ? hugeCapacity(minCapacity)
            : newCapacity;
    }
    //调整缓冲区大小，去掉尾部空格
    public void trimToSize() {
        if (count < value.length) {
            value = Arrays.copyOf(value, count);
        }
    }
    //设置缓冲区大小，尾部补空格
    public void setLength(int newLength) {
        if (newLength < 0)
            throw new StringIndexOutOfBoundsException(newLength);
        ensureCapacityInternal(newLength);
        if (count < newLength) {
            Arrays.fill(value, count, newLength, '\0');
        }
        count = newLength;
    }
    //返回指定位置字符
    public char charAt(int index) {
        if ((index < 0) || (index >= count))
            throw new StringIndexOutOfBoundsException(index);
        return value[index];
    }
    //将str添加到value数组尾部
    public AbstractStringBuilder append(String str) {
        if (str == null)
            return appendNull();
        int len = str.length();
        ensureCapacityInternal(count + len);
        str.getChars(0, len, value, count);
        count += len;
        return this;
    }
    //删除指定范围字符
    public AbstractStringBuilder delete(int start, int end) {
        if (start < 0)
            throw new StringIndexOutOfBoundsException(start);
        if (end > count)
            end = count;
        if (start > end)
            throw new StringIndexOutOfBoundsException();
        int len = end - start;
        if (len > 0) {
            System.arraycopy(value, start+len, value, start, count-end);
            count -= len;
        }
        return this;
    }
    //替换指定范围字符
    public AbstractStringBuilder replace(int start, int end, String str) {
        if (start < 0)
            throw new StringIndexOutOfBoundsException(start);
        if (start > count)
            throw new StringIndexOutOfBoundsException("start > length()");
        if (start > end)
            throw new StringIndexOutOfBoundsException("start > end");
        if (end > count)
            end = count;
        int len = str.length();
        int newCount = count + len - (end - start);
        ensureCapacityInternal(newCount);
        //先把end位置及之后的字符拷贝到start+len的位置再替换
        System.arraycopy(value, end, value, start + len, count - end);
        str.getChars(value, start);
        count = newCount;
        return this;
    }
    //截取字符串
    public String substring(int start, int end) {
        if (start < 0)
            throw new StringIndexOutOfBoundsException(start);
        if (end > count)
            throw new StringIndexOutOfBoundsException(end);
        if (start > end)
            throw new StringIndexOutOfBoundsException(end - start);
        return new String(value, start, end - start);
    }
    //插入字符串到指定位置
    public AbstractStringBuilder insert(int offset, String str) {
        if ((offset < 0) || (offset > length()))
            throw new StringIndexOutOfBoundsException(offset);
        if (str == null)
            str = "null";
        int len = str.length();
        ensureCapacityInternal(count + len);
        //先将offset位置及之后的字符拷贝到offset+len位置
        System.arraycopy(value, offset, value, offset + len, count - offset);
        str.getChars(value, offset);
        count += len;
        return this;
    }
    //翻转字符串
    public AbstractStringBuilder reverse() {
        boolean hasSurrogates = false;
        int n = count - 1;
        for (int j = (n-1) >> 1; j >= 0; j--) {
            int k = n - j;
            char cj = value[j];
            char ck = value[k];
            value[j] = ck;
            value[k] = cj;
            if (Character.isSurrogate(cj) ||
                Character.isSurrogate(ck)) {
                hasSurrogates = true;
            }
        }
        if (hasSurrogates) {
            reverseAllValidSurrogatePairs();
        }
        return this;
    }
    public abstract String toString();
}
```
```java
public final class StringBuilder
    extends AbstractStringBuilder
    implements java.io.Serializable, CharSequence{}
```
`StringBuilder`类中很多方法都是调用的`AbstractStringBuilder`类的方法
```Java
    //默认长度为16
    public StringBuilder() {
        super(16);
    }
    //如果传入一个字符串或字符序列，默认容量为传入参数的长度加上16
    public StringBuilder(String str) {
        super(str.length() + 16);
        append(str);
    }
    //appe、delete、replace、insert、indexOf、reverse方法都是调用的父类的实现
    public StringBuilder append(String str) {
        super.append(str);
        return this;
    }
    public StringBuilder delete(int start, int end) {
        super.delete(start, end);
        return this;
    }
    public StringBuilder replace(int start, int end, String str) {
       super.replace(start, end, str);
       return this;
   }
   public StringBuilder insert(int offset, String str) {
       super.insert(offset, str);
       return this;
   }
   public int indexOf(String str) {
        return super.indexOf(str);
    }
    public StringBuilder reverse() {
        super.reverse();
        return this;
    }
    //toString方法
    public String toString() {
        // Create a copy, don't share the array
        return new String(value, 0, count);
    }
    // 序列化与反序列化
    static final long serialVersionUID = 4383685877147921099L;
    private void writeObject(java.io.ObjectOutputStream s)
        throws java.io.IOException {
        s.defaultWriteObject();
        s.writeInt(count);
        s.writeObject(value);
    }
    private void readObject(java.io.ObjectInputStream s)
        throws java.io.IOException, ClassNotFoundException {
        s.defaultReadObject();
        count = s.readInt();
        value = (char[]) s.readObject();
    }
```
