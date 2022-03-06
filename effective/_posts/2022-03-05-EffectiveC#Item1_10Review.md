---
layout: post
title: Item 1 ~ Item 10 Review
description: >
hide_description: false
category: effective
image: https://images.unsplash.com/photo-1644329901564-1e47f5d1b81a?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1742&q=80
---

Effective C#을 스터디한 내용을 정리할 겸 퀴즈 형식으로 리뷰해보려고 한다.<br>
문제 제작자 : operative
참고 :
- [참고 1 - Hyemi's Blog](https://ohhyemi.github.io/)
- [참고 1 - operative's Blog](https://operative.netlify.app/)

* this unordered seed list will be replaced by the toc
{:toc}
## <span style="color:#3a8791;">문제 1</span>
<span style="font-weight:bold">지역변수를 선언할 때는 var를 쓰는 것이 좋다고 한다. <br>var를 사용함으로써 얻을 수 있는 장단점을
간단하게 나열해보라</span>

장점 :
- 코드의 가독성을 높일 수 있습니다.
- 변수의 타입보다는 변수의 의미 파악에 더욱 집중할 수 있습니다.
- 올바른 타입을 알지 못하여 잘못된 타입을 명시적으로 지정하여<br> 성능이 저하되는 문제를 방지할 수 있습니다.

단점 : 
- 과도하게 사용하면 오히려 가독성이 떨어질 수 있습니다. 

## <span style="color:#3a8791;">문제 2</span>
<span style="font-weight:bold">다음 지문을 읽고 O, X로 답변하라</span><br>

**1) var는 C# 언어의 고유 특성이라고 하는 정적 타이핑을 훼손한다.**<br>
답변 : X<br>
설명 : var을 사용한다고 해서 정적 타이핑을 훼손하는 것은 아닙니다.

**2) var 사용 시 선언과 동시에 초기화가 필요하다.**<br>
답변 : O<br>

**3) 숫자 타입에도 var를 적극적으로 사용하는 것이 좋다.**<br>
답변 : X<br>
설명 : 정밀도 관련 문제가 발생될 수 있으므로 숫자 타입은 명시적으로 타입을 지정해주는 것이 좋습니다.

**4) LINQ를 사용할 때, 반환타입을 확실히 알지 못한다면 var를 사용하는 것이 더 좋다.**<br>
답변 : O<br>

**5) var를 사용하게 되면, Python, Ruby와 마찬가지로 동적 타이핑을 수행하는 것이 된다.**<br>
답변 : X<br>
설명 : var를 사용한다고 해서 런타임에 타입이 결정되는 동적 타이핑이 수행되는 것이 아니라, 할당 연산자(=) 오른쪽의
타입을 확인하여 왼쪽 변수의 타입을 결정하게 되는 것입니다.

## <span style="color:#3a8791;">문제 3</span>
<span style="font-weight:bold">다음 코드의 문제점을 설명해보고 올바른 코드를 작성해보라</span><br>
```csharp
List<string> animalList = new List<string> { "dog", "cat", "tuna", "lion", "squid" };

IEnumerable<string> q =
  from animal in animalList
   select animal;

var query = q.Where(s => s.StartsWith("d"));
```

일단, 코드를 실행했을 때 코드가 실행되지 않거나 하는 등의 문제는 발생하지 않습니다. q의 반환값은 IQueryable이지만
개발자가 IEnumerable로 명시적으로 지정하였기 때문에 성능이 크게 저하되는 문제가 발생됩니다. 

따라서, 만약 LINQ 구문을 이용할 때 정확한 반환타입을 알지 못한다면 아래와 같이 var을 써서 해결할 수 있습니다.
```csharp
List<string> animalList = new List<string> { "dog", "cat", "tuna", "lion", "squid" };

var q =
    from animal in animalList
    select animal;

var query = q.Where(s => s.StartsWith("d"));
```

## <span style="color:#3a8791;">문제 4</span>
<span style="font-weight:bold">C#에는 두가지 유형의 상수가 있다고 한다. 각각에 대하여 간단히 설명해보라</span><br>

C#에는 컴파일타임 상수와 런타임 상수가 있습니다.<br>
컴파일타임 상수
- const 키워드를 사용합니다.
- 컴파일 타임에 변수가 값으로 대체되고, 메서드 내부에서도 선언이 가능합니다.
- 내장 자료형에 대해서만 사용이 가능합니다.

런타임 상수
- readonly 키워드를 사용합니다.
- 컴파일 타임에 값으로 대체되는 것이 아닌 상수에 대한 참조로 컴파일 됩기 때문에 메서드 내에서는 선언이 불가능합니다.
- 반드시 생성과 동시에 초기화 될 필요가 없으며, 생성자에서 최초 1번 초기화가 가능합니다.
- const와는 다르게 어떤 타입이든지 상관없이 사용이 가능합니다.

