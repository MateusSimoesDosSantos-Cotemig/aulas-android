# Fragmentos

Fragmentos são partes partes de uma tela e pode se usado para compor o  layout de uma `Activity`. Um fragmento depende de uma Activity para existir, sendo assim sempre que for necessário usar um fragmento, precisa antes declarar uma `Activity`, a activity se comporta como hospedeira para o fragmento.

  

Um fragmento tem seu próprio ciclo de vida, interface ou layout e variáveis.

Ao adicionar um fragmento a uma `Activity`, esse fragmento é exibido como se fosse um componente de *layout* da `Activity`.

  

## Criação de um Fragmento

Um fragmento pode ser criado por meio da criação de uma classe que estende ou é subclasse da classe base `Fragment`.

  

O ciclo de vida de um fragmento é constituído pelos métodos:

  

`onCreate`: Usado para inicializar os componentes, variáveis e recursos necessários para um Fragmento.

  

`onCreateView`: É usado para retornar o layout de exibição do seu fragmento

  

`onViewCreated`: indica que o layout já foi criado e então é possível interagir com os elementos de layout, por exemplo fazendo referencias aos componentes do layout xml.

  
  

`onPause`: Indica que o usuário está saindo do Fragmento.

  

`onResume`: Indica que o usuário voltou ao fragmento.

  

`onDestroy`: Indica que fragmento já não existe na hierarquia de exibição da Activity.

  

### Exemplo de criação de um fragmento:

  

    class FragmentoExemplo() : Fragment() {
    
	    override fun onCreateView(
		    inflater: LayoutInflater,
		    container: ViewGroup?,
		    savedInstanceState: Bundle?
	    ): View {
		    // Inflate the layout for this fragment
		    return inflater.inflate(R.layout.exemplo_fragmento, container, false)
	    }
    
    }

  

Também poderia ser feito por meio do código abaixo:

  

    class FragmentoExemplo: Fragment(R.layout.exemplo_fragmento) { ... }

  

Para exibir um fragmento em uma Activity, você deve usar o componente **FragmentContainerView**.

  

Veja o layout da Activity como fica:

  

  

    <?xml version="1.0" encoding="utf-8"?>
    <androidx.constraintlayout.widget.ConstraintLayout
	    xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:app="http://schemas.android.com/apk/res-auto"
	    xmlns:tools="http://schemas.android.com/tools"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    tools:context=".MainActivity">
    
	    <androidx.fragment.app.FragmentContainerView
		    android:id="@+id/fragmentContainerView"
		    android:layout_width="0dp"
		    android:layout_height="0dp"
		    app:layout_constraintLeft_toLeftOf="parent"
		    app:layout_constraintRight_toRightOf="parent"
		    app:layout_constraintTop_toTopOf="parent"
		    app:layout_constraintBottom_toBottomOf="parent"
		    android:name=".FragmentoExemplo"
		    tools:layout="@layout/exemplo_fragmento" />
    
    </androidx.constraintlayout.widget.ConstraintLayout>

  

Note que o componente FragmentContainerView, tem o atributo "android:name" que indica qual fragmento será exibido. Esse atributo corresponde ao caminho da sua classe Fragment, criada. Também tem o atributo "tools:layout", este é opcional, e permite ter um preview de como será renderizado o layout do fragmento na tela.

  

Para utilizar o componente FragmentContainerView é obrigatório que seja declarado um id para ele.

  
  

### Interagindo com componente visuais dentro do fragmento

  

Você pode utilizar o metodo onViewCreated, que faz parte do ciclo de vida do fragmento para interagir com componentes do layout xml. No exemplo abaixo o botão "algumBotao" do tipo Button, é declarado no layout do arquivo exemplo_fragmento.xml, e é possível acessado por meio do findViewById passando o id declarado para o elemento no xml. Note que é usado o parâmetro view do método onViewCreated para poder fazer uso da função findViewById dentro do fragmento, o que difere da Activity onde pode chamá-lo diretamente.

  

    class FragmentoExemplo: Fragment(R.layout.exemplo_fragmento) {
	    ...
	    override fun onViewCreated(view: View, savedBundleState: Bundle?) {
		    val algumBotao: Button = view.findViewById(R.id.algum_botao)
		    algumBotao.setOnClickListener { ... }
	    }
    }

  

