# ANÁLISE DE PROJETOS GITHUB COM CONSUMO DE APIs

## Introdução

O objetivo desta atividade foi pesquisar, clonar e analisar 10 repositórios públicos do GitHub que utilizam APIs em cenários reais. A API (Application Programming Interface) é uma interface de comunicação que permite que diferentes aplicações troquem dados e funcionalidades de forma padronizada. No contexto de desenvolvimento moderno, APIs são essenciais para integrar frontends, backends, serviços web, aplicativos mobile e sistemas externos.

A partir da análise do código-fonte, foi possível confirmar a linguagem, o framework, a biblioteca de comunicação HTTP, o endpoint utilizado, o método do protocolo e a finalidade da integração. Essa abordagem ajuda a entender como sistemas reais estruturam chamadas HTTP, tratam respostas JSON e conectam o cliente com serviços externos.

## Tabela comparativa

| Nº | Projeto GitHub | Linguagem | Framework | API consumida | Finalidade | Arquivo de integração |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | gothinkster/realworld | TypeScript/Playwright | API contract + testes browser | https://api.realworld.show/api | validar acesso à API pública | `specs/e2e/health.spec.ts` |
| 2 | lujakob/nestjs-realworld-example-app | TypeScript | NestJS | RealWorld API | expor endpoints de artigos e usuários | `src/article/article.controller.ts` |
| 3 | gothinkster/spring-boot-realworld-example-app | Java | Spring Boot | RealWorld API | expor endpoints REST para artigos | `src/main/java/io/spring/api/ArticlesApi.java` |
| 4 | gothinkster/laravel-realworld-example-app | PHP | Laravel | RealWorld API | listar e persistir artigos | `app/Http/Controllers/Api/ArticleController.php` |
| 5 | gothinkster/flask-realworld-example-app | Python | Flask | RealWorld API | implementar rotas de artigos e comentários | `conduit/articles/views.py` |
| 6 | mutoe/vue3-realworld-example-app | TypeScript | Vue 3 + Fetch | https://api.realworld.show/api | cliente frontend para autenticação e artigos | `src/services/api.ts` |
| 7 | Dimillian/MovieSwiftUI | Swift | SwiftUI | The Movie Database API | listar filmes e tráfego TMDb | `MovieSwift/Packages/Backend/Sources/Backend/services/APIService.swift` |
| 8 | bizz84/open_weather_example_flutter | Dart | Flutter + http | OpenWeatherMap API | consultar clima por cidade | `lib/src/api/api.dart` |
| 9 | Shashank02051997/WeatherApp-Android | Kotlin | Android + Retrofit | OpenWeatherMap API | exibir clima em Android | `app/src/main/java/com/shashank/weatherapp/data/network/ApiInterface.kt` |
| 10 | ming1016/SwiftPamphletApp | Swift | SwiftUI + URLSession | GitHub REST API | consultar recursos do GitHub | `SwiftPamphletApp/SharePackage/SMGitHub/Sources/SMGitHub/APIVM/GItHubReq.swift` |

## Análise individual

### 1) RealWorld
- Repositório: https://github.com/gothinkster/realworld
- Linguagem: TypeScript
- Framework: Playwright + contrato de API
- API consumida: https://api.realworld.show/api
- Finalidade: verificar a disponibilidade da API pública do projeto
- Arquivo de integração: `specs/e2e/health.spec.ts`
- Função responsável: `test("API should be accessible")`
- Trecho real de código:
```ts
const response = await request.get('https://api.realworld.show/api/tags');
expect(response.ok()).toBeTruthy();
```
- Explicação técnica: o teste faz uma requisição GET para /api/tags e valida se a API retorna resposta HTTP bem-sucedida.

### 2) NestJS RealWorld
- Repositório: https://github.com/lujakob/nestjs-realworld-example-app
- Linguagem: TypeScript
- Framework: NestJS
- API consumida: RealWorld API
- Finalidade: expor endpoints de autenticação, artigos e comentários
- Arquivo de integração: `src/article/article.controller.ts`
- Função responsável: `findAll`
- Trecho real de código:
```ts
@Get()
async findAll(@Query() query): Promise<ArticlesRO> {
  return await this.articleService.findAll(query);
}
```
- Explicação técnica: o controlador mapeia requisições HTTP para consultas de artigos, usando decorators do NestJS e o serviço interno para recuperar dados.

