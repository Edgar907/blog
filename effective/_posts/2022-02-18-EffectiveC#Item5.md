---
layout: post
title: Item 5 - 문화권별로 다른 문자열을 생성하려면 FormattableString을 사용하라
description: >
hide_description: false
category: effective
image: https://images.unsplash.com/photo-1644329901564-1e47f5d1b81a?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1742&q=80
---

* this unordered seed list will be replaced by the toc
{:toc}

# FormattableString 이란?
- C# 6.0에서 추가된 클래스입니다.
- **문자열 보간 기능('$')**의 결과로 생성되는 반환값은 문자열일 수도 있지만 FormattableString을 상속한 타입일 수도 있습니다.<br>
  (즉, 용도에 따라서 두 가지를 나눠서 사용해야 합니다.)
- **아래 코드에서 third 변수를 var로 선언하면 타입이 string 객체가 될 수도 있겠지만, FormattableString을 상속한 타입의 객체가 될 수도 있습니다.**

<pre><code class="C#">string first = $"It's the {DateTime.Now.Day} of the  {DateTime.Now.Month} month.";
FormattableString second = $"It's the {DateTime.Now.Day} of the  {DateTime.Now.Month} month.";
var third = $"It's the {DateTime.Now.Day} of the  {DateTime.Now.Month} month.";
</code></pre>

사소한 차이로 인하여 string 객체 혹은 FormattableString으로 인식될 수 있으므로<br> var을 사용하고자 한다면 각별한 주의가 요구됩니다.
- string을 매개변수로 받는 메서드에 var로 생성된 변수를 넘겨주게 되면 당연하지만 var는 string이 되고 이는 FormattableString 객체가 아니므로
문제가 발생할 수 있습니다.
- string과 FormattableString을 모두 받아들일 수 있는 오버로딩 메서드를 작성하게되면, 이 경우에도 var로 선언한 변수가 string 타입의 오버로딩된
메서드를 호출하게 됩니다.

# FormattableString 활용
- **FormattableString 객체**라면 컴퓨터에서 지정된 문화권을 고려하여 문자열을 생성할<br> 수 있습니다.
- 예를들어, 소수를 출력할 때 미국이라면 소수점 기호 '.', 유럽권의 국가라면 소수점 기호<br> '.'가 생성되도록 할 수 있습니다.
<pre><code class="C#">using System;
using System.Globalization;

namespace Item5
{
    class Program
    {
        static void Main(string[] args)
        {
            FormattableString fs = $"{Math.PI}";
            Console.WriteLine(fs); // 미국
            Console.WriteLine(ConvertToGerman(fs)); // 유럽 - 독일
        }

        public static string ConvertToGerman(FormattableString src)
        {
            return string.Format(
                CultureInfo.CreateSpecificCulture("de-DE"),
                src.Format,
                src.GetArgument(0));
        }
    }
}
</code></pre>

## 실행결과
<pre><code class="C#">3.14159265358979 // 미국
3,14159265358979 // 유럽 - 독일
</code></pre>

# 정리
문자열 보간 기능은 이러한 글로벌화 혹은 지역화에 필요한 대부분의 기능을 갖추고 있고 내부적으로 복잡함도 잘 감주고 있기 때문에 사용이 어렵지 않습니다.
즉, 문화권을 임의로 지정해야 하는 경우에는 명시적으로 FormattableString 타입의 객체를 생성하도록 코드를 작성하고 이 객체를 통해 문자열을 얻어 오는 방법을 사용하는 것을 권장합니다.

# 참조
- [참조1 - FormattableString Class](https://docs.microsoft.com/en-us/dotnet/api/system.formattablestring?view=net-5.0)
- [참조2 - 문자열 보간](https://docs.microsoft.com/ko-kr/dotnet/csharp/language-reference/tokens/interpolated)