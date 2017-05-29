---
layout: post
title: "[JAVA] Java Callable & Future Sample"
date:   2017-05-26 09:00:00 +0900
categories: JAVA 
---

## 시나리오
 - MAX 8개의 스레드로 각각 담당한 메시지 리스트를 동시에 처리한다.
 - 리스트를 8개로 분할하는 작업은 Guava 라이브러리를 통해서 처리하였다. (Lists.partition())
 - 각 쓰레드에 쪼개진 리스트를 하나씩 넘겨서 개별 스레드에서 메시지 전송을 처리하게 하였다.
 - 해당 Source는 @Async 적용 전 소스로 Java의 ExecutorService를 통해서 shutdown()까지 처리하도록 구현하였다.
 - MessageCallable은 new로 리스트 별로 생성되는 객체로 BeanFactory로 관리되는 객체가 아니기에 생성자로 BO 객체를 넣어주고 있다.
 - 해당 소스를 좀 더 스프링스럽게 @Async 를 사용하도록 변경한다. 

## Callable Sample Source

~~~java
public class MessageCallable implements Callable<List<Message>>{

    private List<Message> messageList;
    private MessageSendBO messageSendBO;

    public MessageCallable(List<Message> messageList, MessageSendBO messageSendBO) {
        this.messageList = messageList;
        this.messageSendBO = messageSendBO;
    }

    @Override
    public List<Message> call() throws Exception {

        for (Message message : messageList) {
            try {
                messageSendBO.sendAsyncMessage(message);
            } catch (Exception e) {
                ExceptionCommonLogger.error(ExceptionPrefix.BATCH, e, message);
            }
        }

        return messageList;
    }
}
~~~

## Future Sample Source

- executor의 shutdown() 처리도 직접해주어야 한다.
- @Async를 적용하면 Spring Bean Factory가 destory()를 통해 처리해준다.

~~~java
public Integer sendParallelMessageProcessing(List<Message> messageList, Integer executorPoolSize) {
    Integer failCount = 0;

    int partitionSizePerThread = getPartitionSizePerThread(messageList.size(), executorPoolSize);
    List<List<Message>> partitionedList = Lists.partition(messageList, partitionSizePerThread); // 최대 8개의 Thread 가 동작할 수 있는 적절한 값을 찾는다.

    log.info("총 처리 개수 :: {}, 스레드당 처리 개수 :: {}, 파티션된 리스트 개수) :: {}", messageList.size(), partitionSizePerThread, partitionedList.size());

    ExecutorService executor = Executors.newFixedThreadPool(executorPoolSize);
    List<Future<List<Message>>> futureList = Lists.newArrayList();

    for (List<Message> eachList : partitionedList) {
        Callable<List<Message>> worker = new MessageCallable(eachList, messageSendBO);
        Future<List<Message>> submit = executor.submit(worker);
        futureList.add(submit);
    }

    for (Future<List<Message>> future : futureList) {
        try {
            future.get();
        } catch (Exception e) {
            ExceptionCommonLogger.error(ExceptionPrefix.BATCH, e);
        }
    }

    /** safely shutdown */
    try {
        executor.shutdown();

        int tryCount = 0;
        while (!executor.isTerminated()) {
            executor.awaitTermination(1, TimeUnit.SECONDS);

            /** 10번 이상 수행시에 강제종료*/
            if (tryCount++ > 10) {
                executor.shutdownNow();
                break;
            }
        }
        log.info("finished");
    } catch (InterruptedException e) {
        executor.shutdownNow();
    }

    return failCount;
}
~~~
    