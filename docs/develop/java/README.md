# Java必备知识总结

------

> * 整理知识，学习笔记
> * 发布日记，杂文，所见所想
> * 撰写发布技术文稿（代码支持）
> * 撰写发布学术论文（LaTeX 公式支持）

## 常见的五种单例模式
### 1、饿汉式 (线程安全，调用效率高，但是不能延时加载)
```
public class Singleton1 {
   /*
    * 饿汉式是在声明的时候就已经初始化Singleton1,确保了对象的唯一性
    *
    * 声明的时候就初始化对象会浪费不必要的资源
    **/
    private static Singleton1 instance = new Singleton1();
    
    private Singleton1() {
    }
    
    // 通过静态方法或枚举返回单例对象
    public Singleton1 getInstance() {
        return instance;
    }
}
```
>一上来就把单例对象给创建出来了，要用的时候直接返回即可，这种可以说是单例模式最简单的一种实现方式，但是问题也比较明显，单例在还没有使用的时候，初始化已经完成了，也就是说，如果程序从有到尾都没有使用这个单例的话，单例的对象还是会创建，这就造成了不必要资源浪费，多以不推荐使用这种方式。
### 2、懒汉式（线程安全，调用效率不高，但是能延迟加载）
```
public class SingletonDemo2 {
     
    //类初始化时，不初始化这个对象(延时加载，真正用的时候再创建)
    private static SingletonDemo2 instance;
     
    //构造器私有化
    private SingletonDemo2(){}
     
    //方法同步，调用效率低
    public static synchronized SingletonDemo2 getInstance(){
        if(instance==null){
            instance=new SingletonDemo2();
        }
        return instance;
    }
}
```
### 3、Double CheckLock 双重锁判断机制 DCL 也就是（由于JVM底层模型原因，偶尔会出问题，不建议使用）
```
public class Singleton3 {
    private static Singleton3 instance;
    
    private Singleton3() {}
    
    /**
     * 两次判空，第一次判空是为了不必要的同步，第二次判空为了在instance 为 null 的情况下创建实例
     * 既保证了线程安全且单例对象初始化后调用getInstance又不会进行同步锁判断
     * 
     * 优点:资源利用率高,效率高
     * 缺点:第一次加载稍慢，由于java处理器允许乱序执行，偶尔会失败
     *
     * @return
     */
    public static Singleton3 getInstance() {
        if (instance == null) {
            synchronized (Singleton3.class) {
                if (instance == null) {
                    instance = new Singleton3();
                }
            }
        }
        return instance;
    }
}
```
### 4、★ 静态内部类实现模式 (线程安全，调用效率高，可以延迟加载)
```
public class Singleton4 {
   /*
    * 当第一次加载Singleton类时并不会初始化SINGLRTON，只有第一次调用getInstance方法的时候才会初始化 SINGLETON
    * 第一次调用getInstance 方法的时候虚拟机才会加载SingletonHoder类，这种方式不仅能够保证线程安全，也能够保证对象的唯一
    * 还延迟了单例的实例化，所有推荐使用这种方式
    **/
    private Singleton4() {}
    
    public Singleton4 getInstance() {
        return SingletonHolder.SINGLETON;
    }
    
    private static class SingletonHolder {
        private static final Singleton4 SINGLETON = new Singleton4();
    }
}
```
### 5、枚举类（线程安全，调用效率高，不能延时加载，可以天然的防止反射和反序列化调用）