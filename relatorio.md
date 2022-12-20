> Sarom Torres Gaier

# Início do Jogo

Ao entrar no jogo, clicando na tela inicial, é realizada uma requisição HTTP na qual retorna o status `101` indicando a alteração do protocolo HTTP pelo WebSocket.

![image](https://user-images.githubusercontent.com/34520860/208555687-7993ee04-2a91-4639-871b-9502b9b0bee4.png)

# Sinalização
    
## Requisições: 

Ao clicar para entrar na sala é criado um identificador único `sid` para o jogador:
```
0{
   "sid":"ij7ck7YYt3HCu78NAAAA",
   "upgrades":[
      "websocket"
   ],
   "pingInterval":25000,
   "pingTimeout":20000,
   "maxPayload":1000000
}
```
Ao clicar para entrar na sala é enviada a sinalização `40` e obtém como resposta o `sid` do usuário:

```
40{"sid":"rv-FnGzL9LGkVrweAAAH"}
```


## Respostas: 

nomes/números e formatos das respostas.
    
## Mensagens: 
formato completo de cada mensagem, seja essa requisição ou resposta.
    
## Transações: 
sequência válida de mensagens a formar um transação entre duas entidades em rede, com destaque a informações de controle (identificador, número de sequência etc.).
    
## Diálogos: sequência válida de transações a formar um diálogo entre duas entidades em rede, com destaque a informações de controle do diálogo (identificador etc.).

Para cada componente acima, apresentar um exemplo prático do cenário analisado.
