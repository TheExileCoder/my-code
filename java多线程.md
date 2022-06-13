```java
//Future和callable
package com.java;
import java.util.concurrent.*;
import java.util.Date;
import java.util.List;
import java.util.ArrayList;


class ThreadDemo implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
        // 模拟计算需要⼀秒
        Thread.sleep(1000);
        return 2;
    }
    public static void main(String[] args){

        ExecutorService executor = Executors.newCachedThreadPool();
            ThreadDemo task = new ThreadDemo();
        Future<Integer> result = executor.submit(task);
        // 注意调⽤get⽅法会阻塞当前线程，直到得到结果。
        // 所以实际编码中建议使⽤可以设置超时时间的重载get⽅法。
        try{
            System.out.println(result.get());
        }
        catch (InterruptedException e){
            throw new IllegalStateException(e.getMessage(), e);
        }
        catch (ExecutionException e){
            throw new IllegalStateException(e.getMessage(), e);
        }
    }
}
```


```java
//FutureTask
package com.java;
import java.util.concurrent.*;
import java.util.Date;
import java.util.List;
import java.util.ArrayList;


class ThreadDemo implements Callable<Integer>{
    @Override
    public Integer call() throws Exception {
        // 模拟计算需要⼀秒
        Thread.sleep(1000);
        return 2;
    }
    public static void main(String[] args){
        // 使用
        ExecutorService executor = Executors.newCachedThreadPool();
        FutureTask<Integer> futureTask = new FutureTask<>(new ThreadDemo());
        executor.submit(futureTask);


        try{
            System.out.println(futureTask.get());

        }
        catch (InterruptedException e){
            throw new IllegalStateException(e.getMessage(), e);
        }
        catch (ExecutionException e){
            throw new IllegalStateException(e.getMessage(), e);
        }
        System.exit(0);
    }
}
```


```java
//线程组
package com.java;
import java.util.concurrent.*;
import java.util.Date;
import java.util.List;
import java.util.ArrayList;


class ThreadDemo {
    public static void main(String[] args) {
        Thread testThread = new Thread(() -> {
            System.out.println("testThread当前线程组名字：" +
                    Thread.currentThread().getThreadGroup().getName());
            System.out.println("testThread线程名字：" +
                    Thread.currentThread().getName());
        });

        testThread.start();
        System.out.println("执行main方法线程名字：" + Thread.currentThread().getName());
    }
}
```

```java
package com.java;
import java.util.concurrent.*;
import java.util.Date;
import java.util.List;
import java.util.ArrayList;
//对象锁

class ThreadDemo {
    private static Object lock = new Object();

    static class ThreadA implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 0; i < 100; i++) {
                    System.out.println("Thread A " + i);
                }
            }
        }
    }

    static class ThreadB implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 0; i < 100; i++) {
                    System.out.println("Thread B " + i);
                }
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new Thread(new ThreadA()).start();
        Thread.sleep(10);
        new Thread(new ThreadB()).start();
    }
}
```

```java
package com.java;
import java.util.concurrent.*;
import java.util.Date;
import java.util.List;
import java.util.ArrayList;
//等待，通知机制

class ThreadDemo {
    private static Object lock = new Object();

    static class ThreadA implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 0; i < 5; i++) {
                    try {
                        System.out.println("ThreadA: " + i);
                        lock.notify();
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                lock.notify();
            }
        }
    }

    static class ThreadB implements Runnable {
        @Override
        public void run() {
            synchronized (lock) {
                for (int i = 0; i < 5; i++) {
                    try {
                        System.out.println("ThreadB: " + i);
                        lock.notify();
                        lock.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                lock.notify();
            }
        }
    }

    public static void main(String[] args) throws InterruptedException {
        new Thread(new ThreadA()).start();
        Thread.sleep(1000);
        new Thread(new ThreadB()).start();
    }
}
```
