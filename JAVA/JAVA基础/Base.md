# 基础数据类型


| |boolean |byte |char |short| int |float |long| double|
|:---|:---|:---|:---|:---|:---|:---|:---|:----|
|位数(位) |1| 8| 16| 16| 32| 32| 64| 64|
|封装类型 |Boolean| Byte| Char| Short| Integer| Float |Long  |Double|

        
***
# Object的方法
1. public native int hashCode()
2. public boolean equals(Object obj)
3. protected native Object clone() throws CloneNotSupportedException
4. public String toString()
5. public final native Class<?> getClass()
6. protected void finalize() throws Throwable {}
7. public final native void notify()
8. public final native void notifyAll()
9. public final native void wait(long timeout) throws InterruptedException
10. public final void wait(long timeout, int nanos) throws 
11. InterruptedException
12. public final void wait() throws InterruptedException
***
# 浅拷贝：
拷贝对象和原始对象的引用类型引用同一个对象。
深拷贝:拷贝对象和原始对象的引用类型引用不同对象。
***
# 泛型
> https://www.cnblogs.com/Blue-Keroro/p/8875898.html