---
layout: post
title: Item 15 - 불필요한 객체를 만들지 말라
description: >
hide_description: false
category: effective
image: https://images.unsplash.com/photo-1644329901564-1e47f5d1b81a?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1742&q=80
---

* this unordered seed list will be replaced by the toc
{:toc}

# 소개
GC(Garbage Collector)는 개발자를 대신하여 메모리를 훌륭히 관리하며, 사용하지 않는 객체를 효율적으로 제거해줍니다.
이러한 작업이 아무리 효율적이라고 해도, 힙에서 새로운 객체를 생성하고 삭제하는 작업은 생각보다 많은 프로세서 시간을 사용하고 너무 많은 객체를 생성하면 결과적으로 퍼포먼스가 떨어지게 됩니다.
<br><br>
따라서 GC가 과도하게 동작하지 않도록 다음과 같은 사항들에 대하여 유념하고 있는 것이 좋습니다.

## 참조 타입의 지역 변수를 멤버 변수로 선언
모든 참조 타입의 객체는 그것이 설사 지역 변수라고 하더라도 동적으로 메모리를 할당하며, 이렇게 할당된 객체는 이 객체를
참조하는 상위 멤버가 삭제되면 가비지가 됩니다. 매우 자주 호출되는 메서드 내에 참조 타입의 변수가 있다면 호출될 때마다 메모리 할당을 반복하게 되고, 사용되는 메모리의 양이 많아져서 가비지 수집 작업이 수행될 가능성이 높아질 뿐만 아니라, 주기도 높아지고 결과적으로 퍼포먼스에 악영향을 미치게 됩니다.
<br><br>
이럴 때에는 참조 타입의 변수를 멤버 변수로 선언하여 객체를 한 번만 생성한 후에 재사용하도록 개선할 수 있습니다.

## 참조 타입의 지역 변수를 정적 멤버 변수로 선언
여러 클래스에서 참조되는 객체가 있다고 가정해보면, 아무리 해당 지역 변수를 멤버 변수로 선언한다고 해도 해당 객체를 클래스마다 혹은 화면마다 만들어줘야 하는 경우가 생기고, 결과적으로 객체가 잡고 있는 메모리가 늘어나게 되는 것이기 때문에 효율이 좋다고 할 수 없습니다.
<br><br>
이럴 때에는 해당하는 인스턴스를 정적 멤버 변수로 선언하여 최초로 호출했을 때 비로소 해당 객체를 생성하고, 이후에 호출될 때는 미리 생성한 객체를 이용하는 것이 좋다.
<br><br>
하지만 경우에 따라서 생성된 객체가 사용되지 않는데도 메모리에 오래 남아있을 수 있는 문제가 발생할 수 있습니다. 또한 Dispose() 메서드를 호출해야 할 시점을 결정할 수 없기 때문에 비관리 리소스를 삭제할 수 없다는 것도 큰 단점입니다.

## 변경 불가능한 타입
string 객체가 생성되면 이 객체가 가지고 있는 문자열의 내용은 수정이 불가능합니다. 하지만 프로그래밍을 하다보면 마치 string 객체 내의 문자열을 변경할 수 있는 것처럼 보이는데, 이는 문자열이 변경되는 것이 아니라 새로운 문자열을 가진 새로운 string 객체가 생성되는 것입니다. 당연한 얘기이지만 이전 문자열을 가지고 있는 객체는 가비지가 됩니다.
```csharp
class Program
{
    static void Main(string[] args)
    {
        string msg = "Hello, ";
        msg += "Edgar. ";
        msg += "Did you study Effective C# today?";

        Console.WriteLine(msg);

        // 내부로는 이러한 과정을 보입니다.
        string _msg = "Hello, ";
        string tmp1 = new String(_msg + "Edgar. ");
        _msg = tmp1; // "Hello, "는 가비지가 됨
        string tmp2 = new String(_msg + "Did you study Effective C# today?");
        _msg = tmp2; // "Hello, Edgar. "는 가비지가 됨

        Console.WriteLine(_msg);
    }
}
```
<br><br>
문자열 보간 방식을 이용해서도 충분히 해결이 가능하지만, 최종적으로 구성하려는 문자열을 만들기 위해서 복잡한 코드가
포함되어야 하는 경우에는 StringBuilder를 사용하는 것도 좋은 방법입니다.
```csharp
class Program
{
    static void Main(string[] args)
    {
        StringBuilder msg = new StringBuilder("Hello, ");

        msg.Append("Edgar. ");
        msg.Append("Did you study Effective C# today?");

        string finalMsg = msg.ToString();
        Console.WriteLine(finalMsg);
    }
}
```

# 정리
자주 사용하는 객체는 멤버 변수로 선언하여 객체의 생성 빈도를 줄이고, 좀 더 넓은 범위에서 사용되는 인스턴스의 경우에는
정적 멤버 변수로 선언하여 역시 객체의 생성 빈도를 줄이라는 것입니다. 객체의 생성은 즉 소멸을 가지고 있는 것이고 그 만큼
GC를 호출한다는 것이니 항상 유념해야 합니다.