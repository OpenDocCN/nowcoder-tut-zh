# 第六章 第 5 节 Java-操作系统-5

> 原文：[`www.nowcoder.com/tutorial/10070/0cf4575e339545e7a4700a38e5b1d139`](https://www.nowcoder.com/tutorial/10070/0cf4575e339545e7a4700a38e5b1d139)

#### 1.18 介绍一下信号量。

**参考回答**

1.  在多进程环境下，为了防止多个进程同时访问一个公共资源而出现问题，需要一种方法来协调各个进程，保证它们能够合理地使用公共资源。信号量就是这样一种机制。

    信号量的数据类型为结构 sem_t，它本质上是一个长整型的数。函数 sem_init（）用来初始化一个信号量。它的原型为：　　

    extern int sem_init __P ((sem_t *__sem, int __pshared, unsigned int __value));　　

    sem 为指向信号量结构的一个指针；pshared 不为０时此信号量在进程间共享，否则只能为当前进程的所有线程共享；value 给出了信号量的初始值。　　

    （1）函数 sem_post( sem_t *sem )用来增加信号量的值。当有线程阻塞在这个信号量上时，调用这个函数会使其中的一个线程不在阻塞，选择机制同样是由线程的调度策略决定的。　　

    （2）函数 sem_wait( sem_t *sem )被用来阻塞当前线程直到信号量 sem 的值大于 0，解除阻塞后将 sem 的值减一，表明公共资源经使用后减少。函数 sem_trywait ( sem_t *sem )是函数 sem_wait（）的非阻塞版本，它直接将信号量 sem 的值减一。　　

    （3）函数 sem_timedwait(sem_t *sem, const struct timespec *abs_timeout) 与 sem_wait() 类似，只不过 abs_timeout 指定一个阻塞的时间上限，如果调用因不能立即执行递减而要阻塞。

    （4）函数 sem_destroy(sem_t *sem)用来释放信号量 sem。　

2.  使用示例代码如下

```cpp
//g++ semtest.cpp -o test -lpthread
#include <stdio.h>
#include <semaphore.h>
#include <pthread.h>
#include <unistd.h>
#include <sys/time.h>
sem_t sem;

/*function:获取当前时间，精确到毫秒  * */
int64_t getTimeMsec()
{
    struct  timeval    tv;
    gettimeofday(&tv, NULL);
    return tv.tv_sec * 1000 + tv.tv_usec / 1000;
}

void* func_sem_wait(void* arg)
{
    printf("set wait\n");
    sem_wait(&sem);
    printf("sem wait success\n");
    int *running = (int*)arg;
    printf("func_sem_wait running\n");
    printf("%d\n", *running);
}

void* func_sem_timedwait(void* arg)
{
    timespec timewait;
    timewait.tv_sec = getTimeMsec() / 1000 + 2;
    timewait.tv_nsec = 0;
    printf("sem_timedwait\n");
    int ret = sem_timedwait(&sem, &timewait);
    printf("sem_timedwait,ret=%d\n", ret);
    printf("func_sem_timedwait running\n");
}

void* func_sem_post(void* arg)
{
    printf("func_sem_post running\n");
    printf("sem post\n");
    int *a = (int*)arg;
    *a = 6;
    sem_post(&sem);
    sem_post(&sem);
}

int main()
{
    sem_init(&sem, 0, 0);
    pthread_t thread[3];
    int a = 5;

    pthread_create(&(thread[0]), NULL, func_sem_wait, &a);
    printf("thread func_sem_wait\n");

    pthread_create(&(thread[2]), NULL, func_sem_timedwait, &a);
    printf("thread func_sem_timedwait\n");

    sleep(4);

    pthread_create(&(thread[1]), NULL, func_sem_post, &a);
    printf("thread func_sem_post\n");

    pthread_join(thread[0], NULL);
    pthread_join(thread[1], NULL);
    pthread_join(thread[2], NULL);
    sem_destroy(&sem);
}
```

#### 1.19 说说僵尸进程和孤儿进程。

**参考回答**

1.  我们知道在 unix/linux 中，正常情况下，子进程是通过父进程创建的，子进程在创建新的进程。子进程的结束和父进程的运行是一个异步过程,即父进程永远无法预测子进程 到底什么时候结束。 当一个 进程完成它的工作终止之后，它的父进程需要调用 wait()或者 waitpid()系统调用取得子进程的终止状态。

2.  孤儿进程：一个父进程退出，而它的一个或多个子进程还在运行，那么那些子进程将成为孤儿进程。孤儿进程将被 init 进程(进程号为 1)所收养，并由 init 进程对它们完成状态收集工作。

3.  僵尸进程：一个进程使用 fork 创建子进程，如果子进程退出，而父进程并没有调用 wait 或 waitpid 获取子进程的状态信息，那么子进程的进程描述符仍然保存在系统中。这种进程称之为僵尸进程。

#### 1.20 请介绍进程之间的通信方式。

**参考回答**

进程间通信主要有以下几种方式

1.  管道 pipe：管道是一种半双工的通信方式，数据只能单向流动，而且只能在具有亲缘关系的进程间使用。进程的亲缘关系通常是指父子进程关系。

    ```cpp
    #include <stdio.h>
    #include <unistd.h>
    #include <stdlib.h>
    #include <string.h>
    #include <sys/wait.h>

    int pipe_default[2];

    int main()
    {
    pid_t pid;
    char buffer[32];

    memset(buffer, 0, 32);
    if(pipe(pipe_default) < 0)
    {
      printf("Failed to create pipe!\n");
      return 0;
    }

    if(0 == (pid = fork()))
    {
      close(pipe_default[1]); //关闭写端
      sleep(2);
      if(read(pipe_default[0], buffer, 32) > 0)
      {
        printf("[Client] Receive data from server: %s \n", buffer);
      }
      close(pipe_default[0]);
     }
    else
    {
      close(pipe_default[0]);  //关闭读端
      char msg[32]="== hello world ==";
      if(-1 != write(pipe_default[1], msg, strlen(msg)))
      {
        printf("[Server] Send data to client: %s \n",msg);
      }
      close(pipe_default[1]);
      waitpid(pid, NULL, 0);
    }
    return 1;
    }
    ```

2.  命名管道 FIFO：有名管道也是半双工的通信方式，但是它允许无亲缘关系进程间的通信。

    写管道：

    ```cpp
    #include <stdio.h>
    #include <errno.h>
    #include <unistd.h>
    #include <sys/types.h>
    #include <string.h>
    #include <stdlib.h>
    #include <fcntl.h>
    #include <sys/stat.h>

    int main()
    {
        int nFd = 0;
        int nWrLen = 0, nReadLen = 0;;
        char szBuff[BUFSIZ] = {0};

        /* 打开当前目录下的管道文件 */
        nFd = open("pipe", O_RDWR);
        if (-1 == nFd)
        {
            perror("Open fifo failed\n");
            return 1;
        }

        while (1)
        {
            /* 从终端读取数据 */
            memset(szBuff,0,BUFSIZ);
            nReadLen = read(STDIN_FILENO,szBuff,BUFSIZ);

            if(nReadLen > 0)
            {
                /* 往管道写入数据 */
                nWrLen = write(nFd, szBuff, strlen(szBuff)+1);
                if (nWrLen > 0)
                {
                    printf("write data successful: %s \n", szBuff);
                }
                else
                {
                    perror("write failed:");
                }
            }
        }
    }
    ```

读管道：

```cpp
#include <stdio.h>
#include <errno.h>
#include <unistd.h>
#include <sys/types.h>
#include <string.h>
#include <stdlib.h>
#include <fcntl.h>
#include <sys/stat.h>

int main()
{
    int nFd = 0;
    int  nReadLen = 0;;
    char szBuff[BUFSIZ] = {0};

    /* 打开当前目录下的管道文件 */
    nFd = open("pipe", O_RDWR);
    if (-1 == nFd)
    {
        perror("Open fifo failed\n");
        return 1;
    }

    while (1)
    {
        /* 从管道读取数据 */
        memset(szBuff,0,BUFSIZ);
        nReadLen = read(nFd,szBuff,BUFSIZ);
        if(nReadLen > 0)
        {
            printf("read pipe data: %s\n", szBuff);
        }   

    }
}
```

3.  消息队列 MessageQueue：消息队列是由消息的链表，存放在内核中并由消息队列标识符标识。消息队列克服了信号传递信息少、管道只能承载无格式字节流以及缓冲区大小受限等缺点。

    示例：使用消息队列进行进程间通信

    接收信息的程序源文件为 msgreceive.c 的源代码为：

    ```cpp
    #include <unistd.h>
    #include <stdlib.h>
    #include <stdio.h>
    #include <string.h>
    #include <errno.h>
    #include <sys/msg.h>

    struct msg_st
    {
        long int msg_type;
        char text[BUFSIZ];
    };

    int main()
    {
        int running = 1;
        int msgid = -1;
        struct msg_st data;
        long int msgtype = 0; //注意 1

        //建立消息队列
        msgid = msgget((key_t)1234, 0666 | IPC_CREAT);
        if(msgid == -1)
        {
            fprintf(stderr, "msgget failed with error: %d\n", errno);
            exit(EXIT_FAILURE);
        }
        //从队列中获取消息，直到遇到 end 消息为止
        while(running)
        {
            if(msgrcv(msgid, (void*)&data, BUFSIZ, msgtype, 0) == -1)
            {
                fprintf(stderr, "msgrcv failed with errno: %d\n", errno);
                exit(EXIT_FAILURE);
            }
            printf("You wrote: %s\n",data.text);
            //遇到 end 结束
            if(strncmp(data.text, "end", 3) == 0)
                running = 0;
        }
        //删除消息队列
        if(msgctl(msgid, IPC_RMID, 0) == -1)
        {
            fprintf(stderr, "msgctl(IPC_RMID) failed\n");
            exit(EXIT_FAILURE);
        }
        exit(EXIT_SUCCESS);
    }
    ```

    发送信息的程序的源文件 msgsend.c 的源代码为：

    ```cpp
    #include <unistd.h>
    #include <stdlib.h>
    #include <stdio.h>
    #include <string.h>
    #include <sys/msg.h>
    #include <errno.h>

    #define MAX_TEXT 512
    struct msg_st
    {
        long int msg_type;
        char text[MAX_TEXT];
    };

    int main()
    {
        int running = 1;
        struct msg_st data;
        char buffer[BUFSIZ];
        int msgid = -1;

        //建立消息队列
        msgid = msgget((key_t)1234, 0666 | IPC_CREAT);
        if(msgid == -1)
        {
            fprintf(stderr, "msgget failed with error: %d\n", errno);
            exit(EXIT_FAILURE);
        }

        //向消息队列中写消息，直到写入 end
        while(running)
        {
            //输入数据
            printf("Enter some text: ");
            fgets(buffer, BUFSIZ, stdin);
            data.msg_type = 1;    //注意 2
            strcpy(data.text, buffer);
            //向队列发送数据
            if(msgsnd(msgid, (void*)&data, MAX_TEXT, 0) == -1)
            {
                fprintf(stderr, "msgsnd failed\n");
                exit(EXIT_FAILURE);
            }
            //输入 end 结束输入
            if(strncmp(buffer, "end", 3) == 0)
                running = 0;
            sleep(1);
        }
        exit(EXIT_SUCCESS);
    }
    ```

    运行结果如下：

    ```cpp
    biao@ubuntu:~/test/msgRecvSend$
    biao@ubuntu:~/test/msgRecvSend$ ls
    msgreceive.c  msgsend.c  recv  send
    biao@ubuntu:~/test/msgRecvSend$ ./recv &
    [1] 8753
    biao@ubuntu:~/test/msgRecvSend$ ./send
    Enter some text: helloworld
    You wrote: helloworld

    Enter some text: Caibiao Lee
    You wrote: Caibiao Lee

    Enter some text: end
    You wrote: end

    [1]+  Done                    ./recv
    biao@ubuntu:~/test/msgRecvSend$
    ```

4.  共享存储 SharedMemory：共享内存就是映射一段能被其他进程所访问的内存，这段共享内存由一个进程创建，但多个进程都可以访问。共享内存是最快的 IPC 方式，它是针对其他进程间通信方式运行效率低而专门设计的。它往往与其他通信机制，如信号两，配合使用，来实现进程间的同步和通信。

    ```cpp
    /* Linux 6.cpp */
    #include <iostream>
    #include <stdlib.h>
    #include <string.h>
    #include <sys/shm.h>
    #include <sys/ipc.h>
    #include <unistd.h>

    using namespace std;
    int main()
    {
      char *shmaddr;
      char *shmaddread;
      char str[]="Hello, I am a processing. \n";
      int shmid;

      key_t key = ftok(".",1);
      pid_t pid1 = fork();
      if(pid1 == -1){
        cout << "Fork error. " << endl;
        exit(1);
      }
      else if(pid1 == 0){
        //子进程
        shmid = shmget(key,1024,IPC_CREAT | 0600);
        shmaddr = (char*)shmat(shmid, NULL, 0);
                   strcpy(shmaddr, str);
        cout << "[Writer] write: " << shmaddr << endl;
        shmdt(shmaddr);
      }
      else
      {
        //父进程
        pid_t pid2 = fork();
        if(pid2 == -1){
          cout << "Fork error. " << endl;
          exit(1);
        }
        else if(pid2 == 0){
          //子进程
          sleep(2);
          shmid = shmget(key,1024,IPC_CREAT | 0600);
          shmaddread = (char*)shmat(shmid, NULL, 0);        
          cout << "[Reader] read: " << shmaddread << endl;
          shmdt(shmaddread);
        }
      }
      sleep(3);
      return 0;
    }
    ```

5.  信号量 Semaphore：信号量是一个计数器，可以用来控制多个进程对共享资源的访问。它常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段。

    ```cpp
    使用示例代码如下
    ```

    ```cpp
    //g++ semtest.cpp -o test -lpthread
     #include <stdio.h>
     #include <semaphore.h>
     #include <pthread.h>
     #include <unistd.h>
     #include <sys/time.h>
     sem_t sem;

     /*function:获取当前时间，精确到毫秒 * */
     int64_t getTimeMsec()
     {
         struct  timeval    tv;
         gettimeofday(&tv, NULL);
         return tv.tv_sec * 1000 + tv.tv_usec / 1000;
     }

     void* func_sem_wait(void* arg)
     {
         printf("set wait\n");
         sem_wait(&sem);
         printf("sem wait success\n");
         int *running = (int*)arg;
         printf("func_sem_wait running\n");
         printf("%d\n", *running);
     }

    void* func_sem_timedwait(void* arg)
     {
         timespec timewait;
         timewait.tv_sec = getTimeMsec() / 1000 + 2;
         timewait.tv_nsec = 0;
         printf("sem_timedwait\n");
         int ret = sem_timedwait(&sem, &timewait);
         printf("sem_timedwait,ret=%d\n", ret);
         printf("func_sem_timedwait running\n");
     }

    void* func_sem_post(void* arg)
     {
         printf("func_sem_post running\n");
         printf("sem post\n");
         int *a = (int*)arg;
         *a = 6;
         sem_post(&sem);
         sem_post(&sem);
     }

    int main()
     {
         sem_init(&sem, 0, 0);
         pthread_t thread[3];
         int a = 5;

        pthread_create(&(thread[0]), NULL, func_sem_wait, &a);
         printf("thread func_sem_wait\n");

        pthread_create(&(thread[2]), NULL, func_sem_timedwait, &a);
         printf("thread func_sem_timedwait\n");

        sleep(4);

        pthread_create(&(thread[1]), NULL, func_sem_post, &a);
         printf("thread func_sem_post\n");

        pthread_join(thread[0], NULL);
         pthread_join(thread[1], NULL);
         pthread_join(thread[2], NULL);
         sem_destroy(&sem);
     }
    ```

6.  套接字 Socket：套解口也是一种进程间通信机制，与其他通信机制不同的是，它可用于不同机器间的进程通信。

7.  信号 ( sinal ) ： 信号是进程间通信机制中唯一的异步通信机制，可以看作是异步通知，通知接收信号的进程有哪些事情发生了。也可以简单理解为信号是某种形式上的软中断。

    一般情况下，信号的来源可分为以下三种：

*   硬件方式：除数为零、无效的存储访问等硬件异常产生信号。这些事件通常由硬件(如:CPU)检测到，并将其通知给 Linux 操作系统内核，然后内核生成相应的信号，并把信号发送给该事件发生时正在进行的程序。

*   软件方式：用户在终端下调用`kill`命令向进程发送任务信号、进程调用`kill`或`sigqueue`函数发送信号、当检测到某种软件条件已经具备时发出信号，如由`alarm`或`settimer`设置的定时器超时时将生成`SIGALRM`信号等多种情景均可产生信号。

*   键盘输入：当用户在终端上按下某键时，将产生信号。如按下组合键`Ctrl+C`将产生一个`SIGINT`信号，`Ctrl+\`产生一个`SIGQUIT`信号等。

    以下列出几个常用的信号：

    | 信号 | 描述 |
    | --- | --- |
    | SIGHUP | 当用户退出终端时，由该终端开启的所有进程都退接收到这个信号，默认动作为终止进程。 |
    | SIGINT | 程序终止(interrupt)信号, 在用户键入 INTR 字符(通常是`Ctrl+C`)时发出，用于通知前台进程组终止进程。 |
    | SIGQUIT | 和`SIGINT`类似, 但由 QUIT 字符(通常是`Ctrl+\`)来控制. 进程在因收到`SIGQUIT`退出时会产生`core`文件, 在这个意义上类似于一个程序错误信号。 |
    | SIGKILL | 用来立即结束程序的运行. **本信号不能被阻塞、处理和忽略**。 |
    | SIGTERM | 程序结束(terminate)信号, 与`SIGKILL`不同的是该信号可以被阻塞和处理。通常用来要求程序自己正常退出。 |
    | SIGSTOP | 停止(stopped)进程的执行. 注意它和 terminate 以及 interrupt 的区别:该进程还未结束, 只是暂停执行. **本信号不能被阻塞, 处理或忽略**. |

代码示例：

下面的代码收到程序退出信号后会执行用户定义的信号处理函数来替代系统默认的处理程序。

```cpp
#include<stdlib.h>
#include<stdio.h>
#include<signal.h>
#include<sys/types.h>
#include<unistd.h>

void sig_handle(int sig) {
    printf("received signal: %d, quit.\n", sig);
    exit(0);
}

int main () {
    signal(SIGINT, sig_handle);
    signal(SIGKILL, sig_handle);
    signal(SIGSEGV, sig_handle);
    signal(SIGTERM, sig_handle);

    int i = 0;
    while (1) {
        printf("%d\n", ++i);
        sleep(2);
    }

    printf("main quit.");

    return 0;
}
复制代码
```

运行结果：

```cpp
1
2
received signal: 15, quit.
```