# Lab08 Assignment

## 1. 创建一个线程，分别打印该线程和原来的线程的进程号和父进程号，然后回收刚刚创建的线程

```c
#include <stdio.h>
#include <unistd.h>
#include <pthread.h>
#include <string.h>
#include <fcntl.h>

void *create(void *arg){
    printf("new:%d %d\n",getpid(),getppid());
}

int main(){
    pthread_t tidp;
    int error=pthread_create(&tidp,NULL,create,NULL);
    if(error){
        printf("failed!");
        return 0;
    }
    printf("old:%d %d\n",getpid(),getppid());
    pthread_join(tidp,NULL);
    return 0;
}
```

## 2. 仅使用锁来实现两个线程的同步，让线程 a 不断地为公共变量 Num 增加 1，而当 Num 增加至 100 时线程 b 将 Num 归 0，不断重复上述过程。**将 Num 输出到屏幕并给出代码和操作步骤。**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#include <fcntl.h>
#include <unistd.h>
#include <pthread.h>
#include <sys/msg.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <sys/wait.h>
#include <sys/types.h>
#include <semaphore.h>

int num = 0;
pthread_mutex_t mutex;

void *newPthread_A() {
    while (1) {
        pthread_mutex_lock(&mutex);
        if (num < 100) {
            num = num + 1;
            printf ("A: %d\n", num);
        }
        pthread_mutex_unlock(&mutex);
    }
}

void *newPthread_B() {
    while (1) {
        pthread_mutex_lock(&mutex);
        if (num >= 100) {
            num = 0;
            printf ("B: %d\n", num);
        }
        pthread_mutex_unlock(&mutex);
    }
}

int main() {
    pthread_t tidp[2];
    pthread_mutex_init(&mutex, NULL);
    pthread_create(&tidp[0], NULL, newPthread_A, NULL);
    pthread_create(&tidp[1], NULL, newPthread_B, NULL);
    pthread_join(tidp[0], NULL);
    pthread_join(tidp[1], NULL);
    pthread_mutex_destroy(&mutex);
    return 0;
}
```

## 3. 在实际过程中，条件变量和互斥锁总是结合使用，因为互斥锁只能表示锁与不锁两种状态，而仅靠条件变量本身也是无法实现线程同步的。条件变量允许线程以无竞争的方式进行等待直到某条件发生，而不是总是尝试去获取锁。使用条件变量和锁重写上一问题，**给出代码和操作步骤**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#include <fcntl.h>
#include <unistd.h>
#include <pthread.h>
#include <sys/msg.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <sys/wait.h>
#include <sys/types.h>
#include <semaphore.h>

int num = 0;
pthread_mutex_t mutex;
pthread_cond_t cond;

void *newPthread_A() {
    while (1) {
        pthread_mutex_lock(&mutex);
        if (num >= 100)
            pthread_cond_signal(&cond);
        else
            printf ("A: %d\n", ++num);
        pthread_mutex_unlock(&mutex);
    }
}

void *newPthread_B() {
    while (1) {
        pthread_mutex_lock(&mutex);
        if (num >= 100)
            printf ("B: %d\n", num = 0);
        else
            pthread_cond_wait(&cond, &mutex);
        pthread_mutex_unlock(&mutex);
    }
}

int main() {
    pthread_t tidp[2];
    pthread_mutex_init(&mutex, NULL);
    pthread_cond_init(&cond, NULL);
    pthread_create(&tidp[0], NULL, newPthread_A, NULL);
    pthread_create(&tidp[1], NULL, newPthread_B, NULL);
    pthread_join(tidp[0], NULL);
    pthread_join(tidp[1], NULL);
    pthread_mutex_destroy(&mutex);
    pthread_cond_destroy(&cond);
    return 0;
}
```

这两题主要是希望大家体会条件变量的优势，注意线程 A 是每次将 num+1 而不是使用 while 直接将 num 从 0 加到 100

## 4. 在一个程序中创建 8 个线程，每个线程打印你的学号中的一位数字，按照学号顺序打印出来。**不要使用`sleep`来进行同步，给出代码和操作步骤**

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#include <fcntl.h>
#include <unistd.h>
#include <pthread.h>
#include <sys/msg.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <sys/wait.h>
#include <sys/types.h>
#include <semaphore.h>

#define LENGTH 8
const char *stuID = "12345678";

char *str[LENGTH];
sem_t *sems[LENGTH];
pthread_t tidp[LENGTH];

void *print(void *args) {
    int index = *((int*)args);
    sem_wait(sems[index]);
    printf("%c", stuID[index]); fflush(stdout);
    sem_post(sems[(index + 1) % LENGTH]);
}

int* state(int x) {
    int *res = malloc(sizeof (int));
    *res = x;
    return res;
}