## Fragmentos deslizantes

Você pode exibir mais de um fragmento como se fosse um carrosel dentro da sua Activity, permitindo o usuário deslizar com o dedo para acessar o fragmento adjacente, comportamento semelhante ao RecyclerView.

Para isso é necessário adicionar no layout da sua Activiy o componente ViewPager2.

Esse componente vai ser responsável por exibir os fragmentos e manter o comportamento de deslize.

  

Veja o layout da sua Activity como deve ser:

  

    <?xml version="1.0" encoding="utf-8"?>
    <androidx.constraintlayout.widget.ConstraintLayout
	    xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:app="http://schemas.android.com/apk/res-auto"
	    xmlns:tools="http://schemas.android.com/tools"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    tools:context=".MainActivity">
    
    
	    <androidx.viewpager2.widget.ViewPager2
		    android:id="@+id/viewPager"
		    android:layout_width="0dp"
		    android:layout_height="0dp"
		    app:layout_constraintLeft_toLeftOf="parent"
		    app:layout_constraintRight_toRightOf="parent"
		    app:layout_constraintTop_toTopOf="parent"
		    app:layout_constraintBottom_toBottomOf="parent" />
	    
    </androidx.constraintlayout.widget.ConstraintLayout>

  

O controle dos fragmentos, assim como a ordem a serem exibidos é gerenciado por um adapter que deve ser criado.

  

Esse adapter deve ser criado como uma classe normal, e deve estender da classe base `FragmentStateAdapter`.

  

A classe FragmentStateAdapter é abstrata ou seja você deve implementar alguns de seus métodos para poder estender dela, no caso seriam os métodos, getItemCount, que deve retornar quantos fragmentos vai ter no seu carrossel de fragmentos. E também o método *createFragment*, que deve retornar qual fragmento para determinada posição. Veja o código abaixo:

  

    // Deve receber no construtor do adapter, uma activity,
    // que também deve ser passada para o construtor da classe base
    class MeuCarroselAdapter(activity: AppCompatActivity) : FragmentStateAdapter(activity) {
    
	    // Indica a quantidade de fragmentos na tela
	    override getItemCount() : Int {
		    return 3
	    }
	    
	    // O when funciona como o switch case das lingaguens convencionais
	    // para o parametro position, que pode ser um inteiro qualquer
	    // é feito um mapa indicando para aquela posição qual fragmento é equivalente
	    // e então é retornado esse fragmento.
	    override createFragment(position: Int) {
		    return when(position) {
			    1 -> FragmentoA()
			    2 -> FragmentoB()
			    else -> FragmentoC()
		    }
	    }
    }

Por fim, no código da sua activity, deve setar o parâmetro adapter do ViewPager2.

  

    class MinhaActivity: AppCompatActivity() {
    
	    ...
	    
	    override fun onCreate(savedBundleState: Bundle?) {
		    setContentView(R.layout.minha_activity)
		    
		    val viewPager: ViewPager2 = findViewById(R.id.viewPager)
		    viewPager.adapter = MeuCarroselAdapter(this@MinhaActivity)
	    }
    }

  

## Bottom Navigation

Um recurso interessante quando está sendo utilizado fragmentos, é a navegação entre fragmentos por meio de menu inferior.

  

Para isso utilizamos um componente chamado BottomNavigationView, ao qual permite criar alguns botões no inferior da Activity, esses botões são utilizados para trocar entre fragmentos, conforme a imagem abaixo:

  
  

