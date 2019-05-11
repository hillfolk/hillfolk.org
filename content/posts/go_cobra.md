+++
title = "Go Cobra 사용하기 Go Cobra 사용하기"
author = ["Hillfolk"]
date = 2019-05-01
lastmod = 2019-05-11T23:17:32+09:00
tags = ["go", "MongoDB", "go-mongo-driver"]
categories = ["posts"]
weight = 100
draft = true
+++

Cobra는 강력한 Cli 라이브러리로서 Go 기반에서 구동된다. 해당 모듈은 docker, kubernetes, hugo 등에서 사용될 정도로 안정적인 라이브러리 이다.

[cobra github](https://github.com/spf13/cobra)


## Install {#install}

go get을 이용해서 package를 설치하고 코드에 import 해준다.

```nil

go get -u github.com/spf13/cobra/cobra

```

```nil

import "github.com/spf13/cobra"

```


## Cobra Generator {#cobra-generator}

Cobra Generator를 사용하면 손쉽게 Cobra를 적용할수 있다. cobra 팩키지가 설치된 경우 자동으로 cobra 프로젝트를 추가하거나 Command을 추가하는 명령을 사용할 수 있다.
Generator는 $GOPATH 하위 리소스에서 사용가능하다.


### 초기화 {#초기화}

init 명령으로 cobra에게 적합한 프로젝트 구성 및 자동으로 cobra 코드를 생성해 준다.

```nil
cobra init github.com/spf13/newApp
```


### 명령어 추가 {#명령어-추가}

main.go 파일이 위치한 폴더에서 아래 명령을 수행하면 해당 명령어가 추가되는 코드를 생성해 준다.
<https://github.com/spf13/cobra/blob/master/cobra/README.md#cobra-generator>

```nil
cobra add server
```


## 수동으로 명령어 추가 {#수동으로-명령어-추가}

Cobra Generator 를 사용하지 않는 경우 프로젝트에 직접 main.go 파일과 rootCmd 파일을 만들어야 하며 사용할 명령어를 직접 구현해야 한다.
<https://github.com/spf13/cobra>