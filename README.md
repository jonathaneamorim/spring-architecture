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

---

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

---

## Principais Aplicações

### REST APIs
**Criação de APIs RESTful robustas e bem documentadas para integração entre sistemas.**

**Características principais:**
- **Fluxo da Requisição:** O `DispatcherServlet` intercepta as chamadas e roteia para `@RestController`.
- **Suporte Nativo a JSON:** Jackson serializa/desserializa objetos Java automaticamente.
- **Validação de Dados:** Integração com *Jakarta Bean Validation* usando `@Valid`, `@NotNull`, `@Email`.
- **Documentação Automática:** Swagger/OpenAPI gera interface interativa de testes.

**Exemplo prático:**
```java
@RestController
@RequestMapping("/api/usuarios")
public class UsuarioController {
    
    @PostMapping
    public ResponseEntity<UsuarioDTO> criar(@Valid @RequestBody UsuarioDTO dto) {
        // Validação automática, retorna 400 se inválido
        return ResponseEntity.status(201).body(usuarioService.salvar(dto));
    }
    
    @GetMapping("/{id}")
    public UsuarioDTO obter(@PathVariable Long id) {
        return usuarioService.buscarPorId(id);
    }
}
```

**O que está acontecendo aqui:**
1. `@RestController` → Marca a classe como controlador REST (retorna JSON, não HTML)
2. `@RequestMapping("/api/usuarios")` → Define o caminho base da API (`/api/usuarios`)
3. `@PostMapping` → Método POST para criar novo usuário (`POST /api/usuarios`)
4. `@Valid @RequestBody UsuarioDTO dto` → Valida os dados recebidos (email, CPF, etc). Se inválido, retorna automaticamente **400 Bad Request**
5. `ResponseEntity.status(201)` → Retorna status **201 Created** (sucesso na criação)
6. `@GetMapping("/{id}")` → Busca um usuário por ID (`GET /api/usuarios/1`)
7. `@PathVariable Long id` → Extrai o ID da URL e passa como parâmetro

### Microserviços
**Arquitetura distribuída onde cada serviço é independente, escalável e sem ponto único de falha.**

**Componentes principais do Spring Cloud:**
- **Eureka (Service Discovery)** — Serviços se descobrem pelo nome sem precisar de IPs fixos. Cada um se registra ao inicializar.
- **Resilience4j (Circuit Breaker)** — Evita falhas em cascata: se um serviço cai, os outros retornam um fallback em vez de travar.
- **API Gateway** — Entrada única que roteia requisições, centraliza segurança e logging.
- **Spring Cloud Config** — Configurações centralizadas versionadas (Git).

**Fluxo típico:**
```
[Cliente] → [API Gateway] → [Eureka] → [Service A / Service B / Service C]
                                         ↓
                                      [Circuit Breaker]
                                      [Fallback Response]
```

**O que está acontecendo aqui:**
1. **Cliente** faz uma requisição
2. **API Gateway** intercepta e roteia para o serviço correto (ex: `/pedidos` → Service de Pedidos)
3. **Eureka** (Service Registry) tem registro de todos os serviços ativos e seus IPs
4. **Service A/B/C** recebem a requisição e processam
5. Se um serviço cair → **Circuit Breaker** deteta e retorna um **Fallback** (resposta alternativa) para não travar o sistema
6. Exemplo: Se o serviço de email está fora, retorna erro amigável em vez de travar a requisição de pedido

### Aplicações Web (Server-Side Rendering)
**Portais web completos com renderização no servidor, login seguro e suporte a múltiplos idiomas.**

**Características principais:**
- **Thymeleaf:** Motor de templates que injeta dados no HTML dinamicamente (`th:text`, `th:each`).
- **Spring Security:** Cadeia de filtros que gerencia autenticação, autorização e proteção contra CSRF.
- **Internacionalização (i18n):** Suporte nativo a múltiplos idiomas.
- **Sessões:** Controle automático de cookies e estado do usuário.

