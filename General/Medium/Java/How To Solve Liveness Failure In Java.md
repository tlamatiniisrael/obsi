
## Using Synchronization and Mutual Exclusion to Solve Liveness failure.

[

![Vikas Taank](https://miro.medium.com/v2/resize:fill:64:64/1*myL9NCMtAPplh9SxQXZGNA.jpeg)





](https://medium.com/@vikas.taank_40391?source=post_page---byline--c0d6a5e17566---------------------------------------)

[Vikas Taank](https://medium.com/@vikas.taank_40391?source=post_page---byline--c0d6a5e17566---------------------------------------)

Follow

1 min read

·

1 day ago

[

## Understanding Liveness Failure In Java

### Why Your Java Thread May Never Stop (Even When You Set the Flag)

medium.com



](https://medium.com/@vikas.taank_40391/understanding-liveness-failure-in-java-feb5a9112e06?source=post_page-----c0d6a5e17566---------------------------------------)

## How To Solve for The liveness failure.

import java.util.concurrent.TimeUnit;  
public class StopThreadSynchronized {  
    private static  boolean stopRequested;  
    private static synchronized void requestStop() {  
        stopRequested = true;  
    }  
    private static synchronized boolean stopRequested() {  
        return stopRequested;  
    }  
    public static void main(String[] args) throws InterruptedException{  
        Thread backGroundThread= new Thread(()->{  
            int i=0;  
            while(!stopRequested())  
                i++;  
        });  
        backGroundThread.start();  
        TimeUnit.SECONDS.sleep(1);  
        requestStop();  
    }  
}

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/0*QHmjFYFiRANfld7d.png)

## It is important to note here that both the read and write must be synchronized .

The actions of the synchronized methods in StopThread would be atomic , the synchronization is required for the inter thread communication not for the mutual exclusion.

Press enter or click to view image in full size

![](https://miro.medium.com/v2/resize:fit:1400/0*Wo91RJqBlF1wYnVf.png)

Please support me here if you want me to create such content, Stay tuned and thanks for reading.

[buymeacoffee.com/vikastaankd](https://buymeacoffee.com/vikastaankd)

[

Java

](https://medium.com/tag/java?source=post_page-----c0d6a5e17566---------------------------------------)

[

Java8

](https://medium.com/tag/java8?source=post_page-----c0d6a5e17566---------------------------------------)

[

Software Development

](https://medium.com/tag/software-development?source=post_page-----c0d6a5e17566---------------------------------------)

[

Threads

](https://medium.com/tag/threads?source=post_page-----c0d6a5e17566---------------------------------------)

[

Software Engineering

](https://medium.com/tag/software-engineering?source=post_page-----c0d6a5e17566---------------------------------------)

[

![Vikas Taank](https://miro.medium.com/v2/resize:fill:96:96/1*myL9NCMtAPplh9SxQXZGNA.jpeg)



](https://medium.com/@vikas.taank_40391?source=post_page---post_author_info--c0d6a5e17566---------------------------------------)

[

## Written by Vikas Taank

](https://medium.com/@vikas.taank_40391?source=post_page---post_author_info--c0d6a5e17566---------------------------------------)

[3.8K followers](https://medium.com/@vikas.taank_40391/followers?source=post_page---post_author_info--c0d6a5e17566---------------------------------------)

·[60 following](https://medium.com/@vikas.taank_40391/following?source=post_page---post_author_info--c0d6a5e17566---------------------------------------)

👉 Subscribe here: [[https://vikas-newsletter-2dac00.beehiiv.com/archive](https://vikas-newsletter-2dac00.beehiiv.com/archive)]

Follow