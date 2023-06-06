
# Conhecendo o Manifest!

  

O manifest é um arquivo necessário a sua aplicação, onde informa quais recursos são utilizados no aplicativo.

  

Sua estrutura em xml, permite de maneira declarativa configurar sua aplicação por meio de atributos e tags xml.

  

No Manifest é possível declarar as activities que sua aplicação irá executar, permissões necessárias para sua aplicação como câmera, localização, internet e outras. Além de muitas outras funcionalidades como tema da aplicação, configurações de hardware necessárias, serviços e Broadcast Receivers. Por hora vamos nós concentrar em declarar as activities do seu app.

# Declarando uma Activity no Manifest

Abaixo um exemplo retirado do site do Google, [Developers Android](https://developer.android.com/guide/components/activities/intro-activities).

  

```xml
<manifest ... >

  <application ... >

    <activity android:name=".ExampleActivity" />

    ...

    </application ... >

  ...

</manifest >
```
  

Como é possível ver, foi declarada uma nova tag de xml, do tipo **activity**, onde por meio do atributo "android:name", é informado o caminho relativo da sua Activity no app, no caso ".ExampleActivity".

  

## Definindo sua Activity principal, com Intent Filter

  

A Activity principal, é a Activity que será inicializada no inicio da sua aplicação. Para isso é feita no Manifest a declaração de um Intent-Filter que permite dizer para o sistema Android, que ela será a Activity principal da sua aplicação, veja o código abaixo:

  

  
```xml
<manifest ... >

    <application ... >

        <activity android:name=".ExampleActivity" >

            <intent-filter>

                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />

            </intent-filter>
            ...

        </activity>
    ...

    </application ... >
    ...

</manifest >
```

Note que dentro do corpo da Activity, ExampleActivity, foi incluído um novo objeto chamado intent-filter, esse objeto permite filtrar solicitações do sistema. Dentro do intent filter foram incluídas duas novas tags, a tag action cujo valor é "android.intent.action.MAIN" e uma categoria cujo valor é "android.intent.category.LAUNCHER". Não vamos entrar a fundo no momento, mas o que você precisa saber por enquanto é que caso você queira que sua activity seja a principal do projeto, você deve fazer esses ajustes no manifesto da sua aplicação.

  

## Navegação entre activities

  

O Android ele utiliza um como meio de comunicação com a sua aplicação, um sistema de intenções (Intent), por meio desse sistema é possível a comunicação entre activities da sua aplicação, e até mesmo comunicar se com aplicações fora do seu app.

  

O código abaixo exibe como partir de uma Activity A, para uma Activity B. Entenda que esta função foi declarada na própria Activity A, e ela utiliza uma função base chamada startActivity(), a qual recebe uma intenção(intent), essa intent recebe dois parâmetros, contexto, no caso foi passado a keyword **this**, que significa a própria Activity A, e passa um segundo parâmetro no construtor da intent, que é o tipo da classe que será chamada, ou navegada no momento da execução do código, para passar o tipo da classe foi acrescentado ao nome da classe **::class.java**

  
  

```kotlin

fun abrirActivityB() {
    startActivity(Intent(this, ActivityB::class.java))
}

```

## Passagem de parâmetro entre activities

Em alguns casos são necessários passar parâmetros de uma tela para outra tela em sua aplicação. Pense em um formulário de cadastro, geralmente iniciamos com nossos dados pessoais em seguida somos direcionados para um tela de cadastro de endereço por exemplo.

  

Então para dar continuidade ao seu fluxo a passagem de parâmetros é uma parte importante na implementação do seu aplicativo. Essa passagem de parâmetros é feita por meio da intenção(Intent) enviada.

  

O código abaixo mostra o exemplo do envio de dois parâmetros uma string *nome*, e um Int *idade*.

  

```kotlin
fun abrirActivityB() {
    startActivity(
      Intent(this, ActivityB::class.java)
        .putExtra("nome", "mateus")
        .putExtra("idade", 27)
    )
}
```

  

Agora esse código é escrito dentro do método onCreate da Activity B, ele recupera os valores passados pela Acitvity A.

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    ...

    val nome: String? = intent.getStringExtra("nome") // "mateus"

    val idade: Int? = intent.getIntExtra("idade") // 27
}
```

  

### Retornando valor para a Activity anterior

Também pode ser que sua aplicação ao chamar uma tela, espera um retorno dessa tela. Exemplo o usuário está em um aplicativo de loja, e na tela principal possui um carrinho de compras e uma lista de produtos que o usuário pode tocar e navegar para a tela de detalhes do produto. Ao clicar em comprar o produto na tela de detalhes do produto, então retorna para a tela principal do app e o aplicativo atualiza o carrinho de compras.

  

Problema: como a tela principal vai atualizar o carrinho sem saber se o usuário selecionou o produto na tela de detalhes?

Solução: a tela de detalhes retorna que foi selecionado um produto para a tela principal, agora ela pode atualizar o carrinho de compras do usuário.

  

O código descrito abaixo mostra como sua Activity recebe esse valor.

  ```kotlin
// Declare uma variavel global que basicamente é um callback do retorno para sua activity

val startForResult = registerForActivityResult(StartActivityForResult()) { result: ActivityResult ->
    if (result.resultCode == Activity.RESULT_OK) {

      val intent = result.data
    }
}
```


Como pode ver o método registerForActivityResult, permite a passagem de uma função callback que recebe como parâmetro o "result", esse result é do tipo ActivityResult. Este objeto te retorna um parâmetro que é o **resultCode**, um inteiro que sua tela B poderia retornar para tela A, como indicativo se deu certo ou não alguma requisição da sua aplicação. Além do resultCode também tem o objeto **data**, que é do tipo Intent, sendo assim é possível recuperar valores por meio de chaves como já foi mostrado anteriormente no exemplo de passagem de parâmetros.

  


  

Agora a função abrir Activity B, muda um pouco, já não é mais startActivity, o código abaixo mostra como é feita a chamada do método.

  

```kotlin
fun abrirActivityB() {
    startForResult.launch(Intent(this, ActivityB::class.java))
}
```

  

Quando a Activity B se encerrar ela, pode então retornar algum valor para sua Activity A, e então será executado o callback que tinha sido declarado anteriormente como variável global da classe.

  

O código abaixo é escrito na Activity B, ele mostra como é feito esse retorno.

  

```kotlin
fun fechaActivityERetornaValores() {
    setResult(RESULT_CODE, Intent().putExtra("name", "mateus"))
    finish() // este metodo encerra uma Activity
}
```
