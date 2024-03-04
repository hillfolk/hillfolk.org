+++
title = "Go Functional Options Pattern"
author = ["Hillfolk"]
date = 2024-03-01
lastmod = 2024-03-04T16:50:46+09:00
tags = ["golang", "Go", "Mistakes", "option", "pattern"]
categories = ["posts"]
draft = false
weight = 100
+++

## 함수형 옵션 패턴 (Functional Options Pattern) {#함수형-옵션-패턴--functional-options-pattern}

함수형 옵션 패턴은 함수가 선택적 인수를 허용하는 함수형 프로그래밍 에서 유래했다. 함수형 옵션 패턴을 사용하여 기존 함수 구조를 손상시키지 않고 확장할 수 있는 유여한 인터페이스를 제공한다.

Go에서는 구조체를 단순화 하기 위해서 사용하며 서로 다른 매개변수를 가진 많은 생성자를 정의하는 대신 다양한 함수 옵션을 허용하여 단일 생성자를 정의할 수 있다.

```GO

type ClientOptions struct {
  Url    string
  Port   int
  Method string
}

type Option func(*ClientOptions) error

func WithUrl(url string) Option {
  return func(co *ClientOptions) error {
      co.Url = url
      return nil
  }
}

func WithPort(port int) Option {
  return func(co *ClientOptions) error {
      co.Port = port
      return nil
  }
}

func WithMethod(method string) Option {
  return func(co *ClientOptions) error {
      co.Method = method
      return nil
  }
}

func NewClient(opts ...Option) (*ClientOptions, error) {
  var co ClientOptions
  for _, opt := range opts {
      err := opt(&co)
      if err != nil {
         return nil, err
      }
  }
  return &co, nil
}

func main() {
  client, err := NewClient(WithUrl("http://localhost"), WithPort(8080), WithMethod("GET"))
  if err != nil {
      panic(err)
  }

  log.Println(client.Port)
  log.Println(client.Url)
  log.Println(client)

}

```


### 결론 {#결론}

함수형 옵션 패턴은 가변적인 파라메터가 있을때 생성자 함수의 로직을 단순화 하고 유지 보수성을 높여 줄수 있다. 기존 코드의 변경 없이 옵션을 추가할 수 있으므로 재사용성이 높은 코드를 작성 할 수 있다.


### 참고 {#참고}

-   <https://product.kyobobook.co.kr/detail/S000211704725>
-   <https://dev.to/kittipat1413/understanding-the-options-pattern-in-go-390c>
-   <https://dev.to/zaf07/golang-functional-options-pattern-o4l>
