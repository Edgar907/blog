---
layout: post
title: Boxing & UnBoxing
description: >
    C# Boxing, Unboxing 개념 정리
hide_description: false
category: csharp
image: https://images.unsplash.com/photo-1616400619175-5beda3a17896?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1974&q=80
---

* this unordered seed list will be replaced by the toc
{:toc}

## 들어가기 전에...

C#에서는 Object가 모든 데이터를 다룰 수 있도록 하기 위해 모든 데이터 타입 및<br>
개발자들이 직접 정의하는 데이터 타입마저도 자동으로 Object 형식을 상속받는다<br>
고 합니다. 즉, Object는 모든 데이터 타입의 Base Class가 된다고 할 수 있습니다.
<br><br>
하지만 Object 타입은 참조(Reference) 형식이기 때문에 힙에 데이터를 할당하고,<br>
이와 반대로 int 혹은 double과 같은 타입은 값(Value) 형식이기 때문에 스택에 데이<br>
터를 할당합니다. 위에서 모든 데이터 타입은 Object 형식을 상속받는다고 하였는데,<br> 
그러면 어떻게 값 형식의 데이터를 Object에 담을 수 있는지 생각해보고자 합니다.
<br>
## <span style="color:#3a8791;">Boxing</span>
Object 형식은 값 형식의 데이터를 힙에 할당하기 위해 Boxing(박싱)이라는<br>
기능을 제공합니다.
<pre><code class="C#">[정수 변수 i를 Boxing하고 개체 o에 할당합니다.]
int i = 123;
// The following line "boxes i"
object o = i; // 박싱(묵시적) - 명시적(object)으로도 할 수 있음
</code></pre>

## <span style="color:#3a8791;">Unboxing</span>
반대로 힙에 있던 참조 형식 데이터를 값 형식 개체에 다시 할당하기 위해서는<br>
Unboxing(언박싱)이라는 기능을 사용합니다.
<pre><code class="C#">[o개체를 Unboxing하고 정수 변수 i에 할당합니다.]
o = 123;
i = (int)o; // 언박싱(무조건 명시적)
</code></pre>

## 요약
> Boxing : 값 형식을 참조 형식으로 변환
> * 값 타입을 힙에 생성하기 위해 메모리를 힙 영역에 생성
> * 값을 힙 영역에 할당된 메모리로 복사
> * 참조할 변수에 할당된 메모리 주소를 할당

> Unboxing : 참조 형식을 값 형식으로 변환
> * 박싱값인지 먼저 확인
> * 박싱된 값이라면 값 타입 변수에 복사
> * 박싱한 메모리와 언박싱한 메모리 2개 존재 (가비지 발생!)
> * 모든 객체가 값 형식으로 언박싱 되는 것은 아니고, 이전에 박싱이된 데이터들에<br>
한해서만 언박싱이 가능합니다. 또한 박싱하기 이전의 타입을 따라야 합니다.

## So Why?
그래서 왜 Boxing과 Unboxing을 알아야 하나요?
<br><br>
그 이유는 바로 **퍼포먼스**에 있습니다.<br>
MSDN에 의하면 값 형식을 박싱할 때에는 완전히 새로운 개체가 생성되어야 하며,<br>
이 작업에는 할당 작업보다 최대 **20배**에 가까운 시간이 걸리고 언박싱 또한 캐스팅<br> 
과정이 할당 작업보다 **4배**의 시간이 걸릴 수 있다고 합니다.<br>
실제로 최적화 관련 이슈가 있을 때마다 언급될 정도로 성능이 좋지 않다고 합니다.
<br>

## So...
그럼 사용하지 않는 것이 더 좋겠네요...?

최적화 및 속도가 중요한 프로그램에서는 사용하지 않는 편이 좋다고 할 수 있을 것<br>
같습니다. 그럼에도 사용하는 곳은 있다고 한다. 바로 **System.Collections.ArrayList**<br>
클래스인데 이 클래스에는 모든 자료형의 데이터를 넣을 수 있는 복합 자료형(Object <br>
타입으로 저장)입니다. 타입과 관계없이 모든 자료형의 데이터를 넣을 수 있으니 편리<br>
하겠지만, 성능은 책임지지 못하겠네요...

## How to solve?
**제네릭 타입(Generic Type)**을 이용하면 됩니다.