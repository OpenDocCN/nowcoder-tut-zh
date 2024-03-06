# 第三章 第 3 节 进阶知识点与思考

> 原文：[`www.nowcoder.com/tutorial/10077/cc3ac64eb0bc4fb1b33b107bfdfe40cf`](https://www.nowcoder.com/tutorial/10077/cc3ac64eb0bc4fb1b33b107bfdfe40cf)

## 2.3 进阶知识点与思考

### 2.3.1 可以被破坏的双亲委派模型

双亲委派模型一种有效的类加载模型，绝大多数类加载器采用这一模型实现，但是仍有一些特殊情况导致模型被破坏。

由于在双亲委派模型实现前，JDK 已经提供自定义类加载的实现，导致双亲委派模型存在被破坏的机会。在使用自定义类加载器时，通过在 loadClass()中编写加载逻辑实现。为了避免自定义类加载器使用上述方式，JDK 在 java.lang.ClassLoader 中添加了 findClass()方法，并要求用户重写这一方法。

双亲委派模型的第二次被破坏是由于其本身的性质。如果基础类在被加载时又要调用回用户的代码，启动类加载器将无法识别这些代码。为了解决这个问题，引入了线程上下文加载器，这个类加载器可以通过 java.lang.Thread 类的 setContextClassLoader()方法进行设置，如果创建时还未设置，他将从父线程中继承一个，如果在应用程序的全局范围内都没有设置过，那这个类加载器默认就是应用程序类加载器。这里简单介绍下 JDBC 中破坏双亲委派机制的相关内容。

为什么 JDBC 要破坏双亲委派模型？这是因为在一些场景下下父类加载器需要委托子类加载器去加载.class 文件。Driver 接口(DriverManager)仅仅在 JDK 中存在定义，而没有具体实现。具体实现由各个数据库厂商自行提供，但是不能把所有厂商提供的驱动代码全放到 JDK 的 lib 目录吧。例如如下代码：

```cpp
Class clz = Class.forName("java.sql.Driver");
Driver d = (Driver) clz.newInstance();
```

BootstrapClassLoader 将尝试加载，但是 java.sql.Driver 仅仅是一个接口，无法进行实例化，所以执行必然失败。如果一定执行成功，可以将 mysql-connector-java.jar 丢到 JDK 的 lib 目录下，但是这种方式过于硬核，扩展性和优雅性不足。DriverManager 的加载可由 BootstrapClassLoader 实现，但是数据库厂商提供的诸如 mysql-connector-.jar 无法由 BootstrapClassLoader 加载。而通过线程上下文类加载器可实现将 BootstrapClassLoader 无法识别或加载的类转交 AppClassLoader 进行加载。大家可以自己再执行下面代码观察结果。

```cpp
public static void main(String[] args){
    Connection connection = null;
    try {
        connection = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/test", "xxx", "xxxxx");
    } catch (SQLException e) {
        e.printStackTrace();
    }
    System.out.println(connection.getClass().getClassLoader());
    System.out.println(Thread.currentThread().getContextClassLoader());
    System.out.println(Connection.class.getClassLoader());
}
```

下面我们再关注一下 getConnection()的实现

```cpp
private static Connection getConnection(
    String url, java.util.Properties info, Class<?> caller) throws SQLException {

    ClassLoader callerCL = caller != null ? caller.getClassLoader() : null;
    // 同步块处理
    synchronized(DriverManager.class) {
        // callerCL 为空说明时启动类加载器，但是启动类加载器不能识别 rt.jar 之外的类
        // 为了完成加载，可以从线程上下文中获取应用类加载器
        if (callerCL == null) {
          // 获取线程上下文中类加载器
            callerCL = Thread.currentThread().getContextClassLoader();
        }
    }

    if(url == null) {
        throw new SQLException("The url cannot be null", "08001");
    }
    println("DriverManager.getConnection(\"" + url + "\")");
    SQLException reason = null;

    for(DriverInfo aDriver : registeredDrivers) {
          // isDriverAllowed 中实现 mysql-connector-java.jar 的加载
        if(isDriverAllowed(aDriver.driver, callerCL)) {
            try {
                println("    trying " + aDriver.driver.getClass().getName());
                Connection con = aDriver.driver.connect(url, info);
                if (con != null) {
                    // Success!
                    println("getConnection returning " + aDriver.driver.getClass().getName());
                    return (con);
                }
            } catch (SQLException ex) {
                if (reason == null) {
                    reason = ex;
                }
            }

        } else {
            println("    skipping: " + aDriver.getClass().getName());
        }

    }
    if (reason != null)    {
        println("getConnection failed: " + reason);
        throw reason;
    }

    println("getConnection: no suitable driver found for "+ url);
    throw new SQLException("No suitable driver found for "+ url, "08001");
}

private static boolean isDriverAllowed(Driver driver, ClassLoader classLoader) {
    boolean result = false;
    if(driver != null) {
        Class<?> aClass = null;
        try {
            aClass =  Class.forName(driver.getClass().getName(), true, classLoader);
        } catch (Exception ex) {
            result = false;
        }

         result = ( aClass == driver.getClass() ) ? true : false;
    }
    return result;
}
```

双亲委派模型的第三次被破坏是由于动态性变态追求导致，实现动态性的好处例如代码热替换、模块热部署。动态性固然有许多好处，但是往往会在性能或其他场景上存在一定缺陷。OSGi 是 Java 模块化标准，允许自定义类加载器，并且每一个程序模块都有一个类加载器。在 JDK9 之前，基础类集中在 rt.jar 包内。然而过度集中缺违反了单一职责原则，这将导致编译时会将很多无用的类也一并打包。JDK9 中引入了模块化的思想，对 rt.jar 中的模块进行解耦，分为数十个模块单独管理。编译时也只引入用到的模块。下面以一段伪代码对 JDK9 中的加载过程进行描述：

```cpp
Class<?> c = findLoadedClass(cn);
  if (c == null) {
     // 定位类所属模块
     LoadedModule loadedModule = findLoadedModule(cn);
     if (loadedModule != null) {
        // 获取所属模块的类加载器
        BuiltinClassLoader loader = loadedModule.loader();
        // 执行类加载
        c = findClassInModuleOrNull(loadedModule, cn);
     } else {
        // 降级执行双亲委派
     if (parent != null) {
       c = parent.loadClassOrNull(cn);
     }
   }
```

显然，双亲委派在 JDK9 中成为了一种后备加载方式。这种模块化方式应用在类加载场景更高效，也可以减少不必要的打包。这里仅简单对 OSGi、模块化系统进行介绍，详细内容感兴趣的同学可以自行深入理解，例如深入理解 OSGi:Equinox 原理、应用与最佳实践。

### 2.3.2 类加载部分源码简析

本节对类加载涉及的部分源码进行分析，不涉及 C/C++实现的 Bootstrap 层类加载器。

首先通过代码打印出各个类加载器：

```cpp
public class Application {

    public static void main(String[] args) {

        Application app = new Application();
        ClassLoader loader = app.getClass().getClassLoader();

        System.out.println(loader);
        System.out.println(loader.getParent());
        System.out.println(loader.getParent().getParent());
        System.out.println(ClassLoader.getSystemClassLoader());
    }
}
// 代码输出
sun.misc.Launcher$AppClassLoader@18b4aac2
sun.misc.Launcher$ExtClassLoader@61bbe9ba
null
sun.misc.Launcher$AppClassLoader@18b4aac2
```

通过输出我们不难发现，AppClassLoader 和 ExtClassLoader 都是类 Launcher 类下的静态内部类。这个 Launcher 类是 Java 程序执行的入口。启动 Java 应用时首先创建 Launcher 类，并在创建时准备应用程序运行中需要的类加载器。下面我们先对 ClassLoader 类的定义进行分析。

```cpp
public abstract class ClassLoader {
    ......
}
```

ClassLoader 被定义为抽象类，没有继承或实现接口。ClassLoader 有三个构造方法：

```cpp
// The class loader for the system
private static ClassLoader scl;
// Set to true once the system class loader has been set
private static boolean sclSet;

// 核心构造方法
private ClassLoader(Void unused, ClassLoader parent) {
    this.parent = parent;
    if (ParallelLoaders.isRegistered(this.getClass())) {
        parallelLockMap = new ConcurrentHashMap<>();
        package2certs = new ConcurrentHashMap<>();
        assertionLock = new Object();
    } else {
        // no finer-grained lock; lock on the classloader instance
        parallelLockMap = null;
        package2certs = new Hashtable<>();
        assertionLock = this;
    }
}

protected ClassLoader(ClassLoader parent) {
    this(checkCreateClassLoader(), parent);
}

protected ClassLoader() {
    // 把系统默认的类加载器作为父加载器
    this(checkCreateClassLoader(), getSystemClassLoader());
}

public static ClassLoader getSystemClassLoader() {
    initSystemClassLoader();
    if (scl == null) {
        return null;
    }
    // 安全校验，判断行为是否对系统产生危害
    SecurityManager sm = System.getSecurityManager();
    if (sm != null) {
        checkClassLoaderPermission(scl, Reflection.getCallerClass());
    }
    return scl;
}
// 注意看这个方法被 synchronized 修饰
private static synchronized void initSystemClassLoader() {
    // ClassLoader 是否被设置过
    if (!sclSet) {
        if (scl != null)
            throw new IllegalStateException("recursive invocation");
        // 拿到 Launcher 对象
        sun.misc.Launcher l = sun.misc.Launcher.getLauncher();
        if (l != null) {
            Throwable oops = null;
            // 把 scl 设置为 AppClassLoader
            scl = l.getClassLoader();
            try {
                scl = AccessController.doPrivileged(
                    new SystemClassLoaderAction(scl));
            } catch (PrivilegedActionException pae) {
                oops = pae.getCause();
                if (oops instanceof InvocationTargetException) {
                    oops = oops.getCause();
                }
            }
            if (oops != null) {
                if (oops instanceof Error) {
                    throw (Error) oops;
                } else {
                    // wrap the exception
                    throw new Error(oops);
                }
            }
        }
        sclSet = true;
    }
}
```

上面对 ClassLoader 的结构性定义以及初始化进行了介绍。下面我们来看看双亲委派是如何从代码层面实现的。

```cpp
public Class<?> loadClass(String name) throws ClassNotFoundException {
    return loadClass(name, false);
}

protected Class<?> loadClass(String name, boolean resolve) throws ClassNotFoundException {
    // 使用了同步块保证仅加载一次
    synchronized (getClassLoadingLock(name)) {
        // 先检查是否已经加载过
        Class<?> c = findLoadedClass(name);
        if (c == null) {
            long t0 = System.nanoTime();
            try {
                if (parent != null) {
                    // 说明有爹，可以继续向上委派
                    c = parent.loadClass(name, false);
                } else {
                    // 到头了，我现在是上帝，去委派 BootstrapClassLoader 加载
                    c = findBootstrapClassOrNull(name);
                }
            } catch (ClassNotFoundException e) {
                //非空的父加载器找不到类会抛出异常并被 catch 住
            }
            // 走到这里说明已经从父加载器返回(BootstrapClassLoader 除外)，可能遇到两类结果
            // 1\. 父加载器已经完成加载
            // 2\. 需要自己尝试加载
            if (c == null) {
                // 本层类加载器尝试自己加载
                long t1 = System.nanoTime();C
                c = findClass(name);
                sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                sun.misc.PerfCounter.getFindClasses().increment();
            }
        }
        if (resolve) {
            resolveClass(c);
        }
        return c;
    }
}

protected Object getClassLoadingLock(String className) {
    Object lock = this;
    if (parallelLockMap != null) {
        Object newLock = new Object();
        lock = parallelLockMap.putIfAbsent(className, newLock);
        if (lock == null) {
            lock = newLock;
        }
    }
    return lock;
}
```

双亲委派的上层实现不过寥寥数十行，但是却完成了复杂的加载逻辑。上述代码仅对部分方法进行展示，感兴趣的同学可以自己查看调用链，遇到 native 修饰的方法即可终止。

### 2.3.3 自定义类加载器 Demo

口说无凭，不是实操一下。下面给出一个自定义类加载器的 Demo 大家可以在此基础上进行扩展。首先先准备一个.class 文件。Java 代码如下：

```cpp
public class Application {

    private int age;

    private int id;

    private String name;

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return age + id + name;
    }

    public void print() {
        System.out.println(this.toString());
    }
}
```

随后在命令行执行 javac Application.java 会在.java 同级目录下生成一个.class 文件，它就是我们要加载的目标。

```cpp
public class DIYClassLoader extends ClassLoader{

    private String path;

    public DIYClassLoader(String path) {
        this.path = path;
    }

    /**
     * 重写 findClass 方法
     */
    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        Class clazz = null;
        // 这里的 getClassData 方法就是我们自定义加载逻辑
        byte[] classData = getData(name);
        if (classData == null || classData.length == 0) {
            throw new ClassNotFoundException();
        }
        //调用 define()方法将 class 的字节码数组转换成 Class 类的实例
        clazz = defineClass(name, classData, 0, classData.length);
        return clazz;
    }

    private byte[] getData(String name) {

        FileInputStream inputStream = null;
        try {
            String filePath = path + "/" + name.replace(".", "/") + ".class";
            inputStream = new FileInputStream(filePath);
            byte[] buffer = new byte[2048];
            int length = -1;
            ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
            while ((length = inputStream.read(buffer)) != -1) {
                outputStream.write(buffer, 0, length);
            }
            return outputStream.toByteArray();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (inputStream != null) {
                try {
                    inputStream.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
        return null;
    }

    public static void main(String[] args) {
        String srcPath = ".";
        String classname = "Application";
        DIYClassLoader diyClassLoader = new DIYClassLoader(srcPath);
        try {
            Class clazz = diyClassLoader.loadClass(classname);
            // 尝试调用方法证实加载结果
            Constructor constructor = clazz.getConstructor();
            constructor.setAccessible(true);
            Object o = constructor.newInstance();
            Method print = clazz.getDeclaredMethod("print");
            print.setAccessible(true);
            print.invoke(o);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

我们在自定义类加载器时只需重写 findClass()的逻辑，而不去修改 loadClass()的逻辑以复用双亲委派模型。findClass 可以通过本地 IO 也可以通过网络加载的方式。代码不复杂，大家可以尝试使用多种方式并打印一些系统信息查看效果。

## 2.4 总结

本节内容对“类加载”进行了介绍，其中的重点是类加载的**过程**以及**类加载器**。在类加载过程中，一些子流程及其细节不是面试考核的重点（但是有利于大家进一步学习 Java。面试是结果，学习是过程，结果固然重要，但是好的过程一般对应了好的结果），学习能力有限的同学第一遍学习时有难以理解的地方可以跳过，后面再看几遍就能看懂。在记忆这些流程先后顺序时，切记不要死记硬背。

双亲委派模型一般是面试中的常客，这里一般会从双亲委派模型的解释起手发问。如果想在这里拿到加分点，就一定要对破坏双亲委派模型有所见解，结合具体的例子讲破坏双亲委派模型是很好地回答方式。另外，双亲委派模型部分有本节为数不多的源码可供学习。对源码学习的越透彻，面试时的底气也会越足。