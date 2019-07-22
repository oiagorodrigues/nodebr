# Promises

> "...Uma Promise (promessa) é uma abstração para programação assíncrona, que recebe algum input (dados) e retorna uma promessa do output (resultado) final. Diferente de um sistema baseado em callbacks, no qual recebe um input e um callback (função), que é executado retornando algum output." [Sebastian Ferrari](https://imasters.com.br/perfil/sebastianferrari/)

Alguns dos benefícios de trabalhar com **Promises** é escrever o código como se fosse síncrono, diferente de callbacks que possuem a chamada dentro de funções.

A Promise é um objeto cujo qual nos dá o controle sobre o estado da solicitação. Esse objeto pode estar em três estados:

- Pendente (pending): Estado inicial, no qual a Promise ainda não foi resolvida;
- Realizado (fulfilled): Promise resolvida com sucesso e retorna um valor;
- Rejeitado (rejected): Promise resolvida com falha e retorna o motivo do erro.

> "O método *then* é executado quando a promessa se encontra no estado *fulfilled*, passando duas funções como argumentos e retornando uma promessa. Possibilita a criação de uma cadeia de promessas (promise chaining), na qual a próxima promessa sempre poderá utilziar os resultados das anteriores." [Sebastian Ferrari](https://imasters.com.br/perfil/sebastianferrari/)


obs.: ver módulo [*blue-bird*](https://www.npmjs.com/package/bluebird)

```
// exemplo 1
'use strict'

function obterUsuario() {
    return new Promise((resolve, reject) => {
        const usuario = { 
            id: 1, 
            nome: 'John Doe', 
            dataNascimento: '1992-07-16' 
        }

        setTimeout(() => {
            // return reject('DEU RUIM USUARIO!!!')
            return resolve(usuario);
        }, 1000);

    })
}

function obterEndereco(usuario) {
    return new Promise((resolve, reject) => {
        usuario.endereco = [{ 
            userId: usuario.id, 
            endereco: 'rua dos bobos', 
            numero: '0' 
        }]

        setTimeout(() => {
            // return reject('DEU RUIM ENDERECO!!!')
            return resolve(usuario);
        }, 1000);

    })
}

function obterTelefone(usuario) {
    return new Promise((resolve, reject) => {
        usuario.telefone = [{ 
            userId: usuario.id, 
            ddd: '91', 
            numero: '987655674' 
        }]

        setTimeout(() => {
            // return reject('DEU RUIM TELEFONE!!!')
            return resolve(usuario);
        }, 1000);
    })
}

const usuarioPromise = obterUsuario()

usuarioPromise
    .then(obterEndereco)
    .then(obterTelefone)
    .then(res => {
        console.log(`
            Usuario: ${res.nome}
            Nascimento: ${res.dataNascimento}
            Telefone: (${res.telefone[0].ddd}) ${res.telefone[0].numero}
            Endereco: ${res.endereco[0].endereco}, ${res.endereco[0].numero}
        `)
    })
    .catch(error => {
        console.log('DEU RUIM', error)
    })
```

Promises também diminui a dificuldade de trabalhar com chamadas concorrentes utilizando o método [*Promise.all()*](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Global_Objects/Promise/all).

```
'use strict'

const retornarUsuarioOracle = () => {
    return new Promise((resolve, reject) => {
        const usuarioOracle = { nome: 'Jhon Doe' }
        return resolve(usuarioOracle)
    })
}

const retornarUsuarioMysql = () => {
    return new Promise((resolve, reject) => {
        const usuarioMysql = { nome: 'Zina da Silva' }
        return resolve(usuarioMysql)
    })
}

const retornarUsuarioSqlServer = () => {
    return new Promise((resolve, reject) => {
        const usuarioSqlServer = { nome: 'Xuxa Souza' }
        return resolve(usuarioSqlServer)
    })
}

// Enviamos um array. A chamada de cada função deve ser executada.
Promise.all([
  retornarUsuarioOracle(),
  retornarUsuarioMysql(),
  retornarUsuarioSqlServer()
])
  .then(res => {

    const usuarioOracle = res[0]
    const usuarioMysql = res[1]
    const usuarioSqlServer = res[2]

    const mensagem = `
      Oracle: ${usuarioOracle.nome}
      MySQL: ${usuarioMysql.nome}
      SQLServer: ${usuarioSqlServer.nome}
    `
    console.log(mensagem)

  })
  .catch( error => {
    console.log('DEU ZICA!', error)
  })
```