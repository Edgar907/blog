---
layout: post
title: Item 4 - string.Format()을 보간 문자열로 대체하라
description: >
hide_description: false
category: effective
image: https://images.unsplash.com/photo-1644329901564-1e47f5d1b81a?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1742&q=80
---

* this unordered seed list will be replaced by the toc
{:toc}

# 소개
C#에서도 C에서 널리 사용되는 string.Format과 같은 방법으로 string을 만들 수 있지만, C# 6.0에 새롭게 도입된 문자열 보간 기능을 사용하는 편이 여러모로 좋습니다.

- 코드의 가독성이 대폭 향상됩니다.
- 컴파일러 입장에서는 정적 타입 검사를 수행할 수 있기 때문에 개발자의 실수를 미리 방지할 수 있습니다.
- 기존 방식에 비해 문자열을 생성하기 위한 표현식이 더 풍성합니다.
<pre><code class="C#">using System;

namespace Item4
{
    class Program
    {
        static void Main(string[] args)
        {
            int a = 5;
            int b = 13;
            string str1, str2;

            str1 = "a의 값 " + "b의 값";
            Console.WriteLine(str1);

            str2 = string.Format("{0} + {1} = {2}", a, b, a + b);
            Console.WriteLine(str2);
        }
    }
}
</code></pre>

기존에 사용되던 string.Format() 또한 문자열 변환 과정을 잘 수행하였지만, 생성된<br> 문자열을 직접 출력해보고 올바른 형태인지를 
눈으로 직접 확인하기 전까지는 코드를 제대로 작성했는지 확인이 어렵고 이 메소드에는 다음과 같은 문제가 있습니다. 
- 포맷 문자열과 인자 리스트를 분리하여 전달하는 구조이기 때문이다.
- 포맷 문자열에 나타낸 인자의 개수와 실제로 전달되는 인자의 개수가 정확히 일치하는지를<br> 검사하지 않아 자칫 필요한 인자를 누락하면 런타임 예외가 발생할 수 있습니다.

또한 포맷 문자열과 출력할 내용을 담고 있는 배열을 나누어 전달하면 인자의 순서가 올바른지를 확인하기 어렵고 결국에는 항상 코드를 실행해보고 그 내용을
직접 확인해야만 하기 때문에 시간을 낭비하는 작업이라고 할 수 있습니다.

# 보간 문자열 사용방법
1. 문자열 앞에 '$'를 붙이면 됩니다.
2. 문자열로 변경할 표현식은 {}(중괄호) 내에 둡니다.

## 1. 값 타입이 박싱되는 것을 막자
위 코드에서 Math.PI는 double이므로 value(값) 타입입니다. 따라서 이를 object 타입으로 변경하려면 박싱(Boxing)을
수행해야 하는데 박싱은 성능에 크게 영향을 끼치므로 Math.PI.ToString()을 사전에 사용하여 미리 string 형태로 변환을
해두면 불필요한<br>박싱을 피할 수 있습니다.

<pre><code class="C#">using System;

namespace Item4
{
    class Program
    {
        static void Main(string[] args)
        {
            int a = 5;
            int b = 13;
            string str2 = $"{a} + {b} = {a + b}";

            Console.WriteLine(str2);

            // Console.WriteLine($"The value of pi is {Math.PI}"); // 성능에 안 좋을 수 있음
            Console.WriteLine($"The value of pi is {Math.PI.ToString()}");
        }
    }
}
</code></pre>

## 실행 결과
<pre><code class="C#">5 + 13 = 18
The value of pi is 3.14159265358979
</code></pre>

## 2. 삼항 연산자 사용 가능
문자열 보간 기능을 사용하다보면 동적으로 동작하는 보간 문자열이 필요할 수 있는데 그 때 아래와 같이 삼항 연산자의 사용도
가능하다. 다만 삼항 연산자를 사용할 때, ':'<br>의 경우 내장된 표준 포맷 string과 충돌할 위험이 있기 때문에 따로 ()로 묶어주는
것이 좋다.

<pre><code class="C#">using System;

namespace Item4
{
    class Program
    {
        static void Main(string[] args)
        {
            bool isRound = true;
            Console.WriteLine($"The value of pi is {(isRound ? Math.PI.ToString() : Math.PI.ToString("F2"))}");
            isRound = false;
            Console.WriteLine($"The value of pi is {(isRound ? Math.PI.ToString() : Math.PI.ToString("F2"))}");
        }
    }
}
</code></pre>

## 실행 결과
<pre><code class="C#">The value of pi is 3.14159265358979
The value of pi is 3.14
</code></pre>

## 3. null 조건 연산자 사용
<pre><code class="C#">using System;

namespace Item4
{
    class Program
    {
        static void Main(string[] args)
        {
            string name1 = null;
            Console.WriteLine($"The customer's name is {name1?? "Name is missing"}");
            string name2 = "Edgar";
            Console.WriteLine($"The customer's name is {name2 ?? "Name is missing"}");
        }
    }
}
</code></pre>

## 실행 결과
<pre><code class="C#">The customer's name is Name is missing
The customer's name is Edgar
</code></pre>

# 정리
string.Format()에 비해서는 가독성 및 안정성이 높다고 하지만 역시나 과도하게 사용하면 오히려 더욱 복잡한 코드가
될 수도 있기 때문에 적절히 사용하는 것이 좋습니다.

# 참조
- [참조1 - 문자열 보간](https://docs.microsoft.com/ko-kr/dotnet/csharp/language-reference/tokens/interpolated)