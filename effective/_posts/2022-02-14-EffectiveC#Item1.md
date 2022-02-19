---
layout: post
title: Item 1 - 지역변수 선언은 var를 사용해라
description: >
hide_description: false
category: effective
image: https://images.unsplash.com/photo-1644329901564-1e47f5d1b81a?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1742&q=80
---

* this unordered seed list will be replaced by the toc
{:toc}

# 소개
* C# 3.0부터 메서드 범위에서 선언된 변수에 암시적 형식 var를 사용할 수 있습니다.
* 암시적 형식 지역 변수는 형식을 직접 선언하는 것처럼 강력한 형식이지만, 컴파일러가<br>
  형식을 결정합니다.
* 본 책에서는 지역변수를 선언할 시에 var 사용을 권장합니다.

# 장점
- 작성된 코드의 가독성을 높일 수 있습니다. 
- 개발자의 입장에서는 변수의 타입과 같은 지엽적인 부분보다는 변수의 의미 파악에 더욱 집중할 수 있게 됩니다.
- 컴파일러의 관점에서 본다면 특정 객체의 타입을 명시적으로 지정하지 않더라도 객체 생성에 문제가 있을 경우 오류를 보고할 수 있습니다.
- 올바른 타입을 알지 못하여 잘못된 타입을 명시적으로 지정하여 사용하여 성능이 저하되는 등의 문제를 방지할 수 있습니다.

## 참고
지역변수에 대한 타입 추론이 C#의 고유 특성이라고 할 수 있는 정적 타이핑을 훼손하는 것은 아닙니다.
<br>
앞서 정적 타이핑과 동적 타이핑에 대해 짧게 요약하자면

### 정적 타이핑(static typing)
* 컴파일 타임에 자료형을 결정
* 컴파일 당시에 자료형에 대한 판단을 진행
* 대표 언어 : C, C++, C#

### 동적 타이핑(dynamic typing)
* 자료형을 런타임 시에 결정
* 코드 작성 시에는 편하지만, 오류를 발견하기 어렵습니다.
* 대표 언어 : Python, Ruby, Javascript

C#에서 특정 변수를 var로 선언하면 동적 타이핑이 수행되는 것이 아니라 할당 연산자(=) 오른쪽의 타입을 확인하여
왼쪽 변수의 타입을 결정하게 되는 것입니다. 따라서 컴파일러에게 명시적으로 알려주지 않아도 개발자를 대신하여 올바른
타입을 추론해주는 것이라고 할 수 있습니다.

# 사용 시 주의 사항
장점만 본다면 모든 변수에 대하여 var을 쓰는 것이 좋아보이지만, 실제로는 그렇지 않습니다.
* 내장 숫자타입(int, double, float 등)과 사용시에는 가독성과 정밀도에서의 오류가 발생할 수 있어 이 때는 명시적으로 자료형을 적어주는 것이
좋습니다.

## 예제 코드
<pre><code class="C#">using System;

namespace Item_1
{
    class Program
    {
        //static double GetMagicNumber()
        //{
        //    double num = 10;
        //    return num;
        //}

        //static Single GetMagicNumber()
        //{
        //    Single num = 10;
        //    return num;
        //}

        //static Decimal GetMagicNumber()
        //{
        //    Decimal num = 10;
        //    return num;
        //}

        //static Int32 GetMagicNumber()
        //{
        //    Int32 num = 10;
        //    return num;
        //}

        static Int64 GetMagicNumber()
        {
            Int64 num = 10;
            return num;
        }

        static void Main(string[] args)
        {
            var f = GetMagicNumber();
            var total = 100 * f / 6;
            Console.WriteLine($"Declared Type : {total.GetType().Name}, Value : {total}");
        }
    }
}
</code></pre>

## 실행 결과
GetMagicNumber()의 반환 타입을 여러 자료형으로 바꿔가며 출력했을 때의 결과입니다.
<pre><code class="C#">Declared Type : Double, Value : 166.666666666667
Declared Type : Single, Value : 166.6667
Declared Type : Decimal, Value : 166.66666666666666666666666667
Declared Type : Int32, Value : 166
Declared Type : Int64, Value : 166
</code></pre>

* 컴파일러는 GetMagicNumber()의 반환 타입으로 변수 f의 타입을 결정합니다.
* 즉, 숫자 타입은 지역변수에서 선언하더라도 var 타입이 아닌 명시적으로 선언하는 것을 권장합니다.

## 예제 코드
```c#
List<string> animalList = new List<string> { "dog", "cat", "tuna", "lion", "squid" };

IEnumerable<string> q =
  from animal in animalList
   select animal;

var query = q.Where(s => s.StartsWith("d"));
```

* 위의 코드는 제대로 동작할지라도 심각한 성능 문제를 유발할 수 있습니다. 
* 결과를 받을 변수 q를 개발자가 IEnumerable 타입으로 명시적으로 지정하였기 때문입니다.
* q의 반환값은 IQueryable인데 이를 명시적으로 IEnumerable로 선언하였기 때문에 IQueryable로 선언하였을 때의 이점은 모두 상실되고 성능 저하라는 문제로 이어집니다.
* 즉, Linq 구분을 이용할 때 정확한 반환 타입을 모를 때에는 var을 이용하는 것이 좋습니다.
* 수정된 코드는 아래와 같습니다.

```c#
List<string> animalList = new List<string> { "dog", "cat", "tuna", "lion", "squid" };
var q =
    from animal in animalList
    select animal;

var query = q.Where(s => s.StartsWith("d"));
```
# 정리
* 가독성을 해치지 않는 선에서 지역 변수에 var를 사용하는 것이 좋습니다.
* 정확한 정밀도를 요구하는 숫자 타입에는 var보다는 명시적으로 타입을 작성해주도록 합니다.
* Linq를 사용할 때, 그 반환 타입을 제대로 알지 못하는 경우에는 var를 이용하는 것이 성능 저하 문제를 막을 수 있습니다.

# 참조
- [참조1 - var](https://docs.microsoft.com/ko-kr/dotnet/csharp/language-reference/keywords/var)