---
layout: post
title: Item 12 - 할당 구문보다 멤버 초기화 구문이 좋다.
description: >
hide_description: false
category: effective
image: https://images.unsplash.com/photo-1644329901564-1e47f5d1b81a?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1742&q=80
---

* this unordered seed list will be replaced by the toc
{:toc}

# 소개
클래스를 만들다 보면 종종 둘 이상의 생성자를 작성해야 하는 경우가 있습니다. 이 때 생성자 내에서 멤버 변수들의 값을 초기화하도록 코드를 작성하다 보면
모든 생성자에서 초기화를 해줘야 하는데 자칫 멤버 변수 초기화 코드가 누락될 수 있습니다. 이러한 실수를 하지 않으려면 멤버 초기화 구문(member initializer)을 이용하는 것이 좋습니다. 반대되는 개념은 할당 구문이라고 합니다.

## 할당 구문
- 할당 구문은 생성자 내에서 멤버 변수의 값을 할당하는 것을 말합니다.
- 생성자가 여러개일 경우 누락될 우려가 있습니다.
```csharp
public class MyClass
{
    List<string> list;
    
    public MyClass() // MyClass 생성자
    {
        list = new List<string>();
    }
}
```

## 멤버 초기화 구문
- 멤버 초기화 구문은 단순하게 멤버를 선언할 때 초기화를 하는 코드를 작성하면 됩니다.
- 컴파일러는 모든 생성자의 시작 부분에 멤버 초기화 구문을 포함시키기 때문에 새로운 생성자를 추가하더라도 멤버 초기화 구문이 항상 포함됩니다.
- 정확하게는 베이스 클래스 생성자가 호출되기 전에 멤버에 대한 초기화가 이루어집니다.
```csharp
public class MyClass
{
    List<string> list = new List<string>();
}
```
# 이처럼 좋은 기능.. 하지만?
이처럼 멤버 초기화 구문은 좋은 기능이지만, 항상 좋은 기능에는 그에 상응하는 제약 사항과 주의해야 하는 사항들이 존재합니다.<br>
아래 3가지의 경우에 대해서는 멤버 초기화 구문을 사용하지 않는 것이 좋다고 합니다.

## 객체를 0이나 null로 초기화하는 경우
- 기본 시스템 초기화 루틴은 코드를 실행하기 전에 모든 값을 0으로 설정
- low level의 시스템 초기화 루틴에서 이미 변수의 값을 0으로 설정하기 때문에 추가적으로 변수를 0이나 null로 설정할 필요가 없습니다.
<pre><code class="C#">public struct MyValType 
{ 
    // 생략 
} 

MyValType myVal1; // 0으로 초기화 
MyValType myVal2 = new MyValType(); // 반복해서 0으로
</code></pre>

- 위 코드의 아래 두 문장은 모두 변수를 0으로 초기화하는데, 특히 두 번째 문장은 initobj라는 IL(중간 언어) 명령을 하용하는데
박싱 / 언박싱된 myVal2 변수 모두에 대해서 0으로 초기화하는 과정이 수행되고 퍼포먼스 저하가 발생됩니다.

## 동일한 객체를 반복해서 초기화하는 경우
- 객체 생성 방법이 모든 생성자에서 동일한 경우에만 사용하는 것이 좋습니다.
```csharp
public class MyClass2 
{ 
    // 컬렉션을 선언하는 동시에 초기화 
    private List<string> labels = new List<string>(); 
    
    MyClass2()
    { 

    } 
    MyClass2(int size) 
    { 
        labels = new List<string>(size); 
    }
}
```
- 멤버 초기화 구문은 컴파일러가 모든 생성자의 시작 부분에 포함시킨다고 하였습니다. 즉, 우리가 직접 작성한 것은 아니지만 아래와 같은
코드를 실행한 꼴이 됩니다.
```csharp
public class MyClass2 
{ 
    // 컬렉션을 선언하는 동시에 초기화 
    private List<string> labels; 
    
    MyClass2()
    { 
        labels = new List<string>();
    } 
    MyClass2(int size) 
    { 
        labels = new List<string>();
        labels = new List<string>(size); 
    }
}
```
즉, 생성자 내부에서 생성한 객체만 살아남고, 다른 하나는 가비지가 되어버립니다.

## 멤버 초기화할 때 예외 처리가 필요한 경우
- 멤버 초기화 구문은 try ~ catch 구문으로 감쌀 수 없기 때문에 초기화 과정에서 예외가 발생한다면 예외가 외부로 전파됩니다.
이런 경우에는 생성자 내로 초기화 구문을 옮기고 예외 처리 코드를 적용하는 것이 좋습니다.

# 정리
- 멤버 초기화 구문을 이용하면 클래스 내에 생성자가 여러개 있는 경우에 개발자의 실수를 줄여줄 수 있는 좋은 기능입니다.
따라서 위에서 정리한 3가지 예외사항을 제외한다면 가급적 멤버 초기화 구문을 사용하는 것이 좋습니다.