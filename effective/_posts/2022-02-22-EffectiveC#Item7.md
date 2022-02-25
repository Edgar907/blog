---
layout: post
title: Item 7 - 델리게이트를 이용하여 콜백을 표현하라
description: >
hide_description: false
category: effective
image: https://images.unsplash.com/photo-1644329901564-1e47f5d1b81a?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1742&q=80
---

* this unordered seed list will be replaced by the toc
{:toc}

# 소개
**델리게이트(delegate : 대리자)**는 C#만의 생소한 기능이지만, C / C++에서의 함수 포인터와 비슷한 개념이며, 함수 자체를 하나의 데이터로 보고 의미 그대로
다른 메서드를 대신 실행하는 기능입니다.
<br><br>
**콜백(callback)**은 서버가 클라이언트에게 비동기적(asynchronous)으로 피드백을 주기 위해서 주로 사용하는 방법으로 이를 위하여 멀티스레딩 기술도 사용되고 동기적으로 상태를 갱신하는 기법도 활용됩니다.

# 델리게이트의 장점
- 타입 안정적인 콜백을 정의할 수 있습니다.
- 클래스 간의 상호 통신을 수행해야할 때 클래스의 결합도(coupling)을 낮출 수 있습니다.
- 런타임에서 통지 대상을 설정할 수 있으며, 다수의 클라이언트에게 통지를 보낼 수 있습니다.

# .NET Framework 라이브러리의 델리게이트 형태
```C#
.NET Framework는 Predicate<T>, Func<>, Action<>과 같은 형태로 
자주 사용하는 델리게이트를 정의해두었습니다.

Predicate<T> : 조건을 검사하여 bool 값을 반환하는 델리게이트
Func<> : 여러 개의 매개변수를 받아 특정 타입의 단일 결과값을 반환하는 델리게이트
Action<> : 여러 개의 매개변수를 받지만 결과값의 타입이 void인 델리게이트



List<int> numbers = Enumerable.Range(1, 200).ToList();

// Predicate<int> 형식의 델리게이트 사용
var oddNumbers = numbers.Find(n => n % 2 == 1); 
// 각 요소를 개별적으로 테스트하되, 모든 항목 통과 시, True 반환
var test = numbers.TrueForAll(n => n < 50); 

numbers.RemoveAll(n => n % 2 == 0);

// 리스트 내의 각 요소에 대하여 델리게이트로 지정한 동작을 수행합니다.
numbers.ForEach(item => Console.WriteLine(item)); 
```
컴파일러는 람다 표현식을 메서드로 변환한 후, 이 메서드를 참조하는 델리게이트를 생성합니다.

# 멀티캐스트 델리게이트
- 델리게이트에 복수 개의 메서드를 연결한 것입니다.
- 내부적으로는 연속으로 함수들을 호출하기 때문에 예외가 발생하면 함수 호출이 중단됩니다.
- 반환값이 존재한다면, 마지막으로 호출된 함수의 반환 값을 가지게 됩니다.

# 정리
- .NET 환경에서 콜백이 필요한 경우 반드시 델리게이트를 사용합시다

# 참조
- [참조1 - delegate](https://docs.microsoft.com/ko-kr/dotnet/csharp/programming-guide/delegates/)