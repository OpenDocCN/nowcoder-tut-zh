# 第三章 第 21 节 搜狐畅游 2020 秋招笔试真题

> 原文：[`www.nowcoder.com/tutorial/10028/efe7e5037cb141679a9bf5daafaf27f9`](https://www.nowcoder.com/tutorial/10028/efe7e5037cb141679a9bf5daafaf27f9)

# 搜狐畅游 2020 秋招笔试真题

![](img/ea431648756850ef5199f98338df0c3c.png)

## 1、多线程打印

【题目描述】创建两个线程，其中一个输出 1-52，另外一个输出 A-Z。输出格式要求：12A 34B 56C 78D ...

**输入描述**

1、线程 1 打印 1-52 的数字
2、线程 2 打印 26 个字母

**输出描述**

每打印两个数字后打印一个字母，直到最终所有数字和字母都打印完成

**【参考代码】**

```cpp
public class Main {
    public static void main(String[] args) {
        Object object = new Object();
        new Thread(new Number(object)).start();
        new Thread(new Character(object)).start();
    }
}

class Number implements Runnable {
    private Object object;

    public Number(Object object) {
        this.object = object;
    }

    @Override
    public void run() {
        synchronized (object) {
            for (int i = 1; i < 53; i++) {
                if (i > 1 && i % 2 == 1) {
                    System.out.print(" ");
                }
                System.out.print(i);
                if (i % 2 == 0) {
                    object.notifyAll();
                    try {
                        object.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
}

class Character implements Runnable {
    private Object object;

    public Character(Object object) {
        this.object = object;
    }

    @Override
    public void run() {
        synchronized (object) {
            for (char i = 'A'; i <= 'Z'; i++) {
                System.out.print(i);
                object.notifyAll();
                if (i < 'Z') {
                    try {
                        object.wait();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
}
```

 ## 2、实现并发条件下的累加器

【题目描述】创建多个线程，同时运行，对同一累加计算器的累加操作，累加器初始值为 0，累加步长为 1，每次累加操作后输出累加器的当前值，累加器加到 n 时结束。

**输入描述**

累加器中止时的数值

**输出描述**

输出 1 到 n 的连续数值

**示例 1**

**输入**

```cpp
5
```

**输出**

```cpp
1
2
3
4
5
```

**【参考代码】**

```cpp
import java.util.Scanner;
import java.util.concurrent.atomic.AtomicInteger;

public class Main extends Thread {
    public Main() {
    }

    private static AtomicInteger counter = new AtomicInteger(0);
    private static int uper = 0;

    public void run() {
        while (true) {
            if (counter.get() >= uper) {
                return;
            }
            System.out.println(counter.addAndGet(1));
        }
    }

    public static void main(String[] args) {
        Main c1 = new Main();
        Main c2 = new Main();
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            Main.uper = scanner.nextInt();
            c1.run();
            c2.run();
        }
```