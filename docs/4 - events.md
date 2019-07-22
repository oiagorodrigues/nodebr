# Event Emitter

> Tudo que é manipulado no nodejs, é realizado por meio de eventos.

Utilizamos o **Event Emitter** para ações contínuas, no qual implementamos um observador que executará determinada ação toda vez que o evento for disparado.

obs.: um erro comum é utilizar Promises para resolver eventos. É um erro, pois uma Promise é executada apenas uma vez.

Utiliza o **Design Patter Observable/PubSub**, no qual existe um **Publish** que envia os eventos para o Observable (todos os observables que estão "escutando" esse evento) e, esse último, executa ação com base nesse evento disparado.

```
// exemplo 1

const EventEmitter = require('events')

class Event extends EventEmitter {}

const evt = new Event()
const nomeEvento = 'usuario:click'

evt.on(nomeEvento, click => {
  console.log('um usuario clicou', click)
})

evt.emit(nomeEvento, 'na barra de rolagem')
evt.emit(nomeEvento, 'no ok')
```

```
// exemplo 2

const stdin = process.openStdin()
stdin.addListener('data', value => {
  console.log(`Voce digitou: ${value.toString().trim()}`)
})
```