# SMU29009 - Relatório da análise da aplicação WebRTC

Aluna: Sarom Torres Gaier
----------------------------------------

# Início do Jogo

Ao entrar no jogo, clicando na tela inicial, é realizada uma requisição HTTP na qual retorna o status `101` indicando a alteração do protocolo HTTP pelo WebSocket.

![image](https://user-images.githubusercontent.com/34520860/208555687-7993ee04-2a91-4639-871b-9502b9b0bee4.png)

# Sinalização
    
Ao clicar na tela principal da aplicação para entrar na sala, uma mensagem requisição contendo o código da sala escolhida e a mensagem contendo uma solicitação de entrada na sala é enviadas ao servidor, na qual responde com o o identificador único do(s) jogador(es).

## Mensagens 

### Requisição de entrada na sala

A mensagem de requisição de entrada na sala consiste na tag `entrar-na-sala` e no código da sala escolhida pelo jogador

Exemplo de requisição para entrada na sala 0: 

![image](https://user-images.githubusercontent.com/34520860/208665447-3f6bd9da-283d-4d35-8bf1-14e74d76a1b0.png)

Exemplo de requisição para entrada na sala 1: 

![image](https://user-images.githubusercontent.com/34520860/208665345-b851afe7-bbc4-46e0-b376-dbd80cc3a2f3.png)

### Resposta de entrada na sala

A reposta contém a tag `jogadores` e uma lista com seus identificadores únicos, sendo que cada jogador é mapeado com os campos `primeiro` e `segundo`

Exemplo de resposta para entrada do primeiro jogador 

![image](https://user-images.githubusercontent.com/34520860/208667029-c26ad52a-bdc6-4e75-8099-5cabd81a4e19.png)

Exemplo de resposta para entrada do segundo jogador 

![image](https://user-images.githubusercontent.com/34520860/208667326-43364e48-b78f-490b-af98-b943f20ca3a8.png)

### Requisição Candidate

Ao iniciar a sessão de mídia (explicada na seção de Negociação de mídia) é enviado o atributo `a=ice-options:trickle` na qual permite o  o processo de continuar a enviar candidatos depois que a oferta ou resposta inicial já foi enviada para o outro par.

Sendo assim, são enviadas ao servidor mensagens de requisção do tipo `candidate` informando um `usernameFragment` e informações do protoloco UDP, bem como um endereço IPv4.

> As mensagens candidate são mensagens de dialogo, por compõe uma sequência de trasações.

![image](https://user-images.githubusercontent.com/34520860/208688760-36506382-1498-46e9-ab29-5a17888209de.png)

### Resposta Candidate

Que por sua vez recebe a seguinte resposta:

![image](https://user-images.githubusercontent.com/34520860/208693339-45e8c986-067d-4417-a55a-34d7e0d6af90.png)


### Mensagem estadoDoJogador

É enviado ao servidor a mensagem com o `estadoDoJogador` indicando a posição e o quadro do jogador do jogador.

![image](https://user-images.githubusercontent.com/34520860/208694441-7b57215a-84c2-4add-81a3-a15f80f019aa.png)


# Negociação de mídia

> A negociação de mídia é realizada por meio de mensagens _offer_ e _answer_. Esse processo de troca de mensagens válidas entre os pontos é conhecida como transação. Alé disso também são consideradas mensagens de diálogo.

## Requisição 

A negociação de mídia se dá pelo protocolo SDP sendo que a oferta é feita por meio de uma requisição ao servidor enviando o campo `offer` e na sequência as informações referentes a descrição da oferta de mída.

![image](https://user-images.githubusercontent.com/34520860/208670160-d781a701-5eec-4a71-99d4-69d7ed10c5eb.png)

Campo SDP detalhado:

```
v=0
o=mozilla...THIS_IS_SDPARTA-99.0 523876576585789748 0 IN IP4 0.0.0.0
s=-
t=0 0
a=sendrecv
a=fingerprint:sha-256 6C:C0:1F:E7:F4:01:F7:BE:23:A9:ED:D4:97:35:23:F9:A6:F9:A5:41:72:A6:5D:C5:BF:1B:35:0C:E5:8C:3B:FF
a=group:BUNDLE 0
a=ice-options:trickle
a=msid-semantic:WMS *
m=audio 9 UDP/TLS/RTP/SAVPF 109 9 0 8 101
c=IN IP4 0.0.0.0
a=sendrecv
a=extmap:1 urn:ietf:params:rtp-hdrext:ssrc-audio-level
a=extmap:2/recvonly urn:ietf:params:rtp-hdrext:csrc-audio-level
a=extmap:3 urn:ietf:params:rtp-hdrext:sdes:mid
a=fmtp:109 maxplaybackrate=48000;stereo=1;useinbandfec=1
a=fmtp:101 0-15
a=ice-pwd:7b0c5c8f47b45fa12f841a15046a3e6b
a=ice-ufrag:9be81d48
a=mid:0
a=msid:{6b655972-c81e-4cf3-ad7d-7906a52332ba} {99f99761-6097-4b7a-92eb-ca9992212699}
a=rtcp-mux
a=rtpmap:109 opus/48000/2
a=rtpmap:9 G722/8000/1
a=rtpmap:0 PCMU/8000
a=rtpmap:8 PCMA/8000
a=rtpmap:101 telephone-event/8000
a=setup:actpass
a=ssrc:1968612793 cname:{bb877c81-52fd-46c9-85dd-0a2b3ef7a425}
```

Algumas observações sobre o campo SDP da requisição de oferta:

- consiste em uma requisição de mídia realizada pelo agente `523876576585789748` descrita no campo `o`.
- o campo `a` define as extensões que serão usadas nos cabeçalhos RTP para que o receptor possa decodificá-lo corretamente e extrair os metadados. 
- por exemplo, a URI indicada no campo `a=extmap:1 urn:ietf:params:rtp-hdrext:ssrc-audio-level` está sinalizando que serão incluídas informações sobre o nível de áudio no cabeçalho RTP.
- o campo `m` contém a descrição de mídia e de transporte e consiste em `m=<media> <port> <transport> <fmt list>`
- por exemplo, o campo `m` requisita uma mídia do tipo audio na porta 9 utilizando os protocolos UDP/TLS/RTP/SAVPF
- o campo `c` define que o protocolo de transporte deve operar em cima de IP4
- o campo `a=sendrecv` especifica que as ferramentas devem ser iniciadas em _send e receive mode_, isto é, operar em modo bidirecional. Isso é necessário para conferências que são interativas.
- os seguintes campos contém os _codecs_ que são suportados:
  - `a=rtcp-mux` 
  - `a=rtpmap:109 opus/48000/2`
  - `a=rtpmap:9 G722/8000/1`
  - `a=rtpmap:0 PCMU/8000`
  - `a=rtpmap:8 PCMA/8000`
  - `a=rtpmap:101 telephone-event/8000`

## Resposta 

A resposta à requisição de oferta se dá por meio do campo `answer`:

![image](https://user-images.githubusercontent.com/34520860/208677991-a6590c93-32fb-464b-93eb-85c81d0251ef.png)


A resposta SDP é enviada pelo agente `3681336890811055910` e confirma os protocolos de transpor, os tipos de mídia, bem como os codecs que serão utlizados
```
v=0
o=mozilla...THIS_IS_SDPARTA-99.0 3681336890811055910 0 IN IP4 0.0.0.0
s=-
t=0 0
a=sendrecv
a=fingerprint:sha-256 40:F2:48:87:EA:22:2D:25:AC:08:C4:7C:AA:1A:68:7C:01:B0:26:D8:E1:31:39:47:13:0B:B9:E8:A8:1F:06:88
a=group:BUNDLE 0
a=ice-options:trickle
a=msid-semantic:WMS *
m=audio 9 UDP/TLS/RTP/SAVPF 109 9 0 8 101
c=IN IP4 0.0.0.0
a=sendrecv
a=extmap:1 urn:ietf:params:rtp-hdrext:ssrc-audio-level
a=extmap:3 urn:ietf:params:rtp-hdrext:sdes:mid
a=fmtp:109 maxplaybackrate=48000;stereo=1;useinbandfe
c=1
a=fmtp:101 0-15
a=ice-pwd:1fa6da0ff94f4be50b9b539628260837
a=ice-ufrag:2800cd3f
a=mid:0
a=msid:{4fcbde31-d958-4671-99a3-988f1211d1f3} {29dceee2-9e61-403d-85ea-184f2fa77d4a}
a=rtcp-mux
a=rtpmap:109 opus/48000/2
a=rtpmap:9 G722/8000/1
a=rtpmap:0 PCMU/8000
a=rtpmap:8 PCMA/8000
a=rtpmap:101 telephone-event/8000
a=setup:active
a=ssrc:1548576031 cname:{abe09305-8ed2-49f4-b949-02800ac4a786}
```

Referência: [rfc2327](https://www.ietf.org/rfc/rfc2327.txt)

## Codecs

Como já mencionado, os codecs acordados na negociação de mídia SDP foram:

```
[...]
m=audio 9 UDP/TLS/RTP/SAVPF 109 9 0 8 101
[...]
a=rtpmap:109 opus/48000/2
a=rtpmap:9 G722/8000/1
a=rtpmap:0 PCMU/8000
a=rtpmap:8 PCMA/8000
a=rtpmap:101 telephone-event/8000
```

Em que cada um dos itens referênciados na descrição de mídia consiste em um item do mapeamento RTP.

O formato de cada um desses atributos é:

```
a=rtpmap:<payload type> <encoding name>/<clock rate> [/<encoding parameters>]
```
Exemplo, em `a=rtpmap:9 G722/8000/1` o SDP está sinalizando que o cliente suporta G722, na qual provê audio em alta qualidade HD, a uma taxa de 8kHz.


# Segurança

## Username and Password

O ICE username é representado no atributo SDP "ice-ufrag" enquanto a senha é representada no atributo "ice-pwd":

Mensagem SDP de requisição do agente `523876576585789748`:
```
[...]
a=ice-pwd:7b0c5c8f47b45fa12f841a15046a3e6b
a=ice-ufrag:9be81d48
[...]
```

Mensagem SDP de resposta do agente `3681336890811055910`:
```
[...]
a=ice-pwd:1fa6da0ff94f4be50b9b539628260837
a=ice-ufrag:2800cd3f
[...]
```

Alé disso todas as mensagens que realizam transporte de ídia e sinalização utilizam o protocolo SRTP, aa qual é uma extensão do protocolo RTP que conta com medidas de segurança otimizadas, tais como criptografia, autenticação e integridade de mensagens, isto é utiliza HTTPS.
No Stun é possível fazer a criptografia, entretanto a aplicação não conta com essa funcionalidade.  

Referência: [rfc8839](https://datatracker.ietf.org/doc/rfc8839/)