**Exemplo com Thymeleaf:**
```html
<!-- Template HTML -->
<div th:each="usuario : ${usuarios}">
    <p th:text="${usuario.nome}"></p>
</div>
```

```java
@Controller
public class UsuarioWeb {
    @GetMapping("/usuarios")
    public String listar(Model model) {
        model.addAttribute("usuarios", usuarioService.buscarTodos());
        return "usuarios/lista"; // Renderiza usuarios/lista.html
    }
}
```

**O que está acontecendo aqui:**
1. `@Controller` → Marca como controlador MVC (diferente de `@RestController`)
2. `@GetMapping("/usuarios")` → Quando alguém acessa `/usuarios`, executa este método
3. `usuarioService.buscarTodos()` → Busca no banco todos os usuários
4. `model.addAttribute("usuarios", ...)` → Coloca a lista de usuários em um objeto chamado `usuarios`
5. `return "usuarios/lista"` → Procura por arquivo `usuarios/lista.html` em `templates/`
6. **No HTML:** `th:each="usuario : ${usuarios}"` → Itera sobre cada usuário da lista
7. `th:text="${usuario.nome}"` → Substitui o texto HTML pelo nome do usuário
8. **Resultado:** O navegador recebe HTML completo já renderizado com dados do banco (Server-Side Rendering)

**Diferença importante:**
- **REST API:** Retorna JSON (cliente renderiza)
- **Thymeleaf:** Retorna HTML pronto (servidor renderiza)

### Batch Processing
**Processamento de grandes volumes de dados em lote com recuperação automática de falhas.**

**Componentes principais:**
- **Jobs & Steps:** Um `Job` é um processo completo que contém `Steps` (etapas).
- **ItemReader:** Lê registros um por um de arquivos, bancos ou APIs.
- **ItemProcessor:** Aplica transformações e validações nos dados.
- **ItemWriter:** Grava um lote inteiro no banco em uma única transação (otimizado).
- **Retry & Skip:** Recuperação automática de falhas sem perder o progresso.

**Fluxo em Chunks:**
```
Ler 100 registros → Processar → Gravar 100 de uma vez
Ler 100 registros → Processar → Gravar 100 de uma vez
[repetir até fim]
```

**Exemplo prático:**
```java
@Configuration
@EnableBatchProcessing
public class BatchConfig {
    @Bean
    public Job importarFolha(Step processarPagamentos) {
        return jobBuilderFactory.get("importarFolha")
            .start(processarPagamentos)
            .build();
    }
}
```

**O que está acontecendo aqui:**
1. `@Configuration` → Marca como classe de configuração
2. `@EnableBatchProcessing` → Ativa o Spring Batch nesta aplicação
3. `@Bean` → Define um bean (objeto gerenciado pelo Spring)
4. `Job importarFolha(Step processarPagamentos)` → Cria um Job chamado "importarFolha" que executa o Step "processarPagamentos"
5. `jobBuilderFactory.get("importarFolha")` → Factory que constrói jobs
6. `.start(processarPagamentos)` → Define qual Step executar primeiro
7. `.build()` → Finaliza a configuração do Job

**Na prática:**
- Este Job pode ser agendado para rodar todo dia às 22h (off-peak)
- Lê 10.000 funcionários → Processa salários → Grava de 100 em 100 no banco
- Se falhar na linha 7.500, retoma de lá na próxima execução (não recomeça do zero!)

### Mensageria (Event-Driven Architecture)
**Comunicação assíncrona entre serviços via filas e tópicos, desacoplando sistemas.**

**Opções de mensageria:**
- **Spring AMQP (RabbitMQ):** Filas tradicionais com garantia de entrega e confirmação.
- **Spring Kafka:** Streaming de eventos em tempo real, ideal para big data e análises.
- **ActiveMQ:** Alternativa JMS compatível.

