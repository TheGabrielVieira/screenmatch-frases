# ScreenMatch Frases

API REST em Java que fornece frases aleatórias de filmes e séries para consumo por uma aplicação cliente. A resposta inclui o título da produção, a frase, o personagem e o endereço do pôster.

## Objetivo

O projeto demonstra uma aplicação web organizada em camadas com Spring: recebe uma requisição HTTP, consulta uma frase no banco de dados relacional e retorna os dados em formato JSON. A rota implementada é `GET /series/frases`.

## Tecnologias

| Tecnologia | Uso no projeto |
|---|---|
| Java 21 | Linguagem e versão configurada no Maven |
| Spring Boot 4.0.3 | Inicialização e configuração da aplicação |
| Spring Web MVC | Exposição do endpoint REST |
| Spring Data JPA | Abstração de persistência e repositório |
| Hibernate | Implementação JPA usada pelo Spring Data JPA |
| PostgreSQL | Banco relacional configurado para a aplicação |
| Maven 3.9.12 (Maven Wrapper) | Build e gerenciamento de dependências |

As dependências declaradas no `pom.xml` incluem `spring-boot-starter-data-jpa`, `spring-boot-starter-webmvc`, o driver PostgreSQL e Spring Boot DevTools. Para testes, estão declarados os starters de teste para JPA e Web MVC.

## Estrutura do projeto

```text
.
├── .mvn/wrapper/                     # Configuração do Maven Wrapper
├── src/
│   ├── main/
│   │   ├── java/br/com/screenmatchfrases/
│   │   │   ├── config/               # Configuração de CORS
│   │   │   ├── controller/           # Endpoints HTTP
│   │   │   ├── dto/                   # Formato de resposta da API
│   │   │   ├── model/                 # Entidade persistida
│   │   │   ├── repository/            # Acesso a dados via JPA
│   │   │   ├── service/               # Lógica da aplicação
│   │   │   └── ScreenmatchFrasesApplication.java
│   │   └── resources/
│   │       ├── application.properties
│   │       ├── application-dev.properties  # Configuração local, ignorada pelo Git
│   │       ├── static/                # Diretório padrão, atualmente sem arquivos
│   │       └── templates/             # Diretório padrão, atualmente sem arquivos
│   └── test/java/                     # Teste de carregamento do contexto
├── dados_screenmatch_frases_db.txt    # Script SQL com registros de exemplo
├── mvnw / mvnw.cmd                    # Maven Wrapper para Unix / Windows
└── pom.xml                            # Dependências, versão Java e build
```

### Componentes principais

- **`ScreenmatchFrasesApplication`**: ponto de entrada; inicia a aplicação Spring Boot.
- **`FraseController`**: expõe `GET /series/frases` e delega a operação ao serviço.
- **`FraseService`**: obtém a entidade do repositório e a transforma em `FraseDTO`.
- **`FraseRepository`**: interface Spring Data JPA que consulta uma frase aleatória.
- **`Frase`**: entidade mapeada para a tabela `frases`, com `id`, `titulo`, `frase`, `personagem` e `poster`.
- **`FraseDTO`**: record usado como resposta, com título, frase, personagem e pôster.
- **`CorsConfiguration`**: permite acesso à API a partir da origem `http://127.0.0.1:5501`.
- **`ScreenmatchFrasesApplicationTests`**: teste de contexto com `@SpringBootTest`.

## Fluxo da aplicação

```text
Cliente HTTP
    → GET /series/frases
    → FraseController
    → FraseService
    → FraseRepository
    → PostgreSQL (tabela frases)
    → FraseDTO serializado como JSON
```

O repositório declara uma consulta que ordena os registros por `RANDOM` para obter uma frase aleatória. O controller devolve o DTO, que o Spring MVC serializa em JSON.

## Requisitos

- JDK 21;
- PostgreSQL instalado e em execução;
- Git, para clonar o repositório.