## <span style="color:#3a8791;">문제 5</span>
<span style="font-weight:bold">다음 지문을 읽고 O, X로 답변하라</span><br>

**1)const가 붙은 변수는 컴파일 타임에 변수가 값으로 대체된다**<br>
답변 : O

**2) DateTime 자료형은 컴파일 타임 상수로 초기화할 수 있다**<br>
답변 : X<br>
설명 : DateTime은 내장 자료형이 아니기 때문에 const로 초기화하는 것이 불가능합니다.

**3) 런타임 상수는 생성자에서 초기화될 수 있다**<br>
답변 : O

**4) readonly를 사용하는 경우, 동일 클래스의 인스턴스라고 하면 모든 인스턴스가 동일한 값을 가진다**<br>
답변 : X<br>
설명 : 동일 클래스의 인스턴스라고 하면 모든 인스턴스가 동일한 값을 가지는 것은 readonly 키워드가 붙은 런타임 상수가 아닌
const 키워드가 붙은 컴파일타임 상수입니다. 

**5) const와 readonly 모두 선언과 동시에 값을 초기화해줘야 한다**<br>
답변 : X<br>
설명 : const의 경우에는 선언과 동시에 초기화가 필요하지만, readonly 같은 경우는 그렇지 않아도 됩니다.

## <span style="color:#3a8791;">문제 6</span>
<span style="font-weight:bold">다음 코드에서 발생될 수 있는 모든 오류를 찾아보라</span><br>
```csharp
class Test
{
    public const int val1;
    public readonly int val2;

    public Test(int _value)
    {
        val2 = _value;
    }
    public void TestMethod(int _value)
    {
        val2 = _value;

        const int val3 = 10;
        readonly int val4;
    }
}
```
1) public const int val1; 
- const는 선언 때 값을 초기화해줘야 합니다.

2) val2 = _value;
- const이던 readonly이던 초기화 후에는 값 변경이 불가능합니다.

3) readonly int val4;
- readonly 로는 메서드 내에서 선언이 불가능합니다.

## <span style="color:#3a8791;">문제 7</span>
<span style="font-weight:bold">as 연산자와 캐스팅 간의 가장 두드러지는 차이점이 무엇인가?</span><br>

사용자 정의 형변환을 다루는지에 대한 여부입니다. as 연산자의 경우에는 임의의 객체를 원하는 타입으로
형변환할 경우, 지정한 타입이거나 지정한 타입을 상속한 타입이어야 하고 그 이외의 경우는 전부 실패합니다.

반대로 캐스팅의 경우엔 객체를 지정한 타입으로 변환하기 위해서는 사용자 정의 형변환이 개입될 수 있습니다.

## <span style="color:#3a8791;">문제 8</span>
<span style="font-weight:bold">다음 지문을 읽고 O, X로 답변하라</span><br>

**1) as는 형변환을 수행할 수 없거나, null을 대상으로 형변환을 수행하는 경우 null을 반환한다**<br>
답변 : O

**2) 캐스트 연산자를 사용하는 경우 null 처리를 별도로 해줄 필요가 없다**<br>
답변 : X<br>
설명 : 캐스트 연산자를 사용하더라도 별도로 null 처리를 해줘야 합니다.

**3) 사용자 정의 형변환 연산자는 객체의 런타임 타입에 맞춰 수행된다**<br>
답변 : X<br>
설명 : 사용자 정의 형변환 연산자는 컴파일타임 타입에 맞춰 수행됩니다.

## <span style="color:#3a8791;">문제 9</span>
<span style="font-weight:bold">다음 코드의 문제점을 설명해보고 올바른 코드를 작성해보라</span><br>
```csharp
object o = Factory.GetValue();
double d = d as double;
```

문제점<br>
double은 값 타입이고 null이 될 수 없습니다. 
따라서 as 연산자를 그대로 이용하되 nullable 타입으로 형변환을 수행한 후 그 값이 null인지 확인하는 편이 
좋습니다.

```csharp
object o = Factory.GetValue();
var d = o as double ?;
if(d != null)
    Console.WriteLine(d.Value);
```

## <span style="color:#3a8791;">문제 10</span>
<span style="font-weight:bold">다음 지문을 읽고 O, X로 답변하라</span><br>

**1) C# 6.0에 새롭게 도입된 문자열 보간 기능으로 문자열을 만들 수 있다**<br>
답변 : O

**2) 문자열 보간 기능을 이용하면 동적 타입 검사를 수행한다**<br>
답변 : X<br>
설명 : 컴파일러 입장에서는 정적 타입 검사를 수행할 수 있기 때문에 개발자의 실수를 미연에 방지할 수 있다고
합니다.