**Exemplo com Kafka:**
```java
// Produtor
@Service
public class PedidoService {
    @Autowired
    private KafkaTemplate<String, Pedido> kafkaTemplate;
    
    public void criar(Pedido pedido) {
        // Salva e envia evento
        pedidoRepository.save(pedido);
        kafkaTemplate.send("pedidos-criados", pedido);
    }
}

// Consumidor
@Service
public class NotificacaoService {
    @KafkaListener(topics = "pedidos-criados")
    public void aoReceberPedido(Pedido pedido) {
        emailService.enviar(pedido.getEmail(), "Pedido recebido!");
    }
}
```

**O que está acontecendo aqui:**

**LADO PRODUTOR (PedidoService):**
1. `@Service` → Marca como serviço (lógica de negócio)
2. `@Autowired private KafkaTemplate` → Injeta o template para enviar mensagens
3. `pedidoRepository.save(pedido)` → Salva o pedido no banco de dados PRIMEIRO
4. `kafkaTemplate.send("pedidos-criados", pedido)` → Publica um evento no tópico "pedidos-criados" com os dados do pedido
5. **Importante:** O método retorna IMEDIATAMENTE (não espera o email ser enviado)

**LADO CONSUMIDOR (NotificacaoService):**
1. `@KafkaListener(topics = "pedidos-criados")` → Fica escutando o tópico "pedidos-criados"
2. Quando uma mensagem chega, executa o método `aoReceberPedido()`
3. `emailService.enviar(...)` → Envia o email de confirmação
4. Se o email falhar, o Kafka tenta novamente automaticamente

**Fluxo visual:**
```
Cliente cria pedido
↓
PedidoService.criar() → Salva BD + Publica evento (RÁPIDO, retorna logo)
↓
NotificacaoService escuta o evento (pode estar em outro servidor!)
↓
Envia email (assincronamente)
```

**Vantagem:** Se o serviço de email cair, o pedido já foi criado e o evento fica na fila até o email voltar!

### Data & Persistence
**Acesso a dados simplificado para bancos relacionais, NoSQL e cache, com zero boilerplate.**

**Componentes principais:**
- **JPA/Hibernate:** ORM que mapeia objetos Java para tabelas automaticamente.
- **Spring Data JPA:** Repositórios prontos para CRUD sem escrever SQL.
- **Query Methods:** O Spring deduz SQL apenas pelo nome do método.
- **MongoDB/Redis:** Suporte nativo para NoSQL e cache em memória.
- **Transações Gerenciadas:** `@Transactional` garante commit/rollback automático.

**Exemplo prático:**
```java
public interface UsuarioRepository extends JpaRepository<Usuario, Long> {
    // Spring deduz o SQL automaticamente
    List<Usuario> findByNomeAndAtivo(String nome, Boolean ativo);
    
    // Queries customizadas
    @Query("SELECT u FROM Usuario u WHERE u.email = :email")
    Optional<Usuario> buscarPorEmail(@Param("email") String email);
}

// Uso
@Service
public class UsuarioService {
    @Autowired
    private UsuarioRepository repo;
    
    @Transactional
    public void salvar(Usuario usuario) {
        repo.save(usuario); // Commit automático
    }
}
```

**O que está acontecendo aqui:**

**NA INTERFACE:**
1. `extends JpaRepository<Usuario, Long>` → Herda automaticamente métodos CRUD:
   - `.save()`, `.findById()`, `.delete()`, `.findAll()`, etc.
2. `findByNomeAndAtivo(String nome, Boolean ativo)` → Nome mágico!
   - Spring deduz: `SELECT * FROM usuarios WHERE nome = ? AND ativo = ?`
   - **Regra:** `findBy` + nome_campo + `And` + nome_campo
   - Outros exemplos: `findByEmail()`, `findByIdadeGreaterThan()`, `findByNomeContains()`
3. `@Query("...")` → Query customizada quando o nome mágico não é suficiente
4. `@Param("email")` → Liga o parâmetro `:email` da query ao parâmetro do método
5. `Optional<Usuario>` → Pode retornar vazio (sem erro) se não encontrar