### 3) Spring Boot RealWorld
- Repositório: https://github.com/gothinkster/spring-boot-realworld-example-app
- Linguagem: Java
- Framework: Spring Boot
- API consumida: RealWorld API
- Finalidade: implementar o backend REST para o projeto RealWorld
- Arquivo de integração: `src/main/java/io/spring/api/ArticlesApi.java`
- Função responsável: `getArticles`
- Trecho real de código:
```java
@GetMapping
public ResponseEntity getArticles(
    @RequestParam(value = "offset", defaultValue = "0") int offset,
    @RequestParam(value = "limit", defaultValue = "20") int limit,
    @AuthenticationPrincipal User user) {
  return ResponseEntity.ok(articleQueryService.findRecentArticles(tag, author, favoritedBy, new Page(offset, limit), user));
}
```
- Explicação técnica: a classe usa `@GetMapping` e parâmetros de query como `tag`, `author` e `favorited`, retornando a lista de artigos em JSON.

### 4) Laravel RealWorld
- Repositório: https://github.com/gothinkster/laravel-realworld-example-app
- Linguagem: PHP
- Framework: Laravel
- API consumida: RealWorld API
- Finalidade: alimentar a API com operações sobre artigos
- Arquivo de integração: `app/Http/Controllers/Api/ArticleController.php`
- Função responsável: `index`
- Trecho real de código:
```php
public function index(ArticleFilter $filter)
{
    $articles = new Paginate(Article::loadRelations()->filter($filter));
    return $this->respondWithPagination($articles);
}
```
- Explicação técnica: o controlador consulta o modelo `Article`, aplica filtros e responde em JSON via `respondWithPagination`, seguindo o padrão da API RealWorld.

### 5) Flask RealWorld
- Repositório: https://github.com/gothinkster/flask-realworld-example-app
- Linguagem: Python
- Framework: Flask
- API consumida: RealWorld API
- Finalidade: atender rotas de leitura e escrita de artigos
- Arquivo de integração: `conduit/articles/views.py`
- Função responsável: `get_articles`
- Trecho real de código:
```python
@blueprint.route('/api/articles', methods=('GET',))
@jwt_optional
@use_kwargs({'tag': fields.Str(), 'author': fields.Str(), 'favorited': fields.Str(), 'limit': fields.Int(), 'offset': fields.Int()})
def get_articles(tag=None, author=None, favorited=None, limit=20, offset=0):
    res = Article.query
    return res.offset(offset).limit(limit).all()
```
- Explicação técnica: a rota usa Flask e marshmallow para receber parâmetros de filtro e devolver uma lista de artigos em formato JSON.

### 6) Vue 3 RealWorld
- Repositório: https://github.com/mutoe/vue3-realworld-example-app
- Linguagem: TypeScript
- Framework: Vue 3 + Fetch
- API consumida: https://api.realworld.show/api
- Finalidade: consumir autenticação, postagens e perfis em um cliente frontend
- Arquivo de integração: `src/services/api.ts`
- Função responsável: `HttpClient`
- Trecho real de código:
```ts
public baseUrl: string = "https://api.realworld.show/api";
private customFetch = (...fetchParams: Parameters<typeof fetch>) =>
  fetch(...fetchParams);
```
- Explicação técnica: a classe define a URL base da API realworld e encapsula chamadas com `fetch`, permitindo fazer operações REST no frontend.

### 7) MovieSwiftUI
- Repositório: https://github.com/Dimillian/MovieSwiftUI
- Linguagem: Swift
- Framework: SwiftUI
- API consumida: The Movie Database API
- Finalidade: listar filmes, detalhes e imagens do catálogo TMDb
- Arquivo de integração: `MovieSwift/Packages/Backend/Sources/Backend/services/APIService.swift`
- Função responsável: `GET`
- Trecho real de código:
```swift
let baseURL = URL(string: "https://api.themoviedb.org/3")!
components.queryItems = [
    URLQueryItem(name: "api_key", value: apiKey),
    URLQueryItem(name: "language", value: Locale.preferredLanguages[0])
]
```
- Explicação técnica: o serviço monta a URL da API do TMDb, insere a chave `api_key` e usa `URLSession` para obter dados em JSON e decodificá-los.

### 8) OpenWeather Flutter
- Repositório: https://github.com/bizz84/open_weather_example_flutter
- Linguagem: Dart
- Framework: Flutter + HTTP
- API consumida: OpenWeatherMap API
- Finalidade: consultar condições climáticas por cidade
- Arquivo de integração: `lib/src/api/api.dart`
- Função responsável: `OpenWeatherMapAPI.weather`
- Trecho real de código:
```dart
Uri weather(String city) => _buildUri(
  endpoint: "weather",
  parametersBuilder: () => cityQueryParameters(city),
);
```
- Explicação técnica: a API é encapsulada em uma classe que gera a URL com base em `api.openweathermap.org`, adicionando parâmetros como cidade e `appid`.