int main() {
    // init
    for (int i = 0;i < LENGTH; ++i) {
        str[i] = malloc(sizeof(char) * 16);
        sprintf(str[i], "sem%d", i);
        sems[i] = sem_open(str[i], O_CREAT, 0777, 0);
    }
    sem_post(sems[0]);

    // create
    for (int i = 0;i < LENGTH; ++i)
        pthread_create(&tidp[i], NULL, print, state(i));

    // close
    for (int i = 0;i < LENGTH; ++i)
        pthread_join(tidp[i], NULL);

    for (int i = 0;i < LENGTH; ++i)
        sem_close(sems[i]),
        sem_unlink(str[i]);
    return 0;
}
```

**本题目你采取的同步策略是什么，为什么采用这样的策略，这是最好的同步策略吗？**

采用的是信号量，从同步策略的使用习惯而言，一般更倾向于使用锁来体现线程之间的互斥，使用信号量来体现线程之间的同步。互斥突出的是资源的唯一性和排他性，但其本身通常无法限制访问者（本体中为线程）对资源的访问顺序。同步则是在互斥的基础上通过其他机制实现访问者对资源的有序访问。

希望大家能够通过以上三题体会锁、条件变量、信号量的用法和场景。

## 5.假设缓冲区上限为 20，生产者和消费者线程各 10 个，请编写程序实现一个生产者消费者模型。**在每次生产、消费时将当前动作类型（produce/consume）与缓冲区内容量输出到屏幕，给出代码和操作步骤。**

生产者消费者问题（Producer-consumer problem），也称有限缓冲问题（Bounded-buffer problem），是一个多线程同步问题的经典案例。该问题描述了共享固定大小缓冲区的两个线程——即所谓的“生产者”和“消费者”——在实际运行时会发生的问题。生产者的主要作用是生成一定量的数据放到缓冲区中，然后重复此过程。与此同时，消费者也在缓冲区消耗这些数据。该问题的关键就是要保证生产者不会在缓冲区满时加入数据，消费者也不会在缓冲区中空时消耗数据。

生产者消费者问题主要要注意以下三点：

- 在缓冲区为空时，消费者不能再进行消费
- 在缓冲区为满时，生产者不能再进行生产
- 在一个线程进行生产或消费时，其余线程不能再进行生产或消费等操作，即保持线程间的同步

```c
#include <stdio.h>
#include <pthread.h>
#include <stdlib.h>

#define SIZE 20
#define MAX 100
#define PRODUCE_THREAD_NUM 10
#define CONSUME_THREAD_NUM 10
struct  Container{
    int *data;
    int producePos;
    int consumePos;
    int productNumber;
    int size;
} container;

int indexGrow(int size, int i) {
    return ++i >= size ? 0 : 1;
}

void put(int item) {
    container.data[container.producePos] = item;
    container.producePos = indexGrow(container.size, container.producePos);
    container.productNumber++;
    printf("produce: %d\n", item);
    fflush(stdout);
}

int take() {
    int result = container.data[container.consumePos];
    container.consumePos = indexGrow(container.size, container.consumePos);
    container.productNumber--;
    printf("consume: %d\n", result);
    fflush(stdout);
    return result;
}

int canConsume() {
    return container.productNumber >= 1;
}

int canProduce() {
    return container.productNumber < container.size;
}

int produceStop, consumeStop;

int mCount;

pthread_mutex_t mutex;

pthread_cond_t canConsumeCond, canProduceCond;

void init() {
    container.size = SIZE;
    container.data = (int *) malloc(container.size * sizeof(int));
    container.consumePos = container.producePos = container.productNumber = 0;

    pthread_mutex_init(&mutex, 0);
    pthread_cond_init(&canConsumeCond, 0);
    pthread_cond_init(&canProduceCond, 0);
    produceStop = consumeStop = 0;
    mCount = 0;
}

void *product() {
    while(1) {
        pthread_mutex_lock(&mutex);
        while (!canProduce() && !produceStop) {
            pthread_cond_wait(&canProduceCond, &mutex);
        }
        if (produceStop) {
            pthread_mutex_unlock(&mutex);
            break;
        }
        int item = ++mCount;
        put(item);
        if (Item >= MAX) {
            produceStop = 1;
        }
        pthread_cond_signal(&canConsumeCond);
        pthread_mutex_unlock(&mutex);
    }
    return NULL;
}

void *consume() {
    while(1) {
        pthread_mutex_lock(&mutex);
        while (!canConsume() && !consumeStop) {
            pthread_cond_wait(&canConsumeCond, &mutex);
        }
        if (consumeStop) {
            pthread_mutex_unlock(&mutex);
            break;
        }
        int item = take();
        if (item >= MAX) {
            consumeStop = 1;
        }
        pthread_cond_signal(&canProduceCond);
        pthread_mutex_unlock(&mutex);
    }
    return NULL;
}

void closeResources() {
    pthread_mutex_destroy(&mutex);
    pthread_cond_destroy(&canConsumeCond);
    pthread_cond_destroy(&canProduceCond);
}

int main() {
    init();
    pthread_t produceThread[PRODUCE_THREAD_NUM];
    pthread_t consumeThread[CONSUME_THREAD_NUM];
    for (int i = 0; i < PRODUCE_THREAD_NUM; i++) {
        pthread_create(produceThread + i, 0, produce, 0);
    }
    for (int i = 0; i < CONSUME_THREAD_NUM; i++) {
        pthread_create(consumeThread + i, 0, consume, 0);
    }
    for (int i = 0; i < PRODUCE_THREAD_NUM; i++) {
        pthread_join(produceThread[i], 0);
    }
    for (int i = 0; i < CONSUME_THREAD_NUM; i++) {
        pthread_join(consumeThread[i], 0);
    }
    closeResources();
    return 0;
}
```