**3) 보간 문자열을 사용하기 위해서는 문자열 앞에 ‘$’를 붙이고, 문자열로 변경할 표현식은 대괄호 내에 두는 것으로 합니다**<br>
답변 : X<br>
설명 : 대괄호가 아닌 중괄호 내에 두어야 합니다.

**4) 문자열이라면 전부 문자열 보간 기능으로 작성하는 것이 좋다**<br>
답변 : X<br>
설명 : 뭐든 과하면 좋지 않다...

## <span style="color:#3a8791;">문제 11</span>
<span style="font-weight:bold">다음 코드를 수정하고 그렇게 생각한 이유를 작성하라</span><br>

```csharp
Console.WriteLine($"The value of pi is {Math.PI}");
```

Math.PI는 값타입이기 때문에 object 타입으로 변환 시 박싱 현상이 발생할 수 있습니다. 이는 성능에 영향을 끼칠 수 있으므로
ToString()을 붙여 문자열 형식으로 변경해주는 것이 좋습니다.

```csharp
Console.WriteLine($"The value of pi is {Math.PI.ToString()}");
```

## <span style="color:#3a8791;">문제 12</span>
<span style="font-weight:bold">다음 코드의 실행 결과를 예측해보라</span><br>

```csharp
class Program
{
    static void Main(string[] args)
    {
        string name1 = null;
        Console.WriteLine($"The customer's name is {name1 ?? "missing"}");
        string name2 = "Edgar";
        Console.WriteLine($"The customer's name is {name2 ?? "Name is missing"}");
    }
}
```

답안 : <br>
The customer's name is missing<br>
The customer's name is Edgar

## <span style="color:#3a8791;">문제 13</span>
<span style="font-weight:bold">다음 지문들이 설명하는 개념에 대한 답을 적어보라</span><br>

**1) 문화권별로 다른 문자열을 생성하기 위해 무엇을 사용하는가?**<br>
답변 : FormattableString

**2) 심볼 그 자체를 해당 심볼을 포함하는 문자열로 대체해주는 연산자이며, 리팩토링 시 도움을 줄 수 있는 연산자는 무엇인가?**<br>
답변 : nameof

**3) 클래스 간의 결합도를 낮추기 위하여 사용이 권고 되는 것은 무엇인가?**<br>
답변 : 델리게이트(delegate)

## <span style="color:#3a8791;">문제 14</span>
<span style="font-weight:bold">13-(3) 문제의 답을 사용함으로써 얻을 수 있는 이점에 대하여 설명해보라</span><br>
- 타입 안정적인 콜백을 정의할 수 있습니다.
- 클래스 간의 상호 통신을 수행해야할 때 결합도를 낮출 수 있습니다.
- 런타임에서 통지 대상을 정할 수 있으며, 다수의 클라이언트에게 통지를 보낼 수 있습니다.

## <span style="color:#3a8791;">문제 15</span>
<span style="font-weight:bold">결합도에 반대되는 개념은 무엇이며, 두 개념에 대하여 간단히 설명해보라</span><br>

결합도 : 
- 소프트웨어의 한 요소가 다른 것과 얼마나 강력하게 연결되어 있는지, 또한 얼마나 의존적인지 나타내는 정도
- 결합도가 높다면
- 수정하려는 클래스를 이해하기 위해선 다른 클래스를 함께 이해해야 함
- 연관된 다른 클래스를 변경 -> 더불어 같이 변경시켜야 함
- 클래스의 재사용이 어려워짐

응집도 : 
- 소프트웨어의 한 요소가 기능을 수행하기 위해 얼마만큼의 연관된 책임과 아이디어가 뭉쳐있는지를 나타내는 정도
- 응집도가 낮으면
- 이해하기가 어려워짐
- 유지보수에 어려움을 겪을 수 있음

유지보수성이 높은 소프트웨어는 프로그램의 각 요소들이 결합도는 낮게, 응집도는 높게 구성되어야 합니다.

## <span style="color:#3a8791;">문제 16</span>
<span style="font-weight:bold">이벤트에 결합된 이벤트 핸들러가 없다면 어떤 오류가 발생되고, 이를 처리해주기 위해서는 어떻게 해야하는가?</span><br>

이벤트 핸들러가 없다면 NullReferenceException이 발생하고, 이를 확인해주는 코드를 작성해 줄 필요가 있습니다.

## <span style="color:#3a8791;">문제 17</span>
<span style="font-weight:bold">다음 코드들의 문제점을 각각 설명해보라</span><br>

**1)**<br>
```csharp
public class EventSource
{
    private EventHandler<int> Updated;

    public void RaiseUpdates()
    {
        counter++;
        Updated(this, counter);
    }

    private int counter;
}
```
이벤트 핸들러가 결합되어 있지 않기 때문에 실행시키면 NullReferenceException이 발생합니다.