Para começar, vamos atualizar o layout da nossa activity, adicionando o componente `BottomNavigationView`, conforme o código abaixo:

  

    <?xml version="1.0" encoding="utf-8"?>
	    <androidx.constraintlayout.widget.ConstraintLayout
	    xmlns:android="http://schemas.android.com/apk/res/android"
	    xmlns:app="http://schemas.android.com/apk/res-auto"
	    xmlns:tools="http://schemas.android.com/tools"
	    android:layout_width="match_parent"
	    android:layout_height="match_parent"
	    tools:context=".MainActivity">
    
    
	    <androidx.fragment.app.FragmentContainerView
		    android:id="@+id/fragmentContainerView"
		    android:layout_width="0dp"
		    android:layout_height="0dp"
		    app:layout_constraintLeft_toLeftOf="parent"
		    app:layout_constraintRight_toRightOf="parent"
		    app:layout_constraintTop_toBottomOf="@id/bottomNavigationView"
		    app:layout_constraintBottom_toBottomOf="parent"
		    android:name=".FragmentoExemplo"
		    tools:layout="@layout/exemplo_fragmento" />
		    
	    <com.google.android.material.bottomnavigation.BottomNavigationView
		    android:id="@+id/bottomNavigationView"
		    android:layout_width="0dp"
		    android:layout_height="wrap_content"
		    android:layout_gravity="start"
		    app:layout_constraintLeft_toLeftOf="parent"
		    app:layout_constraintRight_toRightOf="parent"
		    app:layout_constraintBottom_toBottomOf="parent"
		    app:menu="@menu/menu_itens_navegacao" />
   
    </androidx.constraintlayout.widget.ConstraintLayout>

  
  

Observe o atributo *"app:menu"*, este atributo recebe uma referencia a um recurso de menu. Para criar esse recurso, você deve clicar com o botão direito sobre o *"res*" na estrutura de arquivos do projeto, *new -> Android Resources file*, e preencha a caixa de dialogo com o nome do menu desejado, é importante que o tipo do resource seja especificado como Menu, conforme a imagem abaixo:

O conteúdo do menu, é composto por itens, esses itens recebem id, titulo(title) e ícone(icon), conforme o código abaixo:

     <menu xmlns:android="http://schemas.android.com/apk/res/android">
         <item android:id="@+id/action_search"
              android:title="@string/menu_search"
              android:icon="@drawable/ic_search" />
         <item android:id="@+id/action_settings"
              android:title="@string/menu_settings"
              android:icon="@drawable/ic_add" />
         <item android:id="@+id/action_navigation"
              android:title="@string/menu_navigation"
              android:icon="@drawable/ic_action_navigation_menu" />
     </menu>

Por fim é possível capturar os eventos de clique em uma `BottomNavigationView`, e fazer a substituição do fragmento de acordo com o item clicado. O código abaixo mostra como fazer isso em uma Activity.

        class MinhaActivity: AppCompatActivity() {
        
        ...
        
        override fun onCreate(savedBundleState: Bundle?) {
        
        setContentView(R.layout.minha_activity)
        
        val bottomNavigation: BottomNavigationView = findViewById(R.id.bottomNavigationView)
        
        bottomNavigation.setOnNavigationItemSelectedListener { item ->
    		// Para identficar qual item foi clicado, é então verificado por meio do itemId,
    		// que é o mesmo declarado no arquivo de menu criado.
    		when(item.itemId) {
    			R.id.action_search -> {
    			
    			// Nós utilizamos a propriedade da activity, chamada supportFragmentManager,
    			// por meio da fução beginTransaction dincamos que será feita alguma operação envolvendo fragmentos
    			// a função replace, deve receber o id do FragmentContainerView que está exibindo os fragmentos, e o fragmento novo para a substituição
    			// por fim o commit indica para a operação ser aplicada.
    				supportFragmentManager
	    				.beginTransaction()
	    				.replace(R.id.fragmentContainerView, FragmentoSearch())
	    				.commit()
			    }
			    R.id.action_settings -> {
					 supportFragmentManager.beginTransaction.replace(R.layout.fragmentContainerView, FragmentoSettings()).commit()
			    }  
			    R.id.action_navigation -> {
					 supportFragmentManager.beginTransaction.replace(R.layout.fragmentContainerView, FragmentoNavegacao()).commit()
			    }
    
		    // por fim retorne true, para que o componente saiba que você tratou o evento de clique.
		    true
	    }		
    }