**NO SERVIÇO:**
1. `@Autowired private UsuarioRepository repo` → Injeta o repositório
2. `@Transactional` → Abre uma transação de banco de dados
3. `repo.save(usuario)` → Insere ou atualiza (se tiver ID já preenche)
4. Ao **final do método** → Spring faz **commit automaticamente**
5. Se uma **exceção é lançada** → Spring faz **rollback automático** (desfaz tudo)

**Exemplo prático de transação:**
```java
@Transactional
public void criar3Usuarios(Usuario u1, Usuario u2, Usuario u3) {
    repo.save(u1); // OK
    repo.save(u2); // OK
    if (u3.getEmail().isEmpty()) throw new Exception("Email vazio");
    repo.save(u3); // NÃO ACONTECE
    // Resultado: Os 3 são deletados (rollback)
}
```

**Suporte para:**
- **Relacionais:** MySQL, PostgreSQL, Oracle, SQL Server
- **NoSQL:** MongoDB, DynamoDB, Cassandra
- **Cache:** Redis, Memcached

---

## Exemplo prático - REST API com Docker

### Criação da aplicação 
Formas de iniciar uma aplicação Spring:
- Spring Initializr (Web): Interface mais amigável, onde as configurações básicas do projeto são definidas: gerenciador de dependências (Maven ou Gradle), versão do Java e dependências.
![Spring.io](/media/springio.png "spring.io")

- Via IDE: Ferramentas como IntelliJ IDEA (Ultimate), Eclipse (com Spring Tools) ou VS Code (com o Spring Boot Extension Pack) possuem integração nativa com o Spring Initializr na tela de criação de um "Novo Projeto".
- Maven (Manual): via pom.xml
``` xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.2.0</version>
</parent>

<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
</dependencies>
```
- Gradle (Manual): via build.gradle
```groovy
plugins {
    id 'org.springframework.boot' version '3.2.0'
    id 'io.spring.dependency-management' version '1.1.4'
    id 'java'
}
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
}
```

### Código
- Entry Point (Já vem criado):
```java
package com.psiconet;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.data.web.config.EnableSpringDataWebSupport;

@SpringBootApplication
public class PsiconetBackendApplication {

    public static void main(String[] args) {
        SpringApplication.run(PsiconetBackendApplication.class, args);
    }
}
```

- Controller Demo (Criados manualmente): Endpoints que farão a comunicação com o client
```java
package com.psiconet.controllers;

@RestController
@RequestMapping("/auth")
@RequiredArgsConstructor
public class AuthController {
    private final AuthService authService;

    @PostMapping("/register/patient")
    public ResponseEntity<String> registerPatient(@Valid @RequestBody PatientRegisterRequest data) {
        authService.registerPatient(data);
        return ResponseEntity.status(HttpStatus.CREATED).body("Paciente cadastrado com sucesso!");
    }

    @PostMapping("/register/psychologist")
    public ResponseEntity<String> registerPsychologist(@Valid @RequestBody PsychologistRegisterRequest data) {
        authService.registerPsychologist(data);
        return ResponseEntity.status(HttpStatus.CREATED).body("Psicólogo cadastrado com sucesso!");
    }

    @PostMapping("/login")
    public ResponseEntity<LoginResponseDTO> login(@Valid @RequestBody AuthenticationDTO data) {
        String token = authService.login(data);
        return ResponseEntity.ok(new LoginResponseDTO(token));
    }
}
```

- Arquivo de propriedades do sistema (Já vem criado, vem com informações base mas é possível adicionar mais posteriormente): 
```properties
spring.application.name=psiconet-backend
server.port=8080

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=false
spring.jpa.properties.hibernate.format_sql=false
spring.jpa.open-in-view=false

api.security.token.secret=
api.security.token.expiration_hours=

spring.datasource.url=jdbc:postgresql://localhost:5432/psiconet
spring.datasource.username=psiconet_db
spring.datasource.password=NAODEIXESUASENHAEXPOSTAAQUI

# Parametros do usuario ADMIN inicial
BASE_EMAIL=admin@psiconet.com
BASE_PASSWORD=NAODEIXESUASENHAEXPOSTAAQUI
```

