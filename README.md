# echosphere [![Language](https://img.shields.io/badge/Language-Go-blue.svg)](https://golang.org/) [![PkgGoDev](https://pkg.go.dev/badge/github.com/animber-coder/echosphere)](https://pkg.go.dev/github.com/animber-coder/echosphere) [![Go Report Card](https://goreportcard.com/badge/github.com/animber-coder/echosphere)](https://goreportcard.com/report/github.com/animber-coder/echosphere) [![License](http://img.shields.io/badge/license-LGPL3.0-orange.svg?style=flat)](https://github.com/animber-coder/echosphere/blob/master/LICENSE)

Library for telegram bots written in pure go

Fetch with
```bash
go get -u github.com/animber-coder/echosphere
```

### Usage

A very simple implementation:

```go
package main

import "github.com/animber-coder/echosphere"

type bot struct {
    chatId int64
    echosphere.Api
}

func newBot(api echosphere.Api, chatId int64) echosphere.Bot {
    return &bot{
        chatId,
        api,
    }
}

func (b *bot) Update(update *echosphere.Update) {
    if update.Message.Text == "/start" {
        b.SendMessage("Hello world", b.chatId)
    }
}

func main() {
    dsp := echosphere.NewDispatcher("TELEGRAM TOKEN", newBot)
    dsp.Run()
}
```


Also proof of concept with self destruction for low ram usage

```go
package main

import "github.com/animber-coder/echosphere"

type bot struct {
    chatId int64
    echosphere.Api
}

var dsp echosphere.Dispatcher

func newBot(api echosphere.Api, chatId int64) echosphere.Bot {
    var bot = &bot{
        chatId,
        api,
    }
    echosphere.AddTimer(bot.chatId, "selfDestruct", bot.selfDestruct, 60)
    return bot
}

func (b *bot) selfDestruct() {
    b.SendMessage("goodbye", b.chatId)
    echosphere.DelTimer(b.chatId, "selfDestruct")
    dsp.DelSession(b.chatId)
}

func (b *bot) Update(update *echosphere.Update) {
    if update.Message.Text == "/start" {
        b.SendMessage("Hello world", b.chatId)
    }
}

func main() {
    dsp = echosphere.NewDispatcher("TELEGRAM TOKEN", newBot)
    dsp.Run()
}
```
