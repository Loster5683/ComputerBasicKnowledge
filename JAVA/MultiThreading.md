#### 信号量(n线程循环输出0到M)

```java
import java.util.concurrent.Semaphore;

class Main  {
    static int count = 1;
    static  final  int M = 100;
    static final int N = 10;

    public static void main(String [] args) throws InterruptedException {
        Semaphore [] semaphore = new  Semaphore[N];
        Thread [] threads  = new Thread[N];
        for(int i=0;i<N;i++){
            semaphore[i]=new Semaphore(1);
            if(i!=N-1){
                semaphore[i].acquire();
            }
        }
        for(int j=0;j<N;j++){
            final Semaphore sem_before = (j==0)?semaphore[N-1]:semaphore[j-1];
            final Semaphore sem_current = semaphore[j];
            final int i = j;
            threads[j] = new Thread(
                    new Runnable() {
                        @Override
                        public void run() {
                            try {
                                while (true){
                                    sem_before.acquire();
                                    System.out.println("thread"+ i + "print" + count++);
                                    if(count>M){
                                        System.exit(0);
                                    }
                                    sem_current.release();
                                }

                            }catch (Exception e){
                                e.printStackTrace();
                            }
                        }
                    }
            );
            threads[j].start();
        }
    }
}
```