- DockerFile: Arquivo de receita. Ele define como construir a imagem Docker da sua aplicação.
```dockerfile
# ==========================================================
# STAGE 1 - BUILD
# Responsável por compilar a aplicação Spring Boot
# e gerar o arquivo .jar
# ==========================================================

# Utiliza uma imagem que já possui Maven 3.8.3 e Java 17
# O "AS builder" cria um apelido para esta etapa,
# permitindo copiar arquivos dela posteriormente.
FROM maven:3.8.3-eclipse-temurin-17 AS builder

# Define o diretório de trabalho dentro do container.
# Caso não exista, ele será criado automaticamente.
WORKDIR /build

# Copia o pom.xml para dentro do container.
# Esse arquivo contém as dependências e configurações do Maven.
COPY pom.xml .

# Copia todo o código-fonte da aplicação para o container.
# Após este comando, o container possui acesso ao projeto.
COPY src ./src

# Executa o build da aplicação:
# - clean: remove artefatos de builds anteriores
# - package: compila e gera o .jar
# -DskipTests: ignora a execução dos testes automatizados
#
# Ao final será gerado algo semelhante a:
# /build/target/meu-projeto-0.0.1-SNAPSHOT.jar
RUN mvn clean package -DskipTests

# ==========================================================
# STAGE 2 - RUNTIME
# Responsável apenas por executar a aplicação.
#
# Utilizamos uma nova imagem para evitar levar Maven,
# código-fonte e arquivos desnecessários para produção.
# Isso reduz o tamanho da imagem final.
# ==========================================================

# Imagem contendo apenas o Java necessário para executar o .jar
FROM eclipse-temurin:17-jdk

# Diretório onde a aplicação ficará armazenada
WORKDIR /app

# Copia apenas o .jar gerado na etapa "builder"
#
# Origem:
#   /build/target/*.jar
#
# Destino:
#   /app/app.jar
#
# O "--from=builder" indica que o arquivo será copiado
# da primeira etapa do Dockerfile.
COPY --from=builder /build/target/*.jar app.jar

# Documenta que a aplicação utiliza a porta 8080.
# Não publica a porta automaticamente.
EXPOSE 8080

# Comando executado quando o container iniciar.
#
# -XX:+UseContainerSupport
#   Faz a JVM respeitar limites de CPU e memória do container.
#
# -XX:MaxRAMPercentage=75.0
#   Permite que a JVM utilize até 75% da memória disponível.
#
# -jar /app/app.jar
#   Inicia a aplicação Spring Boot.
ENTRYPOINT [
    "java",
    "-XX:+UseContainerSupport",
    "-XX:MaxRAMPercentage=75.0",
    "-jar",
    "/app/app.jar"
]
```

