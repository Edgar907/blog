---
layout: post
title: Item 13 - 정적 클래스 멤버를 올바르게 초기화하라
description: >
hide_description: false
category: effective
image: https://images.unsplash.com/photo-1644329901564-1e47f5d1b81a?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1742&q=80
---

* this unordered seed list will be replaced by the toc
{:toc}

# 소개
정적 멤버를 포함하는 클래스가 있다면 인스턴스를 생성하기 전에 반드시 정적 멤버 변수를 초기화해야 합니다. 이를 위해 C#에서는 정적 생성자와 정적 멤버 초기화 구문이라는 두 가지의 기능을 제공합니다.

# 정적 생성자
타입 내에 정의된 모든 메서드, 변수, 속성에 최초로 접근하기 전에 자동으로 호출되는 메서드입니다. 따라서 이 메서드를 활용하면 정적 변수를 초기화하거나, 싱글톤 패턴을 적용하거나 등의 다양한 작업을 하기 수월합니다.
```csharp
public class MySingletone1
{
    private static readonly MySingleton1 theOneAndOnly;

    static MySingleton1() // 정적 생성자
    {
        try
        {
            theOneAndOnly = new MySingleton1();
        }
        catch
        {
            // ...
        }
    }

    public static MySingleton1 TheOnly
    {
        get
        {
            return theOneAndOnly;
        }
    }

    private MySingleton1()
    {

    }
}
```
위 코드에는 정적 생성자에 대한 예시 뿐만 아니라 Item 12의 멤버 초기화 구문의 예외사항 중 하나인 "멤버 초기화할 때 예외 처리가 필요한 경우"에 대한 솔루션이 담겨있습니다. Item 12 포스트에서도 언급은 하였지만 멤버 초기화 구문을 사용할 시 멤버 초기화 구문의 경우엔 예외를 잡아낼 방법이 없습니다. 대신 정적 생성자를 이용하면 위와 같은 방식으로 예외를 잡아줄 수 있습니다. 
<br>
<br>

정적 생성자의 호출 순서를 알아보기 위하여 작성한 코드
```csharp
using System;

namespace StaticConstructor
{
    class Fruit
    {
        public static int fruitCount;
        
        public Fruit() // 일반 생성자
        {
            Console.WriteLine("일반 생성자 호출");
            ++fruitCount;
        }

        static Fruit() // 정적 생성자
        {
            fruitCount = 0;
            Console.WriteLine("정적 생성자 호출 count = 0");
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            Fruit fruit1 = new Fruit();
            Console.WriteLine($"fruitCount = {Fruit.fruitCount}");
            Fruit fruit2 = new Fruit();
            Console.WriteLine($"fruitCount = {Fruit.fruitCount}");
            Fruit fruit3 = new Fruit();
            Console.WriteLine($"fruitCount = {Fruit.fruitCount}");
        }
    }
}
```
정적 필드 초기화 -> 정적 생성자 -> 일반 필드 초기화 -> 일반 생성자 순으로 이뤄지는 것을 확인할 수 있습니다.

## 정적 생성자 vs 일반 생성자
### 정적 생성자
- 주로 정적 데이터(static) 초기화 및 한 번만 수해이해야 하는 특정 작업에 이용됩니다.
### 일반 생성자
- new를 통해 인스턴스 객체를 생성할 때마다 매번 호출됩니다.

# 정적 멤버 초기화 구문
Item 12에 나온 멤버 초기화 구문과 마찬가지로 간단하게 정적 멤버를 초기화하는 경우라면 정적 생성자 보다는 정적 초기화 구문을 이용하는 것이 좋습니다.
```csharp
public class MySingletone1
{
    private static readonly MySingleton1 theOneAndOnly = new MySingleton1();

    public static MySingleton1 TheOnly
    {
        get
        {
            return theOneAndOnly;
        }
    }

    private MySingleton1()
    {

    }
}
```

# 정리
정적 멤버를 초기화할 때는 정적 생성자와 정적 멤버 초기화 구문이라는 두 가지의 기능이 있으므로 초기화의 복잡도에 따라 두 가지 기능을 적절히 이용하는 것이 좋을 것 같습니다.