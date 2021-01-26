# 概念
- 一个大任务分割成多个小任务，并将小任务分别计算得到的结果汇总。

# 源码

# 举例

```
package juc;

import java.util.concurrent.RecursiveTask;

public class SumFork extends RecursiveTask<Long>{
    
    /**
     *
     */
    private static final long serialVersionUID = 1L;

    private static final int THRESHOLD = 500;
    private long[] array;
    private int start;
    private int end;

    SumFork(long[] array,int start,int end){

        this.array = array;
        this.start = start;
        this.end = end;
    
    }

    @Override
    protected Long compute() {
        if (end - start <= THRESHOLD) {
            // 如果任务足够小,直接计算:
            long sum = 0;
            for (int i = start; i < end; i++) {
                sum += this.array[i];
                // 故意放慢计算速度:
                try {
                    Thread.sleep(1);
                } catch (InterruptedException e) {
                }
            }
            return sum;
        }
        // 任务太大,一分为二:
        int middle = (end + start) / 2;
        System.out.println(String.format("split %d~%d ==> %d~%d, %d~%d", start, end, start, middle, middle, end));
        SumFork subtask1 = new SumFork(this.array, start, middle);
        SumFork subtask2 = new SumFork(this.array, middle, end);
        invokeAll(subtask1, subtask2);
        Long subresult1 = subtask1.join();
        Long subresult2 = subtask2.join();
        Long result = subresult1 + subresult2;
        System.out.println("result = " + subresult1 + " + " + subresult2 + " ==> " + result);
        return result;
    }

    
    
}

```
```
package juc;

import java.util.Random;
import java.util.concurrent.ForkJoinPool;
import java.util.concurrent.ForkJoinTask;

public class Main {
    
    static Random random = new Random(0);

    public static void main(String[] args) {

        
        long[] array = new long[2000];
        long sum = 0;
        for(int i = 0;i<array.length;i++){
            array[i] = random.nextInt(10000);
            sum += array[i];
        }
        System.out.println("Expected sum: " + sum);

        ForkJoinTask<Long> task = new SumFork(array, 0, array.length);
        long startTime = System.currentTimeMillis();
        Long result = ForkJoinPool.commonPool().invoke(task);
        long endTime = System.currentTimeMillis();
        System.out.println("Fork/join sum: " + result + " in " + (endTime - startTime) + " ms.");

    }
}
```