- Docker-compose: Arquivo com definições dos conteineres que serão criados para a aplicação
```yaml
# ==========================================================
# DOCKER COMPOSE
#
# Responsável por orquestrar os containers da aplicação.
#
# Neste ambiente serão criados:
# - Container da aplicação Spring Boot
# - Container do PostgreSQL
# - Volume persistente para os dados do banco
# ==========================================================

services:

  # ========================================================
  # SERVIÇO DA APLICAÇÃO
  # ========================================================
  app:

    # Publica a porta da aplicação
    #
    # HOST:CONTAINER
    #
    # A aplicação roda internamente na porta 8080,
    # mas ficará acessível externamente pela porta 80.
    #
    # Exemplo:
    # http://IP_DO_SERVIDOR
    ports:
      - "80:8080"

    # Define como a imagem será construída.
    #
    # context: .
    # Utiliza a pasta atual como contexto do build.
    #
    # dockerfile: Dockerfile
    # Informa qual Dockerfile deve ser utilizado.
    build:
      context: .
      dockerfile: Dockerfile

    # Nome da imagem gerada.
    #
    # IMAGE_TAG será lida do ambiente.
    #
    # Exemplo:
    # localregistry/psiconet_app:v1.0.0
    #
    # Caso IMAGE_TAG não exista:
    # localregistry/psiconet_app:latest
    image: localregistry/psiconet_app:${IMAGE_TAG:-latest}

    # Nome fixo do container.
    #
    # Sem isso o Docker geraria nomes aleatórios.
    container_name: psiconet_app

    # Reinicia automaticamente o container
    # caso ele pare inesperadamente.
    restart: always

    # Carrega variáveis do arquivo .env
    env_file:
      - .env

    # Variáveis de ambiente disponibilizadas
    # para a aplicação Spring Boot.
    environment:

      # URL de conexão com o banco PostgreSQL.
      #
      # Observe que "db" é o nome do serviço,
      # não um IP.
      SPRING_DATASOURCE_URL: jdbc:postgresql://db:5432/${POSTGRES_DB}

      # Usuário do banco
      SPRING_DATASOURCE_USERNAME: ${POSTGRES_USER}

      # Senha do banco
      SPRING_DATASOURCE_PASSWORD: ${SPRING_DATASOURCE_PASSWORD}

      # Estratégia de geração do schema.
      #
      # update:
      # Atualiza tabelas automaticamente.
      SPRING_JPA_HIBERNATE_DDL_AUTO: ${SPRING_JPA_HIBERNATE_DDL_AUTO:-update}

      # Chave secreta utilizada para gerar JWT
      APP_JWT_SECRET: ${APP_JWT_SECRET}

      # Tempo de expiração do token JWT
      APP_JWT_EXPIRATION_MS: ${APP_JWT_EXPIRATION_MS}

    # Define dependência do banco.
    #
    # A aplicação só será iniciada quando o
    # PostgreSQL estiver saudável.
    depends_on:
      db:
        condition: service_healthy

    # Política adicional de reinício.
    #
    # Reinicia somente em caso de falha.
    #
    # Obs:
    # O bloco deploy é mais utilizado em Docker Swarm.
    deploy:
      restart_policy:
        condition: on-failure

  # ========================================================
  # SERVIÇO DO BANCO DE DADOS
  # ========================================================
  db:

    # Utiliza PostgreSQL 15
    image: postgres:15

    # Carrega variáveis do arquivo .env
    #
    # Exemplo:
    # POSTGRES_DB
    # POSTGRES_USER
    # POSTGRES_PASSWORD
    env_file:
      - .env

    # Volume persistente.
    #
    # Garante que os dados continuem existindo
    # mesmo após remover ou recriar o container.
    volumes:
      - db_data_prod:/var/lib/postgresql/data

# ==========================================================
# VOLUMES
#
# Área de armazenamento persistente gerenciada
# pelo Docker.
#
# Os dados do PostgreSQL serão armazenados aqui.
# ==========================================================
volumes:
  db_data_prod:
```

- Readme: Quando se tem um projeto que será acessado por outras pessoas, é mais elegante deixar um arquivo readme detalhando o funcionamento e o start do projeto.
    #### Execução Local
    1. Clone o repositório.
    2. Configure o arquivo `.env` (baseie-se no `.env.example`).
    3. Execute via Docker:
    ```bash
    docker compose up -d --build
    ```
    Ou via Maven:
    ```bash
    ./mvnw spring-boot:run
    ```

- pom.xml: Arquivos de dependências que facilitam o desenvolvimento de aplicações complexas e robustas
```xml
...
    <properties>
        <java.version>17</java.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springdoc</groupId>
            <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
            <version>2.6.0</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-security</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-validation</artifactId>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok-mapstruct-binding</artifactId>
            <version>0.2.0</version>
        </dependency>
        <dependency>
            <groupId>com.auth0</groupId>
            <artifactId>java-jwt</artifactId>
            <version>4.4.0</version>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-devtools</artifactId>
            <scope>runtime</scope>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <scope>runtime</scope>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework.security</groupId>
            <artifactId>spring-security-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
...
```

Acesse o projeto e veja você mesmo: [Link](https://github.com/jonathaneamorim/Psiconet-Backend)

![12,99](/media/ia_1299.png "Hora da pratica")