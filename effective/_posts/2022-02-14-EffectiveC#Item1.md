---
layout: post
title: Item 1 - 지역변수 선언은 var를 사용해라
description: >
hide_description: false
category: effective
image: https://images.unsplash.com/photo-1644329901564-1e47f5d1b81a?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1742&q=80
---

* this unordered seed list will be replaced by the toc
{:toc}

## 1. 소개
* C# 3.0부터 메서드 범위에서 선언된 변수에 암시적 형식 var를 사용할 수 있습니다.
* 암시적 형식 지역 변수는 형식을 직접 선언하는 것처럼 강력한 형식이지만, 컴파일러가<br>
  형식을 결정합니다.
* 본 책에서는 지역변수를 선언할 시에 var 사용을 권장합니다.