---
layout: project
title: "Desafio Técnico: Microsserviços em Java"
date: 2023-11-01
ready: true
src: "https://github.com/djudju12/CompassUOL_SP_Challenge03_Jonathan_Santos"
details: "Autenticação"
---

Esse projeto foi desenvolvido para o programa de bolsas da CompassUOL. Consiste em uma API que simula um e-commerce e permite o gerenciamento de produtos, pedidos e autenticação do usuário. Desenvolvido com Spring Boot e documentado utilizando Swagger, o sistema foi desenvolvido em uma arquitetura de microsserviços (afinal esse o desafio). Vou comentar um pouco sobre o que eu achei mais interessante, mas se quiser saber mais sobre os outros componentes o código estará disponível.

### Autenticação

Durante a bolsa estudamos alguns modos diferentes de fazer a autenticação do usuário. Em desafios anteriores, tive a oportunidade de utilizar frameworks como [Spring Authorization Server](https://spring.io/projects/spring-authorization-server), por esse motivo escolhi fazer de uma forma mais simples dessa vez.

Utilizando apenas o Spring Security e uma biblioteca específica para JWT, foi possível implementar um sistema de autenticação utilizando tokens. A abordagem mais simples é também muito didática, digo isto pois vários diversos conceitos sobre tokens, autenticação e sobre o próprio funcionamento do Spring só puderam ser entendidos totalmente dessa forma. 

Segue um printizinho da resposta da API:

![resposta api auth](/assets/media/token.png "auth")

### Pedidos

O serviço de pedidos de longe é o mais interessante, afinal ele se comunicava com outros três serviços diferentes de forma síncrona. Para fazer um pedido primeiro era necessário validar o usuário com o serviço de autenticação, depois validar o endereço fornecido através da API do via-cep e ainda buscar as informações dos produtos no microsserviço de produtos. A comunicação entre os microsserviços foi feita através do Apache Kafka (primeira vez tocando nele) e, para se comunicar com o Via-cep foi utilizado o [Feign](https://spring.io/projects/spring-cloud-openfeign).

![resposta api produtos](/assets/media/postman-orders-all.png)

A experiência com o Kafka foi bem desafiadora... Compreendia de forma superficial os tipos de comunicação que poderiam existir em um sistema distribuído e fui implementando conforme ia aprendendo mais. No fim, deu certo (_in the happy case_)!

Depois do desafio concluído voltei ao Kafka para entende-lo melhor, mexi com o Avro para a serialização etc... É uma tecnologia incrível e fiquei muito feliz de ter a oportunidade de experimenta-lo com o apoio de pessoas capacitadas.

Fica um artigozinho legal que me ajudou e talvez te ajude também se você não manja muito: [Artigo Kafka que me ajudou :)](https://medium.com/@douglsantos/apache-kafka-trabalhando-com-convers%C3%B5es-do-avro-schema-2aa11b382af8)

### Concluindo

Antes de mais nada: deixei de fora algumas outras tecnologias utilizadas como o Docker, o Postman... e também não falei dos testes unitários/integração. Queria fazer um post rápido, mas ta tudinho lá no GitHub.

Agora sim... Concluindo, esse foi o segundo desafio de microsserviços da bolsa, mas com toda certeza foi o que eu tirei melhor proveito. Tinha algumas expectativas, como desenvolver funcionalidades além do que foi proposto, porém o que eu aprendi de verdade é que sistemas distribuídos são muito difíceis e que você deve respeitá-los. No momento, estou estudando o livro [Criando Microsserviços](https://www.amazon.com.br/Criando-Microsservi%C3%A7os-Projetando-Componentes-Especializados/dp/6586057884/ref=asc_df_6586057884/?tag=googleshopp00-20&linkCode=df0&hvadid=379748659420&hvpos=&hvnetw=g&hvrand=8647102754583414513&hvpone=&hvptwo=&hvqmt=&hvdev=c&hvdvcmdl=&hvlocint=&hvlocphy=9102544&hvtargid=pla-1646134392410&psc=1) e minha mente abriu bastante para o tema e já tenho vários planos para refazer totalmente esse desafio. Quem sabe no futuro eu não faço um outro post. Obrigado!

