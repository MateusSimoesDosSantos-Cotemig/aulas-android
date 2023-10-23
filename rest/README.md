# Consumindo API Rest com Retrofit

Uma API Rest é uma interface web que usa o protocolo HTTP para permitir a comunicação entre aplicações locais e remotas. Por exemplo, quando você faz login em um aplicativo no seu celular, você pode enviar seus dados para um servidor na nuvem que verifica se você é um usuário válido e libera o seu acesso. Da mesma forma, quando você cria ou altera algum conteúdo no aplicativo, você pode sincronizar essas informações com o servidor, que tem mais espaço e velocidade para guardar e processar os dados. Além disso, o servidor pode estar sempre online e compartilhar os dados com outros usuários ou serviços na web. Assim, uma API Rest facilita a integração e a troca de informações entre diferentes aplicações e serviços na internet.

O Retrofit é a biblioteca mais usada para consumir uma API Rest no Android. O Retrofit tem como objetivo:

 - Simplificar o consumo de uma API Rest, escondendo toda a parte complicada do código.
 - Fazer o consumo de uma API Rest em segundo plano.

## Configuração
Antes de consumir uma API Rest, é necessário configurar o seu ambiente.

### Adicionar permissão de acesso a internet.
Para adicionar a permissão, abra o arquivo AndroidManifest dentro da pasta `app > manifests` do seu projeto e insira o seguinte código acima da tag application:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

### Incluir dependência do Retrofit
Para usar o Retrofit, é necessário incluir as dependências da biblioteca no projeto. Abra o arquivo `build.gradle` do módulo app e insira o código abaixo em `dependencies`:

```groovy
dependencies {
  def retrofit_version = "2.9.0"
  
  implementation "com.squareup.retrofit2:retrofit:$retrofit_version" 
  implementation "com.squareup.retrofit2:converter-gson:$retrofit_version"
}
```





## Componentes do Retrofit

Para consumir uma API Rest usando Retrofit, é necessário criar uma classe que terá a instância do Retrofit e uma interface com as especificações da API Rest acessada.

Aqui está um exemplo de implementação para criar uma aplicação de busca de endereço por CEP:

### Criando instancia do Retrofit

Para começar, crie uma pasta em seu projeto chamada `dadosremotos`. Dentro desta pasta, crie um arquivo chamado `ApiService.kt`.

Primeiramente, você deve definir a URL base da sua API. A URL base representa o endereço principal do recurso que você deseja acessar. É semelhante à URL exibida no navegador quando você visita um site. Essa URL é única para cada recurso, por exemplo, o site do YouTube tem várias URLs específicas para diferentes vídeos, mas o início da URL é sempre www.youtube.com, que é a URL base do YouTube.

No nosso caso, a URL base da nossa aplicação é https://viacep.com.br/. Portanto, adicione a seguinte constante ao arquivo `ApiService.kt`:


```kotlin
private const val URL_BASE = "https://viacep.com.br/"
```
A seguir, você deve configurar o Builder do Retrofit. Esse Builder fornece a instância da classe Retrofit necessária para realizar chamadas à API Rest. Você pode fazer isso da seguinte maneira:

```kotlin
private val retrofit = Retrofit.Builder()  
    .addConverterFactory(GsonConverterFactory.create())  
    .baseUrl(URL_BASE)  
    .build()
```

Por fim, vamos criar uma nova interface dentro da pasta "dadosremotos" chamada "ViaCepAPIService". Essa interface conterá as definições das chamadas à API Rest. As definições são estabelecidas por meio de métodos da interface e anotações específicas da biblioteca do Retrofit:

```kotlin
interface ViaCepAPIService {
}
```














É comum que as APIs Rest recebam e enviem dados no formato JSON, uma estrutura amplamente utilizada para modelar dados.
Veja o exemplo de retorno da nossa api ViaCep abaixo:
```json
{
  "cep": "01001-000",
  "logradouro": "Praça da Sé",
  "complemento": "lado ímpar",
  "bairro": "Sé",
  "localidade": "São Paulo",
  "uf": "SP",
  "ibge": "3550308",
  "gia": "1004",
  "ddd": "11",
  "siafi": "7107"
}
```
Neste código JSON, observe que o formato é delimitado por chaves `{ }`, e cada chave define um objeto no formato JSON. No lado esquerdo de cada chave, temos o nome da propriedade do objeto, e após os dois pontos `:`, temos o valor correspondente a essa propriedade. Os valores podem ser strings ou textos, delimitados por aspas duplas `"`, números, arrays delimitados por colchetes `[]`, e até mesmo outros objetos.