**2)**<br>
```csharp
public class EventSource
{
    private EventHandler<int> Updated;

    public void RaiseUpdates()
    {
        counter++;
        if(Updated != null)
            Updated(this, counter);
    }

    private int counter;
}
```
Updated 이벤트가 null이 아님을 확인했다고 하지만 다른 스레드에 의하여 이벤트 핸들러의 등록이 취소되었다고
가정해보면 결국엔 똑같이 NullReferenceException이 발생합니다.

**3)**<br>
```csharp
public class EventSource
{
    private EventHandler<int> Updated;

    public void RaiseUpdates()
    {
        counter++;
        var handler = Updated;
        if(handler != null)
            Updated(this, counter);
    }

    private int counter;
}
```
이 코드는 위의 다른 두 코드에서 생길 수 있는 문제는 모두 해결할 수 있기 때문에 잘못된 코드가 아닙니다.
다만, 코드가 길어지고 모든 이벤트 핸들러에 이와 같은 코드를 작성해 놓기에는 너무 복잡합니다.

따라서 다음과 같이 null 조건 연산자(?.)를 이용하여 간략하게 작성할 수 있습니다.
```csharp
public class EventSource
{
    private EventHandler<int> Updated;

    public void RaiseUpdates()
    {
        counter++;
        Updated?.Invoke(this, counter);
    }

    private int counter;
}
```

## <span style="color:#3a8791;">문제 18</span>
<span style="font-weight:bold">null 조건 연산자(?.)가 if 문으로 하는 null 체크와 다른 점은 무엇인가?</span><br>

메서드를 수행하는 과정이 원자적으로 수행됩니다. 즉, 중간에 중단되지 않는다는 의미입니다.


## <span style="color:#3a8791;">문제 19</span>
<span style="font-weight:bold">박싱(Boxing)과 언박싱(Unboxing)의 개념에 대하여 간단히 설명해보라</span><br>

박싱(Boxing) :
- 값 형식을 참조 형식으로 변환
- 값 타입을 힙에 생성하기 위해 메모리를 힙 영역에 생성
- 값을 힙 영역에 할당된 메모리로 복사
- 참조할 변수에 할당된 메모리 주소를 할당

언박싱(Unboxing) : 
- 참조 형식을 값 형식으로 변환
- 박싱값인지 먼저 확인
- 박싱된 값이라면 값 타입 변수에 복사
- 박싱한 메모리와 언박싱한 메모리 2개 존재 (가비지 발생)

## <span style="color:#3a8791;">문제 20</span>
<span style="font-weight:bold">왜 개발자들이 코드를 작성할 때 코드에서 Boxing과 Unboxing이 발생하지 않도록 코드를 작성하는 것이 중요하다고 하는가? 또 이러한 문제점을 해결하기 위해 권장되는 방법은 무엇인가?</span><br>

가장 큰 이유는 성능(Performance) 때문입니다. 
박싱할 때에는 완전히 새로운 객체가 생성되어야 하며, 이는 할당 작업에 비하여 약 20배의 시간이 소요되고
언박싱 또한 캐스팅 연산 과정이 할당 작업보다 약 4배의 시간이 소요됩니다.

이러한 문제를 해결하기 위해선 Generic(제네릭) 클래스와 메서드를 이용해야 합니다.

## <span style="color:#3a8791;">문제 21</span>
<span style="font-weight:bold">상속 관계에서 일반 메서드를 재정의하기 위해 사용되는 한정자는 무엇인가</span><br>

new 입니다.

## <span style="color:#3a8791;">문제 22</span>
<span style="font-weight:bold">다음 지문을 읽고 O, X로 답변하라</span><br>

**1) 상속 관계에서 일반 메서드를 재정의하는 것이 가능하다**<br>
답변 : O

**2) 21번 문제의 답을 사용하면, 비가상 메서드를 가상 메서드로 만들어주는 것이다**<br>
답변 : X<br>
설명 : 아닙니다. new 한정자를 사용한다고 해서 비가상 메서드를 가상 메서드로 만들어주는 것이 아닌
클래스의 명명 범위 내에 새로운 메서드를 추가해주는 것입니다.

**3) 런타임에 수행도는 동적 바인딩의 경우에는 속도가 다소 느릴 수 있지만, 유연성이 높고 자유도가 높아 많이 이용된다고 한다**<br>
답변 : O

**4) 이미 널리 사용되고 있는 메서드가 있어서 일일히 찾아 수정하기가 어렵거나 수정할 수 없는 상황에서 “21번 문제”의 답을 사용하여 문제를 해결해 볼 수는 있다**<br>
답변 : O

