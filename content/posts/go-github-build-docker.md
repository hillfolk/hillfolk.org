+++
title = "Go 서비스를 Github Action 을 이용해서 AWS ECS 에 배포하기"
author = ["Hillfolk"]
date = 2020-07-15
lastmod = 2020-07-15T22:29:14+09:00
tags = ["github", "actions", "ecs", "ecr", "docker"]
categories = ["posts"]
draft = true
weight = 100
+++

## Slide1 {#slide1}


### 배포 자동화 시스템 소개 {#배포-자동화-시스템-소개}

Go 서비스의 원할한 운영 및 배포를 위한 시스템 구성을 소개 하도록 하겠습니다.

사용 Tool
클라우드 : AWS
소스 레파지토리 : Github
Docker 컨테이너: ECS


#### API 서비스 구성 {#api-서비스-구성}

{{< figure src="/ox-hugo/aws-api-service-struct.png" >}}


#### 배포 자동화 프로세스 {#배포-자동화-프로세스}

{{< figure src="/ox-hugo/aws-depory.png" >}}


## Slide2 {#slide2}


### 배포 테스트용 Go API 서버 만들기 {#배포-테스트용-go-api-서버-만들기}

Echo 와 Swagger 를 설정한 초간단 API 를 구성해 보겠습니다.

