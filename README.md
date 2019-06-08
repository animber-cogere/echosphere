# echosphere

The true echosphere repo

Fetch with
`env GIT_TERMINAL_PROMPT=1 go get -u gitlab.com/animber-coder/echosphere`


### Usage

A very simple implementation:

```go
package main

import "gitlab.com/animber-coder/echosphere"

type bot struct {
	chatId int64
	*echosphere.Engine
}


func NewBot(token string, chatId int64) echosphere.Bot {
	return &bot{
		chatId,
		echosphere.NewEngine(token),
	}
}


func (b *bot) Update(update *echosphere.Update) {
    if update.Message.Text == "/start" {
        b.SendMessage("Hello world", b.chatId)
    }
}


func main() {
	echosphere.RunDispatcher("TELEGRAM TOKEN", NewBot)
}
```


Also proof of concept with self destruction for low ram usage

```go
package main

import "gitlab.com/animber-coder/echosphere"

type bot struct {
    chatId int64
    *echosphere.Engine
}


func NewBot(token string, chatId int64) echosphere.Bot {
    var bot = &bot{
        chatId,
        echosphere.NewEngine(token),
    }
    echosphere.AddTimer(bot.chatId, "selfDestruct", bot.selfDestruct)
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
    echosphere.RunDispatcher("TELEGRAM TOKEN", NewBot)
}
```


