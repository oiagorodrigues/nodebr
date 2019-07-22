# Módulo Async

- facilita a visualização do fluxo de funções
- não altera a performance da aplicação
- utilizar apenas quando precisar tratar a resposta da chamada

obs.: ver módulo [*async*](https://www.npmjs.com/package/async).

```
 // exemplo

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

function obterEndereco(usuarioID) {
  return new Promise((resolve, reject) => {
      const endereco = [{ 
          userId: usuarioID, 
          endereco: 'rua dos bobos', 
          numero: '0' 
      }]

      setTimeout(() => {
          // return reject('DEU RUIM ENDERECO!!!')
          return resolve(endereco);
      }, 1000);

  })
}

function obterTelefone(usuarioID) {
  return new Promise((resolve, reject) => {
      const telefone = [{ 
          userId: usuarioID, 
          ddd: '91', 
          numero: '987655674' 
      }]

      setTimeout(() => {
          // return reject('DEU RUIM TELEFONE!!!')
          return resolve(telefone);
      }, 1000);
  })
}

const main = async () => {
  try {
    const usuario = await obterUsuario()

    const promiseAll = await Promise.all([
      obterTelefone(usuario.id),
      obterEndereco(usuario.id)
    ])
    
    const telefone = promiseAll[0]
    const endereco = promiseAll[1]

    console.log(`
            Usuario: ${usuario.nome}
            Nascimento: ${usuario.dataNascimento}
            Telefone: (${telefone[0].ddd}) ${telefone[0].numero}
            Endereco: ${endereco[0].endereco}, ${endereco[0].numero}
        `)
        
  } catch (error) {
    console.error('DEU RUIM ', error)
  }
}

main()
```