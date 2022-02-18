---
layout: post
title: Item 2 - const보다는 readonly가 좋다
description: >
hide_description: false
category: effective
image: https://images.unsplash.com/photo-1644329901564-1e47f5d1b81a?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1742&q=80
---

* this unordered seed list will be replaced by the toc
{:toc}

# C#에서의 상수
C#에는 컴파일 타임 상수와 런타임 상수가 있습니다. <br>
**결론부터 얘기하자면 컴파일 타임 상수보다는 런타임 상수를 사용하는 편이 좋습니다.**<br>
컴파일 타임 상수가 약간 더 빠르지만 성능면에서 차이가 크지 않지만, 런타임 상수를 사용함으로써 유연성을 얻을 수 있습니다.
<br>
컴파일 타임 상수는 성능이 중요시되고, 상수의 값이 절대로 어떤 경우에도 바뀌지 않을 때에만 제한적으로 사용하는 것이 좋습니다.

# 비교
**런타임 상수는 readonly 키워드를 사용 / 컴파일 타임 상수는 const 키워드를 사용**

## const
* 컴파일 타임 상수입니다.
* 컴파일 타임에 변수가 값으로 대체되어, 메서드 내부에서도 선언이 가능합니다.
* 컴파일 타임에 상수를 리터럴로 대체해야 하기 때문에, 내장된 숫자형, enum, 문자열, null과 같은 내장 자료형에만 사용이 가능합니다.

## readonly
* 런타임 상수입니다.
* 컴파일 타임에 값으로 대체되는 것이 아닌 상수에 대한 참조로 컴파일 되어 메서드 내에서는 선언이 불가능합니다.
* 반드시 생성과 동시에 초기화 될 필요가 없으며, 생성자에서 최초 1번 초기화가 가능하지만 그 이후에는 불가능합니다.
* 내장 자료형에만 사용이 가능한 const와는 다르게 어떤 타입과도 함께 사용될 수 있습니다.

## 요약
* readonly 키워드로 선언된 상수는 컴파일 타임에는 상수에대한 참조로 컴파일 되고, 런타임에 값이 평가됩니다.
* const 키워드로 선언된 상수는 컴파일 타임에 값으로 대체됩니다.

## 정리
결국 성능을 선택한다면 const가 좋을 것이고, 유연성을 선택한다면 readonly가 좋다고 할 수 있습니다. 하지만, 그 차이는 미세하기 때문에
정말 특수하거나 성능이 아주 중요시되는 경우를 제외하고는 readonly를 사용하는 것을 권장합니다.
