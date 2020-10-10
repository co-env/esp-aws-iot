# Sobre o AWS IoT

AWS IoT é uma "ponte" entre o dispositivo que vc quer conectar e os outros serviços da AWS. Suporta conexão por MQTT e HTTPS.

## Arquitetura

A stack do AWS IoT está dividida em três níveis:

- *Device Software*: firmware que vai estar rodando no dispositivo, seja FreeRTOS ou algum SDK da amazon. No nosso caso, vamos usar uma implementação de um dos SDKs adaptada pro ESP32 (esp-aws-iot)

- *Control Services*: o que controla os diversos dispositivos que vão se conectar no AWS. No nosso caso, o próprio AWS IoT Core

- *Data Services*: serviços que podem ser usados para análise de dados coletados.

## Core Services

- Messaging services: gateway que permite a comunicação com o AIoT, message broker pra MQTT ou HTTPS e Rules Engine que permite a aplicação de regras nas mensagens recebidas.

----

# Conectando dispositivos ao AWS IoT

Os dispositivos se conectam ao AWS IoT através do **AWS IoT Core**, utilizando endpoints específicos da sua conta e um message broker. 

Devices e clients publicam mensagem no broker e também se inscrevem pra receber mensagens publicadas pelo broker. Essas mensagens são indentificadas por um **topic**. Quando o broker recebe uma mensagem num **topic**, ele republica a msg pros devices que estão inscritos nesse topic. O broker também repassa essa msg pra ferramenta de *Rules* do aws, que consegue interpretar o conteúdo dela.

## Usando a CLI 

(Instalar a CLI do AWS e conectar na conta)

- Descobrir os endpoints da conta: `aws iot describe-endpoint --endpoint-type tipo_do_endpoint` [tabela_endpoints](https://docs.aws.amazon.com/iot/latest/developerguide/iot-connect-devices.html)

- Pegar o endpoint `aws iot describe-endpoint --endpoint-type iot:Data-ATS` e colocar na variável `HostAddress` do codigo exemplo (tem um define pra ela, mas eu n entendi direito como muda, n consegui fazer o `make menuconfig` funcionar no Windows)

    - **IMPORTANTE**: esse comando aí em cima devolve um endpoint diferente do que tá no console pelo site. Quando eu usava o do site, dava erro. Melhor pegar e usar esse que é retornado pelo comando

- No site do AWS IoT Core, ir em "Gerenciar" e criar uma nova "Coisa". Só dar um nome e avançar, ele vai pedir pra criar um certificado pra ela. Criar o certificado e baixar os arquivos .pem.crt e .pem.key (chave publica apenas). Colocar essas chaves na pasta ./main/certs com o nome "certificate.pem.crt" e "public.pem.key"

- Ao criar uma Coisa e um Certificado, precisa adicionar uma Política pra permitir conexões dos clients aos recursos. Eu fiz uma de teste que permite tudo de qqr client, tem que ver como configurar pra ele aceitar conexões de um jeito "mais certo". A Política é atrelada à Coisa/Certificado. 

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "iot:*",
      "Resource": "*"
    }
  ]
}
```