-   [Simple Code](https://github.com/hillfolk/go-rest-api-templte/blob/develop/main.go)

-   Echo-Swagger 소개
    Echo-Swagger 는 Echo기반의 컨트롤러를 자동으로 문서화 해주는 기능을 해주는 라이브러리 입니다. Ehco 사용시 사용자 및 협업 하는 개발자 에게 API 문서를 제공할때 유용 합니다.

    <https://github.com/pangpanglabs/echoswagger/tree/v2/examples>

-   간단 팀: 코드 버전 지정해서 빌드 하기
    Go App 을 빌드할때 -ldflags 옵션을 사용하면  Git 레파지토리를 지정한 Tag 를 코드에  삽입해서 어플리케이션의 빌드 넘버를 자동으로 마킹해서 버전관리를 쉽게 할수 있습니다.

<!--listend-->

```nil

go build -ldflags "-X main.Version=`git describe --tags` -X main.Commit=`git rev-parse --short HEAD`" -o bin/go-rest-api-template

```


## Slide3 {#slide3}


### Docker를 사용하는 이유 {#docker를-사용하는-이유}

도커는 컨테이너형 가상화 기술을 구현하는 도구로서 어플리케이션 배포에 특화 되어 있고 컨테이너 중심으로 운영된다. 도커는 기존의 가상화 기술보다 더 가볍게 동작하기 때문에 운영환경은 물론 테스트환경에서도 컨테이너를 사용할 수 있다.
아래 그림과 같이 Docker의 어플리케이션은 격리되어 있지만 Host OS는 공유 해서 사용한다. 반면 가상 머신은 Guest OS 로 독립적으로 운영된다.

{{< figure src="/ox-hugo/docker-vm-container.png" >}}


#### Docker 장점 {#docker-장점}

도커를 사용하게 되면 여러가지 장점이 있지만 아래 4가지가 가장 큰 장점이다.

-   변화하지 않는 실행환경으로 멱등성(Idempotency) 확보
-   코드를 통한 실행 환경 구축 및 애플리케이션 구성
-   실행 환경과 애플리케이션의 일체화로 이식성을 향상
-   시스템을 구성하는 어플리케이션 및 미들웨어의 관리 용이성


#### Docker 단점 {#docker-단점}

도커를 사용할 경우 발생할 수 있는 단점은 아래와 같다.

-   개발 초기에 처음 사용할 경우 시행 착오를 할 확률이 높다.
-   아직까지 윈도우 환경을 완벽하게 지원히자 않고 있다. 윈도우 사용자라면 사용에 어려움이 있다.
-   디버그가 어렵다. 컨태이너 내부에서만 발생하는 디버그를 찾아 내기가 쉽지 않다.


#### Dockerfile 인스트럭션 {#dockerfile-인스트럭션}

Docker는 Docekrfile 파일에 전용 도메인 언어로 이미지를 구성한다. 해당 파일에 사용되는 인스트럭션(명령)을 알아 보도록 하겠습니다.

-   FROM
    FROM 인스트럭션은 도커 이미지의 바탕이 될 베이스 이미지를 지정한다. Dockerfile 로 이미지를 빌드할때 가장 먼저 FROM 에 지정된 이미지를 다운 받는다. 받아온 이미지는 도커 허브 (Docker Hub) 라는 레지스트리에 공개된 것이다.
    아래 파일에서는 golang:1.13 의 go 런타임 이미지를 다운 받도록 했다.

-   ENV
    ENV 인스트럭션은 환경 변수를 지정할 수 있다.

-   WORKDIR
    실행 위치를 해당 폴더로 이동한다.

-   RUN
    RUN 인스트럭션은 도커 이미지를 실행할 때 컨테이너 영역 아넹서 실행할 명령을 지정한다. 아래 이미지 에서는 mkdir 과 Git Clone 등을 수행 했다.

-   COPY
    COPY 인스트럭션은 도커가 동작중인 호스트 머신의 파일이나 디렉토리를 도커 컨테이너 안으로 복사 하는 인스트럭션이다. 아래 이미지 에서는 코드 복사를  Git 에서 Clone 했지만신 디버그 시에 Host 머신의 코드를 복사해서 사용하도록 하는 것이 좋다.

-   CMD
    CMD 인스트럭션은 도커 컨테이너를 실행할 때 컨테이너 안에서 실행할 프로세스를 지정한다. CMD로 Shell 명령을 할 경우 아래와 같다.

    ```nil
        $ go run /echo/main.go

        CMD ["go","run","/echo.main.go"]
    ```


## Slide4 {#slide4}


### Docker Image 만들기 {#docker-image-만들기}

```nil

 FROM golang:1.13 as goimage
 ENV SRC=/go/src/
 ENV GO111MODULE=on
 RUN mkdir -p /go/src/
 WORKDIR /go/src/github.com/hillfolk/go-rest-api-template
 RUN git clone -b develop --single-branch https://github.com/hillfolk/go-rest-api-template.git /go/src/github.com/hillfolk/go-rest-api-template && CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
 go build -ldflags "-X main.Version=`git describe --tags` -X main.Commit=`git rev-parse --short HEAD`" -o bin/go-rest-api-template

 FROM alpine:3.9 as baseimagealp
 ENV WORK_DIR=/docker/bin

 WORKDIR $WORK_DIR

 COPY --from=goimage /go/src/github.com/hillfolk/go-rest-api-template/bin/ ./

 ENTRYPOINT /docker/bin/go-rest-api-template
 EXPOSE 1323

```

```nil
docker build -t   hillfolk/go-rest-api-template:latest . && docker run -p 1323:1323 hillfolk/go-rest-api-template
```


## Slide6 {#slide6}


### ECR(Elastic Container Registry) {#ecr--elastic-container-registry}

Elastic Container Registry(ECR)는 개발자가 Docker 컨테이너 이미지를 손쉽게 저장, 관리 및 배포할 수 있게 해주는 완전관리형 Docker 컨테이너 레지스트리이다.
쉽게 생각하면 Private Docker Hub 라고 생각하면 된다. ECS 에 사용하기 위해서 자신이 빌드한 Docker Image 를 ECR 에 업로드 하여 ECS 를 이용해서 배포 한다.

```nil
$(aws ecr get-login --no-include-email --region ap-northeast-2) // AWS SDK login

docker build -t hillfolk/go-rest-api-template .

docker tag hillfolk/go-rest-api-template:latest  [레파지토리 주소를 이미지명으로 지정]:[tag] // 등록시에는 먼저 네임스페이스를 해당 레파지토리로의 경로로 변경 한다.

docker push 068261909741.dkr.ecr.ap-northeast-2.amazonaws.com/hillfolk/go-rest-api-template:latest // 푸쉬

```


## Slide7 {#slide7}


### ECS (Elastic Container Service) {#ecs--elastic-container-service}

ECS는 완전 관리형 컨테이너 오케스트레이션 서비스 입니다. [컨테이너 오케스트레이션](https://team-platform.tistory.com/48) 다양한 오케스트레이션 서비스가 있으며 ECS 의 경우는 AWS 에서 자체 개발한 서비스 이다.
ECS의 장점은 다양한 AWS 기능과 통합이 쉽고 다른 서비스에 비해서 쉽게 적용이 가능합니다. 유료 서비스다 보니 다양한 기능과 옵션이 존재 한다.


#### 시작 유형 {#시작-유형}

-   FARGATE : 컨테이너에 적합한 서버리스 컴퓨팅 엔진 으로 ECS 및 EKS 에 사용할 수 있으며 서버를 관리 할 필요 없이 어플리케이션 별로 리소스를 지정하여 관리 비용을 지불하는 방식으로 앱에 집중하여 인프라가 아닌 어플리케이션을 배포 관리 할수 있다.

-   EC2 : EC2 컨테이너 인스턴스를 연결하여 ECS 서비스 구성하기 때문에 위의 FARGATE 보다 비용을 저렴하나 설정이 복잡하고 확장성이 적음


#### 작업 정의 {#작업-정의}

Docker 리포지토리 및 이미지, 메모리 및 CPU 요구 사항, 공유 데이터 볼륨, 컨테이너가 서로 연결되는 방식 을 지정한 JSON 템플릿의 정의 문서 입니다.
서비스에 등록할 수 있는 단일 작업 정의 파일에서 원하는 만큼의 작업을 실행 할수 있다. 또한  작업 정의 파일을 사용하면 애플리케이션 사양의 버전을 관리할 수도 있습니다.


#### ECS {#ecs}

클러스터 생상 -> 작업 정의 생성 -> 서비스 생성 -> 작업정의 지정


## Slide8 {#slide8}


### Github Action {#github-action}


#### 작업 정의 파일 지정 {#작업-정의-파일-지정}

```nil
{
  "family": "go-rest-api-template",
  "taskRoleArn": "작업 권한 ARN",
  "executionRoleArn": "작업 권한 ARN",
  "networkMode": "awsvpc",
  "containerDefinitions": [
    {
      "dnsSearchDomains": null,
      "logConfiguration": {
        "logDriver": "awslogs",
        "secretOptions": null,
        "options": {
          "awslogs-group": "/ecs/로그 그룹 ",
          "awslogs-region": "리전 ",
          "awslogs-stream-prefix": "ecs"
        }
      },
      "entryPoint": null,
      "portMappings": [
        {
          "hostPort": 1323,
          "protocol": "tcp",
          "containerPort": 1323
        }
      ],
      "command": null,
      "linuxParameters": null,
      "cpu": 1024,
      "environment": [
        {
          "name": "환경변수",
          "value": "환경변수 값"
        }
      ],
      "resourceRequirements": null,
      "ulimits": null,
      "dnsServers": null,
      "mountPoints": [],
      "workingDirectory": null,
      "secrets": null,
      "dockerSecurityOptions": null,
      "memory": null,
      "memoryReservation": 300,
      "volumesFrom": [],
      "stopTimeout": null,
      "image": "사용할 이미지 명",
      "startTimeout": null,
      "firelensConfiguration": null,
      "dependsOn": null,
      "disableNetworking": null,
      "interactive": null,
      "healthCheck": null,
      "essential": true,
      "links": null,
      "hostname": null,
      "extraHosts": null,
      "pseudoTerminal": null,
      "user": null,
      "readonlyRootFilesystem": null,
      "dockerLabels": null,
      "systemControls": null,
      "privileged": null,
      "name": "컨테이너 이름 "
    }
  ],
  "volumes": [
  ],
  "placementConstraints": [
  ],
  "requiresCompatibilities": [
    "FARGATE"
  ],
  "cpu": "1024",
  "memory": "4096",
  "tags": [
    {
      "key": "serviceName",
      "value": "go-rest-api-template"
    }
  ],
  "pidMode": null,
  "ipcMode": null,
  "proxyConfiguration": null,
  "inferenceAccelerators": null
}

```


#### workflows 작성 {#workflows-작성}

```nil
# This is a basic workflow to help you get started with Actions
on:
  push:
    branches:
      - master

name: Deploy to Amazon ECS

jobs:
  deploy:
    name: Deploy
    runs-on: ubuntu-latest

    steps:
    - name: Checkout
      uses: actions/checkout@v2

    - name: Configure AWS credentials
      uses: aws-actions/configure-aws-credentials@v1
      with:
        aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
        aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        aws-region: ap-northeast-2

    - name: Login to Amazon ECR
      id: login-ecr
      uses: aws-actions/amazon-ecr-login@v1

    - name: Build, tag, and push image to Amazon ECR
      id: build-image
      env:
        ECR_REGISTRY: ${{ steps.login-ecr.outputs.registry }}
        ECR_REPOSITORY: hillfolk/go-rest-api-template
        IMAGE_TAG: release
      run: |
        # Build a docker container and
        # push it to ECR so that it can
        # be deployed to ECS.
        docker build -t $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG . -f Dockerfile
        docker push $ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG
        echo "::set-output name=image::$ECR_REGISTRY/$ECR_REPOSITORY:$IMAGE_TAG"
    - name: Fill in the new image ID in the Amazon ECS task definition
      id: task-def
      uses: aws-actions/amazon-ecs-render-task-definition@v1
      with:
        task-definition: task-definition.json
        container-name: go-rest-api-template-container
        image: ${{ steps.build-image.outputs.image }}

    - name: Deploy Amazon ECS task definition
      uses: aws-actions/amazon-ecs-deploy-task-definition@v1
      with:
        task-definition: ${{ steps.task-def.outputs.task-definition }}
        service: go-rest-api-service
        cluster: go-rest-api-template
        wait-for-service-stability: true

#    - name: Slack Notification
#      uses: rtCamp/action-slack-notify@v2.0.2
#      env:
#        SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
#        SLACK_USERNAME: action bot
#        SLACK_TITLE: 서버 배포
#        SLACK_MESSAGE: 서버 배포되었습니다.

```
