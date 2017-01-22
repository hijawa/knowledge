# 设计模式的疑惑与解答
## 单例模式
### 1. 单例对象常驻内存，JVM垃圾回收机制不会回收吗？Spring框架管理的单例bean是怎么解决这个问题的？  
   看了CSDN这篇博客，从JVM的回收实例和类(Class的实例)的角度分析了单例对象不会被回收  
   http://blog.csdn.net/zhengzhb/article/details/7331354     
   又看了知乎上这个问题，R大的回答，从JVM最底层，大道至简的分析了对象回收机制。  
   "无论多长时间不用，只要您所说的对象有被一个活着的强引用所引用，JVM的GC就奈它不何——无法回收。"  
   https://www.zhihu.com/question/51014592
   建议，从《深入理解Java虚拟机》这本书入手，从根源上回答这类问题。  
   
### 2. 单例对象如何保证线程安全来着？  
   看了CSDN这篇博客，把各种创建"单例"的方式都列举了，不是线程安全的单例也能叫做单例？  
   http://blog.csdn.net/cselmu9/article/details/51366946  
   用的最多的就是"饿汉式"创建方式，创建实例比较慢的话，系统启动速度慢。  
   "懒汉式"创建方式，保证线程安全的做法是DCL，延迟加载，系统启动速度快 
   ```
     public class MySingleton {  

      //使用volatile关键字保其可见性  
      volatile private static MySingleton instance = null;  

      private MySingleton(){}  

      public static MySingleton getInstance() {  
          try {    
              if(instance != null){//懒汉式   

              }else{  
                  //创建实例之前可能会有一些准备性的耗时工作   
                  Thread.sleep(300);  
                  synchronized (MySingleton.class) {  
                      if(instance == null){//二次检查  
                          instance = new MySingleton();  
                      }  
                  }  
              }   
          } catch (InterruptedException e) {   
              e.printStackTrace();  
          }  
          return instance;  
      }  
    }  
   ```
### 3. 如何扩展单例模式，使得产生固定的多于一个对象？  
  - 修改static 属性为list，在static 代码块里初始化 n 个  
  - 修改获取对象的方法，随机或者按照某种规则获取list中的某一个或几个   
  
## 工厂模式  
### 1. 通过工厂模式，如何实现限制某一产品类的最大实例化数量？  
  - 同单例模式扩展产生固定的多个对象。  
  
