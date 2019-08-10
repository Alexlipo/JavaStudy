### StringBuffer
`StringBuffer`和`StringBuilder`一样都继承了`AbstractStringBuilder`类,区别是`StringBuffer`的方法都加了一个`synchronized`关键字，所以`StringBuffer`类是线程安全的，但是性能比`StringBuilder`稍差
```java
public final class StringBuffer
        extends AbstractStringBuilder
        implements java.io.Serializable, CharSequence
    {
      // toString返回的最后一个值的缓存。每当修改StringBuffer时清除
      private transient char[] toStringCache;
      // 其他方法和StringBuilder一样都是调用的父类AbstractStringBuilder的实现
      public synchronized int length() {
        return count;
    }
    }
```