O Maven Wrapper incluído baixa e usa a versão Maven definida pelo projeto, então não é necessário instalar o Maven separadamente. Na primeira execução, pode ser necessário acesso à rede para baixar Maven e dependências.

## Como obter o projeto

```bash
git clone <URL_DO_REPOSITORIO>
cd screenmatch-frases
```

Substitua `<URL_DO_REPOSITORIO>` pelo endereço Git deste repositório.

## Configuração do banco

A aplicação ativa o perfil `dev` em `src/main/resources/application.properties`. A configuração local `src/main/resources/application-dev.properties` está excluída pelo `.gitignore` e, portanto, pode não estar presente após clonar o projeto. Crie esse arquivo localmente e configure as credenciais do seu PostgreSQL, por exemplo:

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/screenmatch_frases
spring.datasource.username=SEU_USUARIO
spring.datasource.password=SUA_SENHA
spring.datasource.driver-class-name=org.postgresql.Driver
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.format-sql=true
```

Crie o banco `screenmatch_frases` no PostgreSQL antes de iniciar a aplicação. O arquivo de configuração existente no projeto aponta para `localhost` e banco `screenmatch_frases`; ajuste URL, usuário e senha conforme seu ambiente. Não compartilhe credenciais reais nem as versione.

O arquivo `dados_screenmatch_frases_db.txt` contém comandos `INSERT` para a tabela `frases`. Depois de criar a tabela (o perfil local configura `spring.jpa.hibernate.ddl-auto=update`), execute os comandos desse arquivo no banco configurado para inserir os dados de exemplo. A aplicação depende de registros disponíveis para retornar uma frase.

## Instalação e execução

Na raiz do repositório, use o wrapper apropriado ao sistema:

**Windows (PowerShell ou Prompt de Comando):**

```powershell
./mvnw.cmd spring-boot:run
```

**Linux ou macOS:**

```bash
./mvnw spring-boot:run
```

Com o PostgreSQL preparado e o perfil `dev` configurado, a aplicação inicia por padrão na porta `8080` do Spring Boot.

Para empacotar a aplicação:

```bash
./mvnw package
```

No Windows, substitua `./mvnw` por `./mvnw.cmd`. O artefato gerado fica em `target/`.

## Utilização da API

Faça uma requisição GET:

```bash
curl http://localhost:8080/series/frases
```

O formato de resposta definido pelo `FraseDTO` é:

```json
{
  "titulo": "Stranger things",
  "frase": "Amigos não mentem",
  "personagem": "Eleven",
  "poster": "https://exemplo.com/poster.jpg"
}
```

Os valores acima ilustram o formato; o endpoint retorna um registro disponível no banco. O CORS configurado permite a origem `http://127.0.0.1:5501`.

## Dados e resultados

O script `dados_screenmatch_frases_db.txt` contém dez comandos de inserção para a tabela `frases`, com frases associadas a produções e personagens e URLs de pôsteres. O resultado funcional previsto é uma resposta JSON com os quatro campos do DTO para uma frase escolhida aleatoriamente. Não há métricas ou resultados de execução documentados no repositório.

## Testes

O projeto contém um teste de carregamento do contexto Spring. Para executá-lo:

```bash
./mvnw test
```

No Windows, use `./mvnw.cmd test`. Como o teste inicializa o contexto Spring, configure o banco e o perfil necessários ao ambiente de teste.

## Próximos passos possíveis

- Parametrizar credenciais e propriedades por variáveis de ambiente, evitando configurações locais versionadas.
- Revisar a configuração de dialeto JPA e a consulta aleatória para assegurar compatibilidade com PostgreSQL e com a versão do Hibernate utilizada.
- Adicionar testes para o endpoint, serviço e consulta ao banco.
- Documentar respostas de erro e adicionar validação para banco sem registros.
- Fornecer configuração reproduzível do PostgreSQL para desenvolvimento, por exemplo com Docker Compose.

## Autoria e licença

O `pom.xml` não identifica autor nem define uma licença para o projeto. Essas informações não foram incluídas como fatos; adicione-as quando houver dados confirmados.
