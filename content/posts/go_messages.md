+++
title = "Go Messaging System"
author = ["Hillfolk"]
date = 2019-05-02
lastmod = 2019-07-27T19:16:14+09:00
tags = ["go", "nats", "messaging-system"]
categories = ["posts"]
weight = 100
draft = true
+++

## Nats {#nats}

Message Echo System


## Intro {#intro}

Nats Server는 IoT 메시징 및 마이크로 서비스 아키텍처를 위한 간단하고 고성능의 오픈소스 메시징 시스템 입니다.
go로 작성되어 있으며 Python, Java, Ruby, Node.js 등으로 작성된 클라이언트가 있습니다.


## NATS Design Goals {#nats-design-goals}

The core principles underlying NATS are performance, scalability, and ease-of-use. Based on these principles, NATS is designed around the following core features:

-   Highly performant (fast)
-   Always on and available (dial tone)
-   Extremely lightweight (small footprint)
-   Support for multiple qualities of service (including guaranteed “at-least-once” delivery with NATS Streaming)
-   Support for various messaging models and use cases (flexible)


## Pub Code {#pub-code}

```nil
var urls = flag.String("s", nats.DefaultURL, "The nats server URLs (separated by comma)")

log.SetFlags(0)
flag.Usage = usage
flag.Parse()

args := flag.Args()
if len(args) < 2 {
	usage()
}

nc, err := nats.Connect(*urls)
if err != nil {
	log.Fatal(err)
}
defer nc.Close()

subj, msg := args[0], []byte(args[1])

for  {
	nc.Publish(subj, msg)
	nc.Flush()
}

if err := nc.LastError(); err != nil {
	log.Fatal(err)
} else {
	log.Printf("Published [%s] : '%s'\n", subj, msg)
}

```


## Sub Code {#sub-code}

```nil
var urls = flag.String("s", nats.DefaultURL, "The nats server URLs (separated by comma)")
var showTime = flag.Bool("t", false, "Display timestamps")

log.SetFlags(0)
flag.Usage = usage
flag.Parse()

args := flag.Args()
if len(args) < 1 {
	usage()
}

nc, err := nats.Connect(*urls)
if err != nil {
	log.Fatalf("Can't connect: %v\n", err)
}

subj, i := args[0], 0

nc.Subscribe(subj, func(msg *nats.Msg) {
	i += 1
	printMsg(msg, i)
})
nc.Flush()

if err := nc.LastError(); err != nil {
	log.Fatal(err)
}

log.Printf("Listening on [%s]\n", subj)
if *showTime {
	log.SetFlags(log.LstdFlags)
}



```