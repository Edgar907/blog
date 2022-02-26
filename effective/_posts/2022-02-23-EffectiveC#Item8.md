---
layout: post
title: Item 8 - 이벤트 호출 시에는 null 조건 연산자를 사용하라
description: >
hide_description: false
category: effective
image: https://images.unsplash.com/photo-1644329901564-1e47f5d1b81a?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1742&q=80
---

* this unordered seed list will be replaced by the toc
{:toc}

# 소개
이벤트를 발생시키는 작업은 언뜻보면 단순한 작업처럼 보이지만, 이벤트에 결합된 이벤트 핸들러가 없다면 NullReferenceException이 발생합니다. 이를
해결해주기 위해서는 이벤트 핸들러가 결합되어 있는지 확인하는 코드를 추가해주면 그만일 수도 있지만 여기서 또 다른 문제가 발생할 수 있습니다. 이벤트 핸들러의 유무를 확인하더라도 다른 스레드에서 이벤트 핸들러 처리에 관한 작업이 실행될지도 모르기 때문입니다.

# 예제 코드
```c#
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
위 코드는 Update 이벤트에 이벤트 핸들러가 결함되어 있지 않기 때문에 실행시키면 NullReferenceException 예외가 발생합니다. 
이를 해결하기 위해서는 이벤트 핸들러가 결합되어 있는지 확인하는 코드를 작성해줘야 합니다.

```c#
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

첫 번째 코드의 문제점을 보완하기 위하여 if 문을 통해 Updated 이벤트가 null이 아님을 확인했다고 하지만 다른 스레드가 이벤트 핸들러의 등록을 취소했다고 가정해보면 결국엔 똑같이 NullReferenceException 예외가 발생하게 될 것입니다.

```c#
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

위 버전의 코드가 위의 다른 두 코드에서 보여지는 문제점들을 발생시키지 않도록 권장되는 코드입니다. handler라는 로컬 변수에다가 Updated 이벤트를 복사하는 방식으로 문제를 해결하였습니다. 이런 경우, 다른 스레드가 이벤트에 대한 구독을 취소하더라도 로컬 변수에 담아둔 이벤트 핸들러는 여전히 존재하기 때문에 이벤트를 발생시키는데에 있어 문제가 없습니다.

하지만, 모든 이벤트 핸들러에 이와 같은 코드를 작성해놓기엔 여간 복잡해 보이는 것이 아닙니다.

# 너굴맨이 처리했으니 안심하라구!
![jpg](/assets/img/nugull.png)
```c#
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
C# 6.0에 추가된 null 조건 연산자를 이용하면 위와 같이 간결하게, 명확하게 표현을 할 수 있습니다. null 조건 연산자는 연산자의 왼쪽을 평가하여 이 값이 null이 아닐 경우 연산자 오른쪽의 표현식을 수행합니다. if 문으로 하는 null 체크와 다른 점은 null 체크와 메서드를 수행하는 과정이 원자적(atomic)으로 수행된다는 것입니다.

# 정리
이벤트를 if문으로 null 검사하는 것은 구식의 방법이며, 그 어떤 경우라도 null 조건 연산자(?.)를 이용하는 방식을 사용하라

# 참조
- [참조1 - ?.](https://docs.microsoft.com/ko-kr/dotnet/csharp/language-reference/operators/member-access-operators#null-conditional-operators--and-)