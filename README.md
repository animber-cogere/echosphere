# echosphere [![Language](https://img.shields.io/badge/Language-Go-blue.svg)](https://golang.org/) [![PkgGoDev](https://pkg.go.dev/badge/github.com/animber-coder/echosphere/v2)](https://pkg.go.dev/github.com/animber-coder/echosphere/v2) [![Go Report Card](https://goreportcard.com/badge/github.com/animber-coder/echosphere)](https://goreportcard.com/report/github.com/animber-coder/echosphere) [![License](http://img.shields.io/badge/license-LGPL3.0-orange.svg?style=flat)](https://github.com/animber-coder/echosphere/blob/master/LICENSE)

Library for telegram bots written in pure go

Fetch with

```bash
go get github.com/animber-coder/echosphere/v2
```

## Usage

### Long Polling

A very simple implementation:

```go
package main

import (
    "log"

    "github.com/animber-coder/echosphere/v2"
)

type bot struct {
    chatId int64
    echosphere.API
}

const TOKEN = "YOUR TELEGRAM TOKEN"

func newBot(chatId int64) echosphere.Bot {
    return &bot{
        chatId,
        echosphere.NewAPI(TOKEN),
    }
}

func (b *bot) Update(update *echosphere.Update) {
    if update.Message.Text == "/start" {
        b.SendMessage("Hello world", b.chatId)
    }
}

func main() {
    dsp := echosphere.NewDispatcher(TOKEN, newBot)
    log.Println(dsp.Poll())
}
```


Also proof of concept with self destruction for low ram usage

```go
package main

import (
    "log"
    "time"

    "github.com/animber-coder/echosphere/v2"
)

type bot struct {
    chatId int64
    echosphere.API
}

const TOKEN = "YOUR TELEGRAM TOKEN"

var dsp echosphere.Dispatcher

func newBot(chatId int64) echosphere.Bot {
    var bot = &bot{
        chatId,
        echosphere.NewAPI(TOKEN),
    }
    go bot.selfDestruct(time.After(time.Hour))
    return bot
}

func (b *bot) selfDestruct(timech <- chan time.Time) {
    select {
    case <-timech:
        b.SendMessage("goodbye", b.chatId)
        dsp.DelSession(b.chatId)
    }
}

func (b *bot) Update(update *echosphere.Update) {
    if update.Message.Text == "/start" {
        b.SendMessage("Hello world", b.chatId)
    }
}

func main() {
    dsp := echosphere.NewDispatcher(TOKEN, newBot)
    log.Println(dsp.Poll())
}
```

### Webhook

```go
package main

import "github.com/animber-coder/echosphere/v2"

type bot struct {
	chatId int64
	echosphere.API
}

const TOKEN = "YOUR TELEGRAM TOKEN"

func newBot(chatId int64) echosphere.Bot {
	return &bot{
		chatId,
		echosphere.NewAPI(TOKEN),
	}
}

func (b *bot) Update(update *echosphere.Update) {
	if update.Message.Text == "/start" {
		b.SendMessage("Hello world", b.chatId)
	}
}

func main() {
	dsp := echosphere.NewDispatcher(TOKEN, newBot)
	dsp.ListenWebhook("https://newworld:443/bot", 23456)
}
```
