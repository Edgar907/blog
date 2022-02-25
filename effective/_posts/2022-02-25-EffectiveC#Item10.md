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
베이스 클래스에서 virtual로 선언하지 않은 멤버를 재정의하려는 경우 new 한정자를 사용할 수 있지만, 오히려 메서드의 동작 방식을 모호하게 만들 수 있습니다.

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

동일한 객체를 상속받는 경우 동일한 메서드는 같은 동작을 할 것이라고 생각되는데, 사실 new 한정자는 비가상 메서드를 가상 메서드로
만들어주는 것이 아닌, 클래스의 명명 범위(naming scope) 내에 새로운 메서드를 추가하는 역할을 합니다.

애초에 virtual을 붙여 가상 메서드를 만들면 되지 않나? 라는 생각이 앞설 수 있는데, 그렇다고 베이스 클래스에 있는 모든 메서드를 가상 메서드로
변경하는 것은 안될 말이지 않는가. 어떤 메서드를 가상으로 선언할지에 대한 여부는 다형성이 필요한 메서드나 속성이 무엇인지를 우선 생각해보고 
반드시 다형성이 필요한 경우에만 가상 메서드를 사용해야 합니다.
<br>
new 한정자를 활용해도 좋은 경우는 베이스 클래스에서 이미 사용하고 있는 메서드를 재정의하여 완전히 새로운 베이스 클래스를 만들어야 하는 경우이거나
베이스 클래스를 변경할 수 없는 경우(유지보수 측면에서) 뿐이라고 할 수 있습니다.

# 정리
new 한정자는 자칫 잘못 사용하면 협업 등에 있어서 혼란을 초래할 수 있기 때문에 특수한 상황에서 신중하게 고민하여 사용해야 합니다.


