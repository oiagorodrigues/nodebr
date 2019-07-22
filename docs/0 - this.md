# this keyword

O valor de *this* é determinado pela maneira no qual uma função é chamada (runtime binding).
Não pode ser definido durante execução e pode  ter um resultado diferente toda vez que a função for executada.

> É a propriedade da execução de um contexto (global, função ou eval) que, no modo *non-strict* é sempre uma referência a um objeto e no modo *strict* pode ser  qualquer valor.

obs.: no contexto **global**, *this* sempre vai se referir ao objeto global, independente de estar no modo strict ou não.
