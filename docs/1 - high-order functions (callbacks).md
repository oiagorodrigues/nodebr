# High-order Functions

São funções que recebem como argumento um outra função, conhecida como **continuação explícita** e retornam um valor pela chamada dessa função de continuação, via argumento. 

> Continuação Explícita, é uma função passada como parâmetro para outra função, no qual executar uma ordem e retorna um valor de volta para a função que a invocou. Também são conhecidos como **callbacks**.

> As high-order functions também são conhecidas como **funções de continuação explícita**.

Passar uma high-order function -- callback -- como argumento para outra função, pode resolver problemas de sincronização na hora que a aplicação estiver executando o seu código.

Por exemplo: quando queremos buscar dados de um usuário e exibí-los em um card, poderemos não receber os dados desejados se fizermos da seguinte maneira:

```
function retornarDadosUsuario() {
  setTimeout(() => {
    return { nome: 'John Doe' }
  })
}


console.log(retornarDadosUsuario())

// Saída: undefined
```

Nesse caso, a função `retornarDadosUsuario` é executada, porém como precisa de um tempo para ser finalizada, o Runtime executa os códigos seguintes para, no final, retornar o valor da função em questão. Por isso, `usuario` é undefined, pois no momento em que ele é executado, a função ainda não tinha retornado o valor desejado.

Agora, se passarmos um callback à função `retornarDadosUsuario`, poderemos resolver essa falta de sincronia.

```
fucntion retornarDadosUsuario(callback) {
  setTimeout(() => {
    return callback({ nome: 'John Doe' })
  })
}

retornarDadosUsuario(res => {
  console.log(res)
})

// Saída: { nome: 'John Doe' }

```

Com isso, conseguimos pegar os dados de retorno e exibí-los, porém, é válido ressaltar que a ordem de execução continua assíncrona.

Mesmo passando um callback para um função, o retorno desse callback será executado somente quando a requisição for realizada com sucesso.

Por exemplo, caso colocássemos um outro código após a chamada de `retornarDadosUsuario`, o resultado seria o seguinte:

```
[...]

retornarDadosUsuario(res => {
  console.log(res)
})

console.log('código 2')

/* Saída:
*   código 2
*   { nome: 'John Doe' }
*/
```

 Neste ponto, o Runtime ainda executa nossa função como antes; a diferença é que após a execução das funções ele aguarda o retorno e somente após isso ele executa a função de callback. E enquanto ele esperar o retorno, procura executar os outros código para otimizar tempo.

> Isso é uma prática do paradigma de programação funcional.

# Boas práticas em Callbacks

- Use funções nomeadas como argumentos para manipulação de callbacks

```
[...]

function getDados(usuario) {
  console.log(usuario)
}

retornarDadosUsuario(getDados)

// Saída: { nome: 'John Doe' }

```
- Sempre passar dois parâmetros para o callback:
1 - O primeiro vai tratar um possível erro de requisição
2 - O segundo vai tratar o resultado de sucesso da requisição

```
function retornarDadosUsuario(cb) {
  setTimeout(() => {
    if (success) {
      return cb(null, { nome: 'John Doe' })

    } else {

      return cb(new Error('Erro interno da aplicação') ,null)
    }   
  })
}
```

- O callback deve ser sempre o último parâmetro da função.

- Sempre retorne uma chamada da função de callback para evitar a continuação indevida da função original.

```
function retornarDadosUsuario(cb) {
  setTimeout(() => {
    if (success) {
      return cb(null, { nome: 'John Doe' })

    } else {

      return cb(new Error('Erro interno da aplicação') ,null)
    }   
  })
}
```

# Callback HELL

Com callbacks, conseguimos resolver o problema de sincronia. Entretanto, à medida que a aplicação cresce, a necessidade de recuperar mais informações também cresce.

Imaginemos que, além do nome do usuário, também gostaríamos de recuperar o seu endereço, telefone e veículo.

```
class Usuario {
  retornarDadosUsuario(callback) {
    setTimeout(() => {
      return callback(null, {nome: 'Erick Wendel', id: 123 });
    });
  };

  retornarEndereco(idUser, callback) {
    setTimeout(() => {
      return callback(null, {'rua': 'dos bobos', idUser: idUser});
    });
  };

  retornarTelefone(idUser, callback) {
    setTimeout(() => {
      return callback(null, {'numero': '123123123', idUser: idUser});
    });
  };

  retornarVeiculo(idUser, callback) {
    setTimeout(() => {
      return callback(null, {'carro': 'Fuscao', idUser: idUser});
    });
  };
}
```

A chamada nessas funções ficaria da seguinte forma:

```
let usuario = new Usuario();

usuario.retornarDadosUsuario(function (erro, dadosUsuario) {
  let id = dadosUsuario.id;

  usuario.retornarEndereco(id, function (erro, endereco) {
    usuario.retornarTelefone(id, function (erro, telefone) {
      usuario.retornarVeiculo(id, function (erro, veiculo) {
        let user = { nome: dadosUsuario.nome, endereco, telefone, veiculo };
        console.log(user);
      });
    });
  });
});

/**
 * Saida
 * { 
 *   nome: 'John Doe',
 *   endereco: { rua: 'dos bobos', idUser: 123 },
 *   telefone: { numero: '123123123', idUser: 123 },
 *   veiculo: { carro: 'Fuscao', idUser: 123 }
 * } 
 */
```

Isso é o que chamamos de **callback hell**. Intercalamos um callback dentro de outro, o que cresce o código horizontalmente e dificulta tanto a sua execução, quanto a sua leitura. 

Para resolvermos isso, utilizamos **Promises**.

```
// exemplo completo
'use strict'

function obterUsuario(cb) {
    setTimeout(() => {
        return cb(null, {
            id: 1,
            nome: 'John Doe',
            dataNascimento: new Date()
        });
    }, 1000);
}

function obterEndereco(idUsuario, cb) {
    setTimeout(() => {
        return cb(null, {
            endereco: 'rua dos bobos',
            numero: '0'
        });
    }, 2000);
}

function obterTelefone(idUsuario, cb) {
    setTimeout(() => {
        return cb(null, {
            ddd: '91',
            numero: '987655674'
        });
    }, 2000);
}

obterUsuario(function resolverUsuario(error, usuario) {
    if (error) {
        console.error('DEU RUIM em USUARIO', error);
        return;
    }

    obterTelefone(usuario.id, function resolverTelefone(error1, telefone) {
        if (error1) {
            console.error('DEU RUIM em TELEFONE', error1);
            return;
        }

        obterEndereco(usuario.id, function resolverEndereco(error2, endereco) {
            if (error2) {
                console.error('DEU RUIM em ENDERECO', error2);
                return;
            }

            console.table({
                nome: usuario.nome,
                endereço: `${endereco.endereco}, ${endereco.numero}`,
                telefone: `(${telefone.ddd}) ${telefone.numero}`
            })
        })
    })
});

```