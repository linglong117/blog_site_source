title: 56行代码理解RxJava
date: 2015-12-15 14:30:35
categories: 编程
tags: [rxjava]
---

自己动手实现RxJava。

<!-- more -->

# 最简单的观察者模式

```java
class Data {}

interface Subscriber {
    public void onEvent(Data d);
}

class Observable {
    private Subscriber subscriber;
    public void register(Subscriber s) {
        subscriber = s;
    }
    private void onEvent(Data d) {
        if(subscriber != null) {
            subscriber.onEvent(d);
        }
    }
}
```

# 多级观察者

```java
class InterSubscriber implements Subscriber {
    private Subscriber subscriber;
    
    public InterSubscriber register(Subscriber s) {
        subscriber = s;
    }
    @Override
    public void onEvent() {
        // 1. do something
        // 2. tell subscriber
        if(subscriber != null) {
                subscriber.onEvent();
        }
    }
}

Observable o = new Observable();
InterSubscriber is0 = new InterSubscriber();
InterSubscriber is1 = new InterSubscriber();
Subscriber s = new Subscriber();

o.register(is0);
is0.register(is1);
is1.register(s);
o.onEvent();
```

数据处理流程： `o.onEvent() -> is0.onEvent() -> is1.onEvent() -> s.onEvent`

# 中间观察者的另一面是**被观察者**

被观察者的行为可以总结为：
> 产生事件，事件
