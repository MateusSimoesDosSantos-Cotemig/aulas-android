# AlertDialog

Em alguns casos o desenvolvedor de um app, precisa de interagir com o usuario de forma que chame a atenção deste usuario para algo que seja importante.

Um jeito bem útil de chamar a atenção do usúario é por meio de um AlertDialog(caixa de dialogo). Um AlertDialog, é uma caixa de mensagem que sobrepoe a interface do app, e geralmente contem alguma informação, ou requer que o usuario faça alguma intereção por parte dele. Exemplos de AlertDialog podem ser, ao usuario tentar exceluir uma mensagem em um de conversa, confirmar se ele realmente quer fazer aquilo ou se foi uma ação acidental.

![](./images/imagesdialog.png)

O código abaixo mostra como exibir um AlertDialog em uma Activity.


```kotlin

AlertDialog.Builder(this)
            .setTitle("olá!")
            .setMessage("Tudo bem?")
            .setPositiveButton("Sim", ::cliquePositivo)
            .setNegativeButton("Não", ::cliqueNegativo)
            .show()
    }
```
As funções cliquePositivo e setNegativeButton, são passadas como referencia, e são implementadas também na Activity.

```kotlin

fun cliquePositivo(dialog: DialogInterface, p1: Int) {
        Toast.makeText(this, "Clicou em Sim", Toast.LENGTH_SHORT).show()
    }

    fun cliqueNegativo(dialog: DialogInterface, p1: Int) {
        Toast.makeText(this, "Clicou em Não", Toast.LENGTH_SHORT).show()
    }
```