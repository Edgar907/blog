---
layout: post
title: Item 3 - 캐스트보다는 is, as가 좋다
description: >
hide_description: false
category: effective
image: https://images.unsplash.com/photo-1644329901564-1e47f5d1b81a?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1742&q=80
---

* this unordered seed list will be replaced by the toc
{:toc}

# 소개 - C#에서의 형변환
- C# 에서 형변환을 수행하는 방법에는 **is/as 연산자**를 사용하는 방법과 컴파일러의 **캐스팅**을 사용하는 방법이 있습니다.
- 더욱 방어적인 코드를 작성하려는 경우에는 우선 **is 연산자**로 형변환 가능 여부를 확인한<br> 후에 실제 형변환을 수행하도록 코드를 작성할 수 있습니다.

## as 형변환 예제 코드
<pre><code class="C#">using System;

namespace Item3
{
    class Program
    {
        static void Main(string[] args)
        {
            object o = Factory.GetObject();

            // as 변환
            MyType t = o as MyType;

            if (t != null)
            {
                // MyType 타입의 t 객체 사용
            }
            else
            {
                // 형변환 실패 시
            }
        }
    }
}
</code></pre>

## 캐스팅 예제 코드
<pre><code class="C#">using System;

namespace Item3
{
    class Program
    {
        static void Main(string[] args)
        {
            object o = Factory.GetObject();

            // cast 변환 
            try
            {
                MyType t;
                t = (MyType)o;
                //MyType 타입의 t 객체 사용 
            }
            catch (InvalidCastException)
            {
                //형변환 실패 오류 
            }
        }
    }
}
</code></pre>

첫 번째 코드(as 연산자를 사용하는 경우)와 두 번째 코드(강제 형변환)를 비교했을 때 첫 번째 코드가 훨씬 가독성이 좋을 뿐만 아니라,
try/catch 문이 없기 때문에 성능면에서도 훨씬 좋다고 할 수 있습니다. 
<br><br>
물론 여기서 캐스팅 방법은 as 연산자 방법과는 다르게 null 체크를 해줄 필요가 없지 않냐고 반문할 수 있지만, 실제로 캐스팅 방법을 사용할 때
null은 어떤 타입으로든 형변환이 가능하기 때문에 반환값이 null인지 따로 체크해줘야 하기 때문에 null 체크 코드를<br> 따로 작성해줘야 합니다. 

# as 연산자와 캐스팅 간의 차이점

as 연산자와 캐스팅의 가장 큰 차이는 **사용자 정의 형변환**을 다루는지에 대한 여부로써, as 연산자의 경우에는 임의의 객체를 원하는 타입으로
형변환할 경우, 지정한 타입이거나 지정한 타입을 상속한 타입이어야 하고 그 외의 경우에는 전부 실패합니다. <br>
<br>
반대로 캐스팅의 경우엔 객체를 지정한 타입으로 변환하기 위해서 사용자 정의 형변환이 개입될 수 있습니다. 대표적인 형변환 연산자는 값 타입에서
숫자 간의 형변환 연산자입니다. (ex) short <-> long

## 예제 코드
<pre><code class="C#">public class SecondType
{
   private MyType _value;

   public static implicit operator MyType(SecondType t)
   {
      return t._value;
   }
}

// Version one:
object o = Factory.GetObject(); // GetObject() 메서드는 SecondType 객체 반환

// o is a SecondType:
MyType t = o as MyType; // 만약 o가 MyType이 아니면 실패

if (t != null)
{
    
}
else
{
   // 오류 보고
}

// Version two:
object o = Factory.GetObject(); // GetObject() 메서드는 SecondType 객체 반환

try
{
    MyType t1;
    t1 = (MyType)o; // 만약 o가 MyType이 아니면 실패
}
catch (InvalidCastException)
{
   // 형변환 오류 보고
}
</code></pre>

- 위의 두 버전의 형변환은 모두 실패합니다.
- 첫 번째 버전에서 as는 런타임의 타입으로 결정이 되기 때문에, 런타임에 o가 SecondType이므로 MyType 혹은 MyType을 상속한 클래스가 아니므로 실패합니다. 물론 사용자 정의 연산자가 존재하지만 as 연산자는 사용자 정의 형변환을 허용하지 않기 때문에 최종적으로 형변환에 실패하게 됩니다.
- 두 번째 버전에서 o가 SecondType이므로 사용자 정의 형변환에 의해 MyType으로 변환이 가능한 것처럼 보이지만 이 방법 또한 실패합니다.
- 캐스트 연산자는 컴파일 타임에 코드를 생성하기 때문에, 변환을 시도할 시 코드 상에 있는 object 타입에서 MyType으로 변환을 시도하지만, object에서 MyType으로 변환하는 사용자 정의 형변환 연산자가 정의되어 있지 않기 때문에 실패합니다.
- 컴파일 타임에서 형변환에 실패하게 되면, 컴파일러는 런타임의 o가 MyType인지만 검사하는 코드를 생성합니다.
- 하지만 런타임에서는 o가 SecondType이고 이는 MyType이 아니기 때문에 최종적으로 변환에 실패하게 됩니다.
<br><br>
요약하자면, as나 is 연산자는 런타임에 객체의 타입을 확인하지만 반대로 cast 연산자는 컴파일 타임에 객체의 타입을 확인합니다.

# as 연산자를 사용할 수 없는 경우
as 연산자는 형변환을 할 수 없는 경우 null을 반환하는데, int와 같은 값 타입의 객체들은 null을 가질 수 없는 타입입니다. 이런 경우, 캐스팅 구문과
예외 처리 방법을 통해 해결을 할 수 있겠지만, as 연산자를 그대로 이용하되 nullable 타입으로 형변환을 수행한 후 그 값이 null인지를 확인하는 편이 더 낫습니다.

<pre><code class="C#">object o = Factory.GetValue();
var i = o as int ?;
if(i != null)
    Console.WriteLine(i.Value);
</code></pre>

# 정리
**캐스팅을 사용하는 것보단 as 연산자를 사용하는 것이 좋습니다.**
<br>as 연산자를 사용하는 방법이 더 안전하고 성능면에서도 런타임에 더 효율적으로 동작하기 때문입니다. 하지만 캐스팅과 as 연산자가 
형변환을 수행한다는 역할은 동일할지라도 동작 방법에서는 차이가 있기 때문에 이를 잘 인지하고 사용해야 예상치 못한 에러를 방지할 수 있습니다.

# 참조
- [참조1 - 형식 테스트 연산자 및 캐스트 식](https://docs.microsoft.com/ko-kr/dotnet/csharp/language-reference/operators/type-testing-and-cast)
- [참조2 - 사용자 정의 형변환 연산자](https://docs.microsoft.com/ko-kr/dotnet/csharp/language-reference/operators/user-defined-conversion-operators)