---
layout: post
title: Item 9 - 박싱과 언박싱을 최소화하라
description: >
hide_description: false
category: effective
image: https://images.unsplash.com/photo-1644329901564-1e47f5d1b81a?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1742&q=80
---

* this unordered seed list will be replaced by the toc
{:toc}

# 소개
[박싱 & 언박싱](https://operative.netlify.app/csharp/2022-01-27-Boxing&Unboxing/) 글에서 이어집니다.
<br><br>
**박싱** : 값 타입 -> 참조 타입
<br>
**언박싱** : 참조 타입 -> 값 타입

박싱과 언박싱을 수행하는 과정에서 복사가 일어나 임시 객체가 생성되기도 하며, 불필요한 가비지가 남아 성능에 좋지 않은 영향을 미칩니다.
또한 예상치 못한 버그가 발생할 수 있기 때문에 가능한 한 피하는 것이 좋습니다.

# 해결 방법
대부분의 경우에는 .NET 2.0에 추가된 제네릭(Generic) 클래스와 제네릭 메서드를 사용하면 박싱과 언박싱을 피할 수 있습니다.
하지만, .NET Framework 곳곳에 System.Object 타입의 객체를 요구하는 API들이 많고, 여전히 박싱과 언박싱을 수행하며, reference
타입의 객체가 필요한 곳에 value 타입의 객체를 사용하면, 컴파일러는 자동적으로 박싱과 언박싱을 수행합니다.

# 대표적인 예시
```C#
using System;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
            int firstNumber = 1, secondNumber = 2, thirdNumber = 3;
            Console.WriteLine($"A few numbers:{firstNumber}, {secondNumber}, {thirdNumber}");
        }
    }
}
```
위 코드에서 first ~ thirdNumber 는 int 타입, 즉 value 타입이지만, 보간 문자열에 대입되면서 System.Object 타입으로 변환되면서
박싱을 수행하게 됩니다. 그리고 보간 문자열의 string이 되기 위해 자동으로 ToString() 메서드를 수행하게 됩니다. 결과적으로는 성능 저하로 이어지고요.

```C#
using System;

namespace ConsoleApp3
{
    class Program
    {
        static void Main(string[] args)
        {
            int firstNumber = 1, secondNumber = 2, thirdNumber = 3;
            Console.WriteLine($@"A few numbers:{firstNumber.ToString()}, {secondNumber.ToString()}, {thirdNumber.ToString()}");
        }
    }
}
```
첫 번째 코드의 취약점을 개선하고 싶다면, WriteLine() 메서드에 Value 타입의 객체를 직접 전달하는 것이 아닌 string 인스턴스를 전달하는 것이 좋습니다.

# 컬렉션과 박싱
- .NET 1.x의 컬렉션(Collection)은 System.Object 타입의 객체의 참조를 저장합니다.
- 따라서 value 타입을 컬렉션에 추가할 때는 박싱이 발생합니다.
- value 타입의 객체를 사용한다면, 값을 사용할 때마다 박싱된 객체의 복사본을 가져오고, 이로 인해 버그가 발생합니다.

# 컬렉션 내 구조체 값 변경
- 클래스와는 다르게 구조체는 값 타입입니다.
- 구조체가 리스트 내에 있을 경우 값을 바로 변경하면 컴파일 오류가 발생합니다.
- 리스트 내부에서 원소를 가져오는 순간 새로운 복사본이 생성됩니다.
- 구조체의 배열의 값은 바로 변경이 가능합니다.

## 소스 코드

```C#
using System;
using System.Collections.Generic;

namespace ConsoleApp3
{
    class Program
    {
        public struct Coords
        {
            public Coords(double x, double y)
            {
                X = x;
                Y = y;
            }

            public double X { get; set; }
            public double Y { get; set; }
        }
        
        public List<Coords> myList = new List<Coords>();
        public Coords[] myArray = new Coords[3];

        public void Setup()
        {
            myList.Add(new Coords());

            var tmp = myList[2]; // 리스트 내부에서 원소를 가져오는 순간 새로운 복사본 생성
            tmp.X = 1; // 복사본의 값을 바꾸는 것이기 때문에 원본의 값이 바뀌지 않음

            myArray[2].X = 2; // 구조체의 배열은 원본의 값을 직접 변경 가능
        }
    }
}
```

# 정리
박싱과 언박싱은 암시적으로 이루어져 개발자 입장에서 변환 작업을 알기 어렵고, 이로 인해 알 수 없는 버그가 발생할 수 있고, Value 타입을
다형적(Polymorphic)으로 처리하는 과정에서 성능에 악영향을 미칩니다.
<br>
따라서, value 타입을 System.Object 타입이나 인터페이스 타입으로 변경하는 코드는 가능한 작성하지 않는 것이 좋습니다.