### 9) Weather App Android
- Repositório: https://github.com/Shashank02051997/WeatherApp-Android
- Linguagem: Kotlin
- Framework: Android + Retrofit
- API consumida: OpenWeatherMap API
- Finalidade: mostrar dados climáticos em um app Android
- Arquivo de integração: `app/src/main/java/com/shashank/weatherapp/data/network/ApiInterface.kt`
- Função responsável: `findCityWeatherData`
- Trecho real de código:
```kotlin
@GET("weather")
suspend fun findCityWeatherData(
    @Query("q") q: String,
    @Query("units") units: String = AppConstants.WEATHER_UNIT,
    @Query("appid") appid: String = BuildConfig.weather_api_key
): Response<WeatherDataResponse>
```
- Explicação técnica: a interface `ApiInterface` usa Retrofit para transformar o endpoint `/weather` em uma chamada type-safe, com `q`, `units` e `appid` como parâmetros.

### 10) SwiftPamphletApp
- Repositório: https://github.com/ming1016/SwiftPamphletApp
- Linguagem: Swift
- Framework: SwiftUI + URLSession
- API consumida: GitHub REST API
- Finalidade: obter e exibir dados e recursos do GitHub dentro do app
- Arquivo de integração: `SwiftPamphletApp/SharePackage/SMGitHub/Sources/SMGitHub/APIVM/GItHubReq.swift`
- Função responsável: `GitHubReq.req`
- Trecho real de código:
```swift
var req = URLRequest(url: URL(string: "https://api.github.com/\(path)")!)
req.addValue("token \(githubat)", forHTTPHeaderField: "Authorization")
let (data, response) = try await URLSession.shared.data(for: req)
```
- Explicação técnica: a classe monta requisições autenticadas para a API do GitHub e decodifica as respostas JSON em modelos Swift, permitindo consumir dados do serviço remoto.

## Comparação técnica

- Linguagens: os projetos usam TypeScript, Java, PHP, Python, Dart, Kotlin e Swift, demonstrando ampla variedade de stacks.
- Frameworks: NestJS, Spring Boot, Laravel, Flask, Vue 3, Flutter, Android, SwiftUI e URLSession são abordagens diferentes para a mesma necessidade: consumo de serviços externos.
- Bibliotecas HTTP: `fetch`, `axios`, `Retrofit`, `http`, `URLSession` e `OkHttp` aparecem em diferentes repositórios, o que mostra padrões equivalentes para estruturar clientes HTTP.
- APIs consumidas: o conjunto inclui RealWorld API, OpenWeatherMap, The Movie Database (TMDb) e GitHub REST API.
- Métodos HTTP: predominam GET para leitura de dados; também existem POST, PUT e DELETE em projetos de API backend e frontend.
- Finalidade: os projetos variam entre backend de API, frontend de aplicação, aplicativos mobile e serviços de dados externos.

## Conclusão

A atividade demonstrou como a integração com APIs é central para o desenvolvimento moderno de software. Os projetos analisados usaram diferentes linguagens e frameworks, mas todos seguiram o mesmo padrão: montar a URL, enviar parâmetros, tratar autenticação quando necessário, consumir JSON e transformar a resposta em dados úteis para a interface ou a lógica da aplicação.

Além disso, a análise evidenciou que o uso de APIs não é restrito a um tipo de arquitetura: ela aparece em backends REST, clientes web, apps mobile e serviços de dados públicos. Esse conjunto reforça a importância do estudo de HTTP, serialização de JSON, autenticação, manipulação de respostas e tratamento de erros em qualquer projeto tecnológico.

## Referências

1. https://github.com/gothinkster/realworld
2. https://github.com/lujakob/nestjs-realworld-example-app
3. https://github.com/gothinkster/spring-boot-realworld-example-app
4. https://github.com/gothinkster/laravel-realworld-example-app
5. https://github.com/gothinkster/flask-realworld-example-app
6. https://github.com/mutoe/vue3-realworld-example-app
7. https://github.com/Dimillian/MovieSwiftUI
8. https://github.com/bizz84/open_weather_example_flutter
9. https://github.com/Shashank02051997/WeatherApp-Android
10. https://github.com/ming1016/SwiftPamphletApp
