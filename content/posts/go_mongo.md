+++
title = "Go MongoDB 사용하기"
author = ["Hillfolk"]
date = 2019-05-01
lastmod = 2019-05-11T23:16:20+09:00
tags = ["go", "MongoDB", "go-mongo-driver"]
categories = ["posts"]
weight = 100
draft = true
+++

## MongoDB {#mongodb}

MongoDB는 C++로 작성된 오픈소스 문서지향(Document-Oriented) 적 Cross-platform 데이터베이스이며, 뛰어난 확장성과 성능을 가지고 있다.


## Go용 MongoDB 드라이버 {#go용-mongodb-드라이버}

Go 에서 Mongodb 용 라이브러리는 몇개 정도 지원 되는것으로 보인다. 그중에 mongo-go-driver 공식 Go 드라이버를 기준으로 작성 하였습니다.


## Install {#install}

```nil
go get go.mongodb.org/mongo-driver/mongo
```


## import & Create client {#import-and-create-client}

import시 주소에 주의해야한다. 일부 문서에는 이전 github 주소로 되어 있는 경우가 있다.

```nil

import "go.mongodb.org/mongo-driver/mongo"
import "go.mongodb.org/mongo-driver/mongo/options"

client, err := mongo.NewClient(options.Client().ApplyURI("mongodb://localhost:27017"))
```


## MongoDB Connect {#mongodb-connect}

MongoDB 연결시 일정시간이 지나면 연결 종료 신호를 보낼수 있도록 context.WithTimOut 함수를 이용하여 타임아웃 시간을 지정한후 연결한다.
연결 체크는 Ping 함수를 통해서할수 있다.

```nil

ctx, _ := context.WithTimeout(context.Background(), 10*time.Second)
err = client.Connect(ctx)

err = client.Ping(ctx,readpref.Primary()) // Primary DB에 대한 연결 체크

if err != nil {
		log.Fatal(err)
		}

```


## Database  & Collection 가저오기 {#database-and-collection-가저오기}

아래 예제는 test Database 에서 trainers collection 을 가지고 오는 예제이다.
만약 해당 데이터베이스나 collection 이 없는 경우 새롭게 생성된다.

```nil
collection := client.Database("test").Collection("trainers")

```


## Insert {#insert}

trainers collection에 단일 Document를 추가하는 예제로 결과 값으로는 objectID 를 반환한다 .

```nil

//입력할 데이터
ash := Trainer{"Ash", 10, "Pallet Town"}
misty := Trainer{"Misty", 10, "Cerulean City"}
brock := Trainer{"Brock", 15, "Pewter City"}

//1개의 Document 추가하기
insertResult, err := collection.InsertOne(context.TODO(), ash)
if err != nil {
log.Fatal(err)
}
```

참고로 context.TODO 는 해당 nil을 대신하여 넘기는 값으로 취소 되거나 중지 되는 조건이 없는 경우 넣는다.


## Insert Many {#insert-many}

동시에 어려개의 slice 데이터를 넣는 예제로 반환값은 여러개의 Object ID slice 값을 반환한다.

```nil
trainers := []interface{}{misty, brock}

insertManyResult, err := collection.InsertMany(context.TODO(), trainers)
if err != nil {
log.Fatal(err)
}

```


## Udate {#udate}

collection 에 데이터를 데이터를 업데이트 하는 경우 사용하는 방법

```nil

filter := bson.D{{"name", "Ash"}}

	update := bson.D{
		{"$inc", bson.D{
			{"age", 1},
		}},
	}

	updateResult, err := collection.UpdateOne(context.TODO(), filter, update)
	if err != nil {
		log.Fatal(err)
	}

```


## Delete {#delete}

status 가 D인 한개의 문서를 삭제하는 예제

```nil

result, err := coll.DeleteOne(
			context.Background(),
			bson.D{
				{"status", "D"},
			},
		)

```

설정된 조건 없이 collection 의 Document 를 삭제하는 예제

```nil
result, err := coll.DeleteMany(context.Background(), bson.D{})
```


## Find {#find}

collection에서 한개의 Document를 찾는 예제

```nil
var result Trainer

err = collection.FindOne(context.TODO(), filter).Decode(&result)
if err != nil {
	log.Fatal(err)
}

```

collection에서 여러개의 문서를 찾는 예제
SetLimit 함수는 결과값을 제한하는 용도로 사용된다.
curser 에서 Decode 로 값을 변환할 수 없는경우 err 을 반환한다.

```nil

findOptions := options.Find()
findOptions.SetLimit(2)

var results []*Trainer

cur, err := collection.Find(context.TODO(),bson.D{{}}   ,findOptions)

if err != nil {
	log.Fatal(err)
}
log.Println("completed find")

for cur.Next(context.TODO()){

	var elem Trainer
	err := cur.Decode(&elem)
	fmt.Printf(" document: %+v\n", elem)
	if err != nil {
		log.Fatal(err)
	}
	results = append(results,&elem)

}

if err := cur.Err(); err != nil {
	log.Fatal(err)

}
```


## 참고 {#참고}

-   <https://www.mongodb.com/blog/post/mongodb-go-driver-tutorial>
-   <https://www.mongodb.com/blog/post/go-migration-guide>