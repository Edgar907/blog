---
layout: post
title: Item 10 - 베이스 클래스가 업그레이드된 경우에만 new 한정자를 사용하라
description: >
hide_description: false
category: effective
image: https://images.unsplash.com/photo-1644329901564-1e47f5d1b81a?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1742&q=80
---

* this unordered seed list will be replaced by the toc
{:toc}

# 소개
상속 관계에서 메서드를 재정의 하는 경우 virtual과 override 키워드를 사용해서 재정의를 할 수 있습니다. 그러나 virtual로 선언하지 않은
일반 메서드를 재정의하려는 경우에는 new라는 한정자를 사용할 수 있습니다.

```C#
object c = MakeObject();

// MyClass 타입의 참조를 이용하여 메서드를 호출
MyClass c1 = c as MyClass;
c1.MagicMethod();

// MyOtherClass 타입의 참조를 이용하여 메서드를 호출
MyOtherClass c12 = c as MyOtherClass;
c12.MagicMethod();
```

MyOtherClass가 MyClass를 상속했다는 것을 알고 위 코드를 보면 같은 동작을 할 것이라고 예상됩니다.

```C#
public class MyClass
{
    public void MagicMethod()
    {
        Console.WriteLine("MyClass");
    }
}

public class MyOtherClass : MyClass
{
    // MagicMethod를 재정의
    public new void MagicMethod()
    {
        Console.WriteLine("MyOtherClass");
    }
}
```

동일한 객체를 상속받는 경우 동일한 매서드는 같은 동작을 할 것이라고 생각되는데, new 한정자를 사용하는 경우에는 다른 동작을 하기 때문에
이는 혼란을 초래할 수 있다고 합니다. 베이스 클래스(이하 부모 클래스)에 A라는 기능을 가진 Func() 메서드를 자식 클래스에서 B라는 기능을
가진 동일한 이름의 Func() 메서드로 new 한정자를 이용하여 선언했다고 생각을 해보면 됩니다. 즉, 개발자는 부모 클래스의 A라는 기능을 이용하고 
싶었는데, 자신의 의도와는 다르게 B라는 기능이 호출되는 것이라고 할 수 있습니다.
<br>
<br>
new 한정자는 virtual 키워드가 붙지 않은 비가상 메서드(즉, 일반 메서드)를 가상 메서드로 만들어주는 것이 아니라 클래스의 명명 범위 내에 새로운
메서드를 추가하는 역할을 수행합니다. 일반 메서드는 정적으로 바인딩 되기 때문에 파생 클래스에서 새롭게 정의하고 있는 메서드가 있는지 찾지 않고
곧바로 수행을 하는 반면에 가상 메서드는 동적으로 바인딩되므로 런타임에 객체의 타입이 무엇이냐에 따라 그에 부합하는 메서드를 찾아내어 호출합니다.

### <span style="color:#3a8791;">정적 바인딩 vs 동적 바인딩
바인딩의 개념을 한 문장으로 설명하면 "함수를 호출하는 부분에 함수가 위치한 메모리 번지로 연결시켜주는 것"이라고 할 수 있습니다.
<br>
바인딩이 되는 시점을 기준으로 정적 바인딩과 동적 바인딩으로 나눌 수 있습니다.

#### 정적 바인딩
- 컴파일 타임
- 이미 실행할 함수가 빌드 때부터 결정이 되었기 때문에 런타임 때 함수를 찾아갈 필요가 없어 빠릅니다.
- 하지만 실행 전에 미리 다 정의를 해놓아야 하기 때문에 유연성이 상당히 결여됩니다.

#### 동적 바인딩
- 런타임
- 실행 중에 함수의 포인터를 보고 직접 해당 함수를 찾아가 실행하기 때문에 속도가 다소 느립니다.
- 하지만 유연성이 높고, 자유도가 높아 더 많이 이용됩니다.

# 그럼 new 한정자는 언제 사용해야 하나?
- 부모 클래스에서 이미 사용하고 있는 메서드를 재정의하여 완전히 새로운 부모 클래스를 만들어야 하는 경우
- 이미 널리 사용되고 있는 메서드가 있어서 일일히 찾아 수정하기가 어렵거나 수정할 수 없는 경우

# 정리
사실 이 Item 10 부분만 몇 번을 반복해서 읽어도 저자가 무엇을 얘기하고 싶은건지 확실하게 이해를 할 수 없었다.
그럼에도 나름의 요약을 해보자면, virtual 키워드가 붙지 않은 일반 메서드도 new 한정자를 사용하여 재정의를 하는
방법이 있긴 하지만, 베이스 클래스가 업그레이드되어 메서드의 이름이 충돌하는 경우는 "매우" 특별한 경우라 이 때도
"고려"는 해보되 신중하게 사용해야 되며, 이외의 경우에는 장기적인 관점에서 혼란을 야기할 수 있으므로 사용을 지양
하는 것이 좋다라고 정리할 수 있을 것 같다.