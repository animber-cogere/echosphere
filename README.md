# echosphere

The true echosphere repo

Fetch with
`env GIT_TERMINAL_PROMPT=1 go get -u gitlab.com/animber-coder/echosphere`


### Usage
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
	b.SendMessage("Hello world", b.chatId)
}


func main() {
	echosphere.RunDispatcher("568059758:AAFRN3Xg3dOkfe2n0gNlOWjlkM6dihommPQ", NewBot)
}
```

