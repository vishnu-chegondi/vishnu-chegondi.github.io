---
layout: post
title:  "Asynchronous Programming"
date:   2018-08-09 16:27:24
categories: [Coding]
comments: true
author: vishnuchegondi
tags: [Advanced Python]
---

For understanding the asynchronous, we start from the synchronous or sequenctial programming where each intruction of your python code is executed one by one by interpreter.

- Let us move into a scenario where you are requesting data from different urls where one of those urls is taking much time to execute.
- CPU(thread) will be idle untill the data is pulled from the url.
- In this time instead of waiting for the data we can work on other requested data.
- The idle time of thread is reduced using this asynchronous programming.

<!--more-->

### Speed Breakers for Threads in python

- Threads require lot of memory, around 8MB. For small number of threads that doesn't matter if we have hundreds of thousand  threads of functions 'simultaneously'. Running a thread per unique activity is not a good idea.
- Threads are costly to start. If you constantly create threads it slows down all other work.

Python can work around all these and cam up with the solution of coroutines as threads. They are implemented extension of generators. The memory cost is around 1kb for each function.

{% highlight python %}
import time

def my_coroutine():
    print ('something')
    while True:
        received = yield
        if received == 'First':
            print ('waiting First')
            time.sleep(4)
        if received == 'Second':
            print ('waiting second')
            time.sleep(2)
        print('Received:', received)

if __name__=="__main__":
    it = my_coroutine()
    next(it)             # Prime the coroutine
    it.send('First')
    it.send('Second')
{% endhighlight %}

Consider the above example yield after python 2.5 makes it possible to run the coroutines in the while loop with yield instruction.

In the above example next is used to prepare the generator for recieving the inputs. Here the yield makes the coroutines by sending initial values.

### Coroutines with Asyncio module python 3.7

This is most recommended way to use asynchronous programming.

{% highlight python %}
import asyncio
import aiohttp,async_timeout,time

async def minimum(x):
    print (x,"started")
    await asyncio.sleep(5-x)
    print ("Received:",x)
async def run_tasks():
    tasks = [minimum(x) for x in range(3)]
    await asyncio.gather(*tasks)

if __name__=="__main__":
    asyncio.run(run_tasks())
{% endhighlight %}

{% highlight shell %}
Output
0 started
1 started
2 started
Received: 2
Received: 1
Received: 0
{% endhighlight %}

In the above example you can verify that whenever you faced the sleep, I/O input wait or remote resource wait then in that scenarios you can just wait until the action performed mean while the thread is busy with the next independent task.

### Terminology

**Coroutines:** We all familiar with sub-routines. If a function *A* is executed as a part of function *B* then we call *A* is sub-routine of *B*. In the similar way if the function executes in parallel with the another function we call them as co-rotines.

**Event Loop:** This is a central executor in asynio. Asynchronous code is running on only one thread.

> Always use async.sleep() as this returns the executor handle to the thread. On the other hand time.sleep() do not return handle to thread.