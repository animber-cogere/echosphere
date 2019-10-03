# echosphere

Library for telegram bots written in pure go

Fetch with
```bash
go get -u gitlab.com/animber-coder/echosphere
```

Doc at:
https://godoc.org/gitlab.com/animber-coder/echosphere

### Usage

A very simple implementation:

```go
package main

import "gitlab.com/animber-coder/echosphere"

type bot struct {
	chatId int64
	echosphere.Engine
}


func newBot(engine echosphere.Engine, chatId int64) echosphere.Bot {
	return &bot{
		chatId,
		engine,
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

import "gitlab.com/animber-coder/echosphere"

type bot struct {
    chatId int64
    echosphere.Engine
}


func newBot(engine echosphere.Engine, chatId int64) echosphere.Bot {
    var bot = &bot{
        chatId,
        engine,
    }
    echosphere.AddTimer(bot.chatId, "selfDestruct", bot.selfDestruct, 60)
    return bot
}


func (b *bot) selfDestruct() {
    b.SendMessage("goodbye", b.chatId)
    echosphere.DelTimer(b.chatId, "selfDestruct")
    echosphere.DelSession(b.chatId)
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
