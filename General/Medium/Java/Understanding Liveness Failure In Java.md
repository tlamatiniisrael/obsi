
## Why Your Java Thread May Never Stop (Even When You Set the Flag)

[

![Vikas Taank](https://miro.medium.com/v2/resize:fill:64:64/1*myL9NCMtAPplh9SxQXZGNA.jpeg)





](https://medium.com/@vikas.taank_40391?source=post_page---byline--feb5a9112e06---------------------------------------)

[Vikas Taank](https://medium.com/@vikas.taank_40391?source=post_page---byline--feb5a9112e06---------------------------------------)

Follow

2 min read

·

3 days ago

1

![](https://miro.medium.com/v2/0*uMwXFYNkPY05HNDm.png)

## This is How Synchronization Works

Let’s say we want to stop a thread , lets look at the below example. If you run below program you would see that the main thread does not see the value of the stopRequested and never stops.

If you run the below program , the backGround thread loops forever.

import java.util.concurrent.TimeUnit;  
public class StopThread {  
    private static  boolean stopRequested;  
    public static void main(String[] args) throws InterruptedException{  
        Thread backGroundThread= new Thread(()->{  
            int i=0;  
            while(!stopRequested)  
                i++;  
        });  
        backGroundThread.start();  
        TimeUnit.SECONDS.sleep(1);  
        stopRequested=true;  
    }  
}

![](https://miro.medium.com/v2/0*uMwXFYNkPY05HNDm.png)

## What is the Problem Here?

The Problem is in the absence of synchronization , there is no guarantee whether or when the backGround thread will see the modifications made to stopRequested by the main thread.

## In the absence of synchronization the virtual machine will interpret the above code as:

while (!stopRequested)  
 i++;  
to  
if(!stopRequested)  
  while(true)  
    i++

> The above optimization is called hoisting and that is what open JDK VM does.

Please support me here if you want me to create such content, Stay tuned and thanks for reading.

[buymeacoffee.com/vikastaankd](https://buymeacoffee.com/vikastaankd)

[

Medium

](https://medium.com/tag/medium?source=post_page-----feb5a9112e06---------------------------------------)

[

Interview

](https://medium.com/tag/interview?source=post_page-----feb5a9112e06---------------------------------------)

[

Software Development

](https://medium.com/tag/software-development?source=post_page-----feb5a9112e06---------------------------------------)

[

Software Engineering

](https://medium.com/tag/software-engineering?source=post_page-----feb5a9112e06---------------------------------------)

[

Software Architecture

](https://medium.com/tag/software-architecture?source=post_page-----feb5a9112e06---------------------------------------)

1

[

![Vikas Taank](https://miro.medium.com/v2/resize:fill:96:96/1*myL9NCMtAPplh9SxQXZGNA.jpeg)



](https://medium.com/@vikas.taank_40391?source=post_page---post_author_info--feb5a9112e06---------------------------------------)

[

## Written by Vikas Taank

](https://medium.com/@vikas.taank_40391?source=post_page---post_author_info--feb5a9112e06---------------------------------------)

[3.8K followers](https://medium.com/@vikas.taank_40391/followers?source=post_page---post_author_info--feb5a9112e06---------------------------------------)

·[60 following](https://medium.com/@vikas.taank_40391/following?source=post_page---post_author_info--feb5a9112e06---------------------------------------)

👉 Subscribe here: [[https://vikas-newsletter-2dac00.beehiiv.com/archive](https://vikas-newsletter-2dac00.beehiiv.com/archive)]

Follow