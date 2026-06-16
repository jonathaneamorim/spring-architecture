![Spring Boot]( /media/Spring_Framework_Logo_2018.svg.png "Spring")

## O que é?
Spring Boot é um framework Java de código aberto (quando o código-fonte é disponibilizado publicamente) que facilita a criação de aplicações standalone (programas que são independentes, não precisam de plugins ou servidores), prontas para produção, com configuração mínima. É construído sobre o ecossistema Spring Framework.

### Spring Framework x Spring Boot
![Spring x Springboot](/media/springxspringboot.png "Spring x Springboot")
O Spring Framework é o Núcleo - Uma plataforma para construir aplicações Java, mas que exige muitas configurações manuais: Servidor, dependências, beans, etc.

O Spring Boot é construído em cima do Spring Framework com o objetivo de eliminar essas configurações manuais.

Exemplo: 
No Spring Framework a configuração de Datasource (Arquivo de configurações) é feita na mão:
```java
@Bean
public DataSource dataSource() {
    DriverManagerDataSource ds = new DriverManagerDataSource();
    ds.setDriverClassName("com.mysql.jdbc.Driver");
    ds.setUrl("jdbc:mysql://localhost/mydb");
    ds.setUsername("root");
    ds.setPassword("senha");
    return ds;
}
```
Ja no Spring Boot existe um arquivo responsável por essas configurações:
```properties
spring.datasource.url=jdbc:mysql://localhost/mydb
spring.datasource.username=root
spring.datasource.password=senha
```

Importante: O Spring Boot não substitui o uso do Framework.

## Criado em
Abril de 2014  
Versão 1.0 lançada

## Empresa
Criado pela: Pivotal (2014–2019)  
Atualmente: VMware / Broadcom  
![Pitoval](/media/pivotal.png "Pitoval")

## Linguagem 
Java (JVM)  
Também: Kotlin, Groovy

## Como Funciona o Spring Boot?
- Auto-Configuração
  - Detecta automaticamente as dependências no classpath e configura beans necessários. Ex: se o H2 está presente, configura um DataSource em memória.
    ```xml
    <dependency>
        <groupId>com.h2database</groupId> 
        <artifactId>h2</artifactId> 
        <scope>runtime</scope> 
    </dependency>
    ```
- Starters
  - Dependências pré-empacotadas que trazem tudo necessário para uma funcionalidade. Ex: spring-boot-starter-web inclui Tomcat + Spring MVC.
  Substituir tudo isso:  
    ```xml
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId> 
    </dependency> 

    <dependency> 
        <groupId>org.apache.tomcat.embed</groupId>
        <artifactId>tomcat-embed-core</artifactId> 
    </dependency> 

    <dependency> 
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId> 
    </dependency>
    ```
    Por isso:
    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    ```
- Actuator
  - Endpoints prontos para monitoramento em produção: /health, /metrics, /info, /env — integração com Prometheus e Grafana.
    Configuração no properties para expor os endpoints de monitoramento:
    ```properties
    management.endpoints.web.exposure.include=*
    management.endpoints.web.exposure.include=health,info,metrics,prometheus
    management.server.port=8081
    management.endpoints.web.base-path=/actuator
    ```
    Exemplos de endpoints disponíveis automaticamente:
    - /actuator/health
    - /actuator/info
    - /actuator/metrics
    - /actuator/env
    - /actuator/loggers

    Exemplos:  
    ![Actuator 1](/media/actuator1.png "Actuator 1")
    ![Actuator 1](/media/actuator2.png "Actuator 2")
    ![Actuator 1](/media/actuator3.png "Actuator 3")

## Principais Aplicações
### REST APIs
Criação de APIs RESTful com Spring MVC. Suporte nativo a JSON, validação e documentação com OpenAPI/Swagger.
![Actuator 1](/media/swagger.png "Swagger")
### Microserviços
Spring Cloud resolve três problemas de quem tem múltiplos serviços rodando:
- Eureka — os serviços se encontram pelo nome, sem precisar saber o IP um do outro
- Resilience4j — se um serviço cair, os outros não travam esperando; retornam uma resposta alternativa
- API Gateway — porta de entrada única que roteia as requisições para o serviço certo
### Aplicações Web
Servidor MVC completo com Thymeleaf, segurança (Spring Security) e internacionalização.
### Batch Processing
Spring Batch para processamento em lote de grandes volumes de dados com steps, jobs e readers/writers.
### Mensageria
Integração com Kafka, RabbitMQ e ActiveMQ via Spring AMQP e Spring for Apache Kafka.
### Data & Persistence
JPA/Hibernate, Spring Data, MongoDB, Redis — com repositórios automáticos e transações gerenciadas.

## Exemplo prático - REST API com Docker
Demo: docker run -p 8080:8080 springio/gs-rest-service
## Ecossistema Spring

## Vantagens & Desvantagens