Agora, vamos criar uma classe para representar esse modelo de endereço retornado pela nossa API. Na pasta `dadosremotos`, criaremos uma nova classe chamada Endereco. Essa classe será a representação em Kotlin do objeto JSON retornado pela nossa API. Uma das características interessantes do JSON é que você não precisa adicionar todas as propriedades do JSON à sua classe Kotlin, apenas as que você pretende utilizar. Veja o código da classe `Endereco` abaixo:

```kotlin
class Endereco(  
     var logradouro: String,  
	 var complemento: String?,  
	 var bairro: String,  
	 var localidade: String,  
	 var uf: String  
)
```

> Observe que a propriedade complemento possui o tipo `String?`. Isso ocorre porque essa propriedade pode não existir em todos os endereços, por isso é definida como opcional.

Agora com o nosso objeto definido basta voltarmos para a nossa interface `ViaCepApiService` e definir a chamada de busca desse recurso.

Agora que temos nossa classe definida, podemos voltar para nossa interface `ViaCepApiService` e definir a chamada para buscar esse recurso. A chamada para nossa API que retorna o endereço via CEP é feita pela seguinte URL, onde `cep` é o número de CEP do endereço buscado:

    https://viacep.com.br/ws/cep/json

Aqui está o método definido em nossa interface `ViaCepApiService`:

```kotlin
@GET("ws/{cep}/json")  
fun getEndereco(@Path("cep") cep: String): Call<Endereco>
```

Este método, chamado `getEndereco`, recebe um único parâmetro chamado `cep` e retorna um objeto `Endereco` correspondente ao CEP buscado. Esse `Endereco` é encapsulado no tipo `Call` do `Retrofit`.

Observe a anotação `@GET`, que indica que nossa aplicação espera receber dados, no caso, o endereço do CEP informado. Dentro da anotação GET, temos apenas uma parte da URL de requisição de endereço:

    /ws/{cep}/json
Isso ocorre porque a parte inicial da URL já foi definida na etapa de criação da instância do `Retrofit`, onde definimos a constante `URL_BASE`. As chaves `{}` na palavra "cep" indicam que esse valor é dinâmico e será substituído pelo valor passado na chamada do método `getEndereco`. Para que isso aconteça, a variável `cep` no método `getEndereco` deve ter a anotação `@Path`, que especifica qual parte da URL será substituída.

Por fim, retornamos ao arquivo `ApiService.kt` e adicionamos o código responsável por fornecer a instância de nossa `ViaCepApiService`:
```kotlin
val retrofitService : ViaCepAPIService by lazy {  
  retrofit.create(ViaCepAPIService::class.java)  
}
```

## Executando a função de buscar enderço na MainActivity

Dentro do método `onCreate`, fazemos referência ao objeto `retrofitService` e executamos o método getEndereco de nossa `ViaCepApiService`. A chamada é realizada pelo código a seguir:

```kotlin
retrofitService.getEndereco("31748402").enqueue(object : Callback<Endereco> {  
    override fun onResponse(call: Call<Endereco>, response: Response<Endereco>) {  
        if(response.isSuccessful) {  
            var endereco: Endereco = response.body()!!  
            Toast.makeText(this@MainActivity, endereco.localidade, Toast.LENGTH_SHORT).show()  
  
        } else {  
            Toast.makeText(this@MainActivity, "erro", Toast.LENGTH_SHORT).show()  
        }  
    }  
  
    override fun onFailure(call: Call<Endereco>, t: Throwable) {  
        Toast.makeText(this@MainActivity, "erro", Toast.LENGTH_SHORT).show()  
    }  
})
```
Foi utilizada uma função anônima que é passada como parâmetro para o método `enqueue`. Esta função anônima possui dois métodos, `onResponse` e `onFailure`. O `onResponse` é chamado quando o `Retrofit` consegue fazer a requisição à API. O onFailure é acionado quando a requisição não é bem-sucedida.

O `response.body()` retorna o objeto JSON convertido em uma classe Kotlin do tipo `Endereco`. Certifique-se de que sua chamada à API seja tratada de acordo com os resultados esperados.