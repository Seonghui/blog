---
title: "[React] Flux 개념 이해하기"
tags: [react]
categories: React
---

> Flux 개념 정리

# Flux 등장 배경

Flux는 MVC 패턴의 문제점을 보완하기 위해 만들어진 디자인 패턴이다. 그렇기 때문에 왜 등장했는지부터 짚고 넘어가면 좋다. 등장 배경에 대해 잘 설명된 문서가 있어서 [링크](https://bestalign.github.io/2015/10/06/cartoon-guide-to-flux/)로 대체한다.

요악하자면 MVC(Model-View-Controller) 패턴으로 개발하던 페이스북이 알림(notification)버그를 발견하고, 이를 근본적으로 해결하는 과정에서 Flux가 탄생하게 되었다는 얘기다. MVC 패턴은 작은 애플리케이션의 경우에는 별 문제가 없지만, 대규모 애플리케이션의 경우라면 엄청나게 복잡해진다는 단점을 가지고 있다.

![mvc](/images/react-flux/flux-mvc-1.png)
MVC 패턴은 **컨트롤러(Controller), 모델(Model), 뷰(View)** 세 가지 개념으로 이루어져있다. 작은 애플리케이션의 경우 모델과 뷰가 적어서 MVC 패턴이 큰 문제없이 작동한다.

> - 컨트롤러: 모델에 명령을 보내서 모델의 상태를 변경할 수 있음

- 모델: 모델의 상태에 변화가 있을 때 컨트롤러와 뷰에 통보
- 뷰: 모델로부터 정보를 얻어와 결과물로 반영함

![mvc](/images/react-flux/flux-mvc-2.png)
하지만 대규모 애플리케이션은 모델과 뷰가 많아서 엄청나게 복잡해진다. 모델이 많아지면서 이를 표현하기 위한 뷰들이 많아지고, 뷰를 통해 입력된 값이 모델에 영향을 주면서 엄청나게 복잡해진다. 이를 타개하기 위해 flux 아키텍처가 만들어졌다.

# Flux 패턴

![flux](/images/react-flux/flux-1.png)
Flux는 페이스북에서 MVC의 문제를 해결할 목적으로 고안한 애플리케이션 아키텍처이다. Flux 아키텍쳐의 가장 큰 특징은 단방향 **데이터 흐름(unidirectional data flow)**이다. 말 그대로 한 방향으로 데이터가 흐르는 것이다. 뷰를 통해 입력된 값은 디스패쳐를 통해 스토어로 전달되고, 스토어는 이 변경된 값을 다시 뷰에게 전달하는 역할을 한다. Flux 애플리케이션은 세 가지 주요 파트로 구성되는데, 각각 ​**디스패처(Dispatcher), 스토어(Store), 뷰(View)**이다.

# 액션 생성자(The action creator)

Flux 아키텍쳐의 주요 파트를 알아보기 전에 **'액션 생성자(The action creator)'** 라는 개념을 알고 넘어가야 한다. 액션 생성자란 모든 변경사항과 사용자와의 상호작용이 거처가야 하는 액션의 생성을 담당하고 있다. 애플리케이션의 상태를 변경하거나 뷰를 업데이트하고 싶다면 액션을 생성해야만 한다.

액션 생성자가 하는 일은 마치 전보 기사가 알파벳을 모스 부호로 바꾸는 것처럼 특정 포맷에 맞게 메시지를 작성하는 것과 같다. 그리고 액션 생성자가 특정 액션이 적힌 메시지를 다 작성하면 바로 디스패쳐에게 넘겨준다.

# Flux 아키텍쳐의 세 가지 주요 파트

앞서 말했듯, Flux 애플리케이션은 세 가지 주요 파트로 구성되어 있다.

## 1.디스패쳐(Dispatcher)

**디스패쳐는 Flux 어플리케이션의 중앙 허브로 모든 데이터의 흐름을 관리한다.** 스토어의 콜백함수들이 등록되어 있으며, 각각의 스토어를 직접 등록하고 콜백함수를 제공한다. 비유를 하자면 전화 교환수와도 같다. 액션 생성자한테서 액션을 받으면, 여러 스토어에게 그 액션을 보내는 역할을 한다.

## 2. 스토어(The stores)

**애플리캐이션의 상태를 저장한다.** 모든 상태 변경은 스토어에 의해서 결정되고, 상태 변경을 위한 요청을 스토어에 직접 보낼 수 없다. 무조건 액션 생성자->디스패처 흐름으로 액션을 보내야 상태 변경이 가능해진다. 스토어의 상태 변경이 될 때마다 스토어는 변경 이벤트를 내보내는데, 이 이벤트는 컨트롤러 뷰에게 상태가 변경되었다는 것을 알려주게 된다.

## 3. 컨트롤러 뷰(Controller view)와 뷰(View)

**상태를 가져오고, 유저에게 입력받을 화면을 보여준다.** 리액트에서는 즉 리액트 컴포넌트에 해당한다. 컨트롤러 뷰와 뷰의 차이는 리액트에서의 부모 컴포넌트와 자식 컴포넌트와도 같다. 스토어가 상태를 변경되었다고 컨트롤러 뷰에게 알려주면, 컨트롤러 뷰는 자신의 아래에 있는 모든 자식 뷰에게 새로운 상태를 넘겨준다.

# Flux는 어떻게 동작할까?

## 준비 단계 (The setup)

![flux](/images/react-flux/flux-2.png)

1. 스토어는 디스패쳐에게 액션이 들어오면 알려달라고 말해둔다.
2. 컨트롤러 뷰는 스토어에게 최신 상태를 묻는다.
3. 스토어가 컨트롤러 뷰에게 상태를 주면 렌더링하기 위해 모든 자식 뷰에게 상태를 넘겨준다.
4. 컨트롤러 뷰는 스토어에게 상태가 바뀔 때 알려달라고 다시 부탁한다.

## 데이터의 흐름 (The data flow)

일단 액션으로 발생한 모든 데이터는 디스패쳐 -> 스토어 -> 뷰로 흘러간다.
![flux](/images/react-flux/flux-3.png)

1. 뷰는 액션 생성자에게 액션을 준비하라고 말한다.
2. 액션 생성자는 액션을 포맷에 맞게 만들어서 디스패쳐에게 넘겨준다.
3. 디스패쳐는 들어온 순서에 따라 알맞은 스토어로 보낸다.
4. 스토어는 상태가 변경되었기 때문에 컨트롤러 뷰에게 이 사실을 알린다.
5. 컨트롤러 뷰는 자신의 아래에 있는 자식 뷰에게 모든 상태를 넘겨준다.

# Reference

- [Wikipedia](https://ko.wikipedia.org/wiki/%EB%AA%A8%EB%8D%B8-%EB%B7%B0-%EC%BB%A8%ED%8A%B8%EB%A1%A4%EB%9F%AC)
- [Flux 한국어 번역 페이지](http://haruair.github.io/flux/)
- [Flux로의 카툰 안내서](https://bestalign.github.io/2015/10/06/cartoon-guide-to-flux/)