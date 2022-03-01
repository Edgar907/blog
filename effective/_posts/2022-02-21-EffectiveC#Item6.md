---
layout: post
title: Item 6 - nameof() 연산자를 적극 활용하라
description: >
hide_description: false
category: effective
image: https://images.unsplash.com/photo-1644329901564-1e47f5d1b81a?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1742&q=80
---

* this unordered seed list will be replaced by the toc
{:toc}

# 소개
분산 시스템이 널리 활용되면서 서로 다른 플랫폼 및 언어로 개발된 프로그램들 사이에서도 데이터를 주고 받을 일이 많아졌고, 이는 이질적인 플랫폼과 서로 다른 언어로 개발된 프로그램 사이에서 데이터를 주고 받아야 하는 일이 많아졌다는 것을 의미하기도 합니다. 이러한 차이를 극복하기 위해 이름이나 문자열 식별자에 의존하는 라이브러리들이 많이 사용되는데 여기서 **타입 정보 손실**이라는 문제가 발생합니다.
<br><br>
이로 인하여, 타입 정보를 활용해서 추가 기능을 제공하는 개발 도구를 사용하는데에 어려움을 겪고, 정적 타입 언어의 주요 장점을 상실하게 됩니다.
<br><br>
이에 C# 6.0에는 nameof()라는 연산자가 추가되었고, 이 키워드는 심볼 그 자체를 해당 심볼을 포함하는 문자열로 대체해줍니다.

# 예시 코드
아래 코드는 nameof() 연산자의 가장 간단한 사용 예제입니다.
<pre><code class="C#">private string Name
{
    get
    {
        return name;
    }
    set
    {
        if(value != name)
        {
            name = value
            PropertyChanged?.Invoke(this, 
                new PropertyChangedEventArgs(nameof(name)));
        }
    }
}
</code></pre>

nameof() 연산자를 사용했기 때문에 프로퍼티의 이름을 변경할 경우에도 이벤트의 인자로 전달해야하는 string도 
쉽게 변경이 가능합니다.
<br>
또한 심볼의 이름을 평가하며, 타입, 변수, 인터페이스, 네임스페이스 등에 사용할 수 있지만 제네릭 타입을 사용할 경우에는 타입 매개변수를
지정한 **닫힌 제네릭 타입**만을 사용할 수 있습니다.

### 참고
#### 열린 제네릭 타입
```C#
Stack<T>
```
위와 같이 타입 매개변수가 T 꼴로 되어 있는 경우

#### 닫힌 제네릭 타입
```C#
Stack<int>
```
위와 같이 타입 매개변수가 int와 같이 지정된 경우

# 예시코드
<pre><code class="C#">public static void ExceptionMessage(object thisCantBeNull)
{
    if(thisCantBeNull == null)
    {
        throw new ArgumentNullException(nameof(thisCantBeNull), "We told you this cant be null");
    }
}
</code></pre>
위 코드에서 보여지는 것과 같이 nameof()연산자를 사용하면 심볼의 이름을 완전히 바꾸거나 수정할 경우에 쉽게
그 변경 사항을 반영할 수 있습니다.

# 예시 코드
<pre><code class="C#">public static string Append(this string str, string appendString)
{
    if(str == null)
    {
        throw new ArgumentNullException("str");
    }
    if(appendString == null)
    {
        throw new ArgumentNullException("appendString");
    }

    str = string.Concat(str, appendString);

    return str;
}
</code></pre>
<pre><code class="C#">public static string Append(this string str, string appendString)
{
    if(str == null)
    {
        throw new ArgumentNullException(nameof(str));
    }
    if(appendString == null)
    {
        throw new ArgumentNullException(nameof(appendString));
    }

    str = string.Concat(str, appendString);

    return str;
}
</code></pre>

첫 번째 코드의 경우에는 ArgumentNullException의 파라미터 이름이 하드코딩 되어 있고 
두 번재 코드의 경우에는 파라미터가 nameof()연산자로 감싸져 있습니다.
<br><br>
표면적으로 보기에는 하드코딩 되어 있는 코드가 직관적이기 때문에 좋아보인다고 생각이 될 수도 있지만
시간이 지난 뒤에 Append() 메서드의 매개변수 중 appendString이 appStr로 이름이 변경되었다고 가정을 해봅시다.

Visual Studio에서 제공하는 리팩터링 기능을 이용하면 1번 코드 같은 경우에는 하드 코딩이 되어 있기 때문에 자동으로
변환을 해주지 못하지만 2번 코드에서는 ArgumentNullException에 있는 매개변수도 변경대상이 됩니다.

위와 같이 단순하고 짧은 코드에서는 직접 발견을 하고 수정을 할 수 있겠지만, 코드가 길어지고 복잡한 경우에는 자칫 놓칠 수 있는 부분이라고 생각을 할 수 있을 것 같습니다. 이 때 nameof() 연산자를 이용하면 이러한 실수를 미연에 방지할 수 있다는 얘기가 될 수 있습니다.

# 정리
- nameof() 연산자를 활용하면 변수, 형식, 멤버의 이름 등이 수정되어도 문제를 발생시키지 않습니다.
- 타입 자체를 문자열로 출력하기 때문에 이러한 출력이 필요할 때 불필요한 하드코딩을 줄여 리팩토링 시 도움이 됩니다.


# 참조
- [참조1 - nameof()](https://docs.microsoft.com/ko-kr/dotnet/csharp/language-reference/operators/nameof)