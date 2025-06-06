# Corrida Multitarefa

Este projeto é um exercício para a aula sobre Multitarefa de disciplinas de Programação Orientada a Objetos.

## Orientações Gerais

- Você deve fazer um passo de cada vez, testá-lo, fazer o commit e enviar suas alterações.
Somente depois disso é que você deve passar para o próximo passo.

- **ATENÇÃO**: **desligue o GitHub Copilot para fazer o exercício!**
  - Se você utilizá-lo você não estará realmente exercitando os conceitos aprendidos e
    não terá o domínio adequado para desenvolver as habilidades necessárias para se tornar
	um bom programador/desenvolvedor.
  - Sem contar ainda a questão do plágio.
  - Lembre-se que você pode (e deve) consultar os materiais da disciplina para fazer o exercício.

- Esse arquivo README pode ser melhor visualizado no VS Code (com formatação adequada) 
  abrindo-o no modo de visualização. Para isso, basta apertar Ctrl+Sfhit+V com ele aberto.

## Passo 0 - Conhecendo a proposta do sistema

Execute o programa e veja a tela que é exibida.

- Repare que há uma caixa de texto que permite escolher a distância da corrida.
- Um botão que permite iniciar um corrida.
- Um botão que permite interromper uma corrida.
- E um botão que permite atualizar as informações da corrida na tela.

Veja que, por enquanto, esses botões na verdade não fazem nada, já que esta versão inicial ainda não trata 
uma corrida propriamente dita.
Para tratá-a teremos que fazer a implementação dos competidores da corrida.

O projeto possui uma interface chamada `Competidor` que define um contrato para um competidor de corridas.
Ele traz também a classe chamada `Tela` que implementa uma interface gráfica que aparece ao executar o progama.

Sua missão nos próximos passos será implementar uma classe de competidores e usar threads para simular a corrida.

## Passo 1 - Classe Corredor

Crie uma classe chamada `Corredor` que implemente a interface `Competidor`.
Cada corredor deve ter um nome, uma velocidade (em metros por segundo), uma distância a ser percorrida (distância da corrida, em metros), a distância já percorrida e um booleano indicando se o corredor está correndo ou não.
O construtor da classe deve receber o nome e a velocidade do corredor, pois a ideia é que tenhamos diferentes corredores com diferentes velocidades.

Além dos métodos de acesso (_gets_), a classe deve sobrescrever também o método `prepararParaNovaCorrida` de modo que o corredor possa participar de diversas corridas.

> Dica: o método `prepararParaNovaCorrida` será chamado sempre que o usuário clicar no botão para iniciar a corrida.
> O usuário poderá clicar nesse botão várias vezes e, em cada uma delas, poderá usar uma distância diferente para a corrida.
> Portanto, a implementação do método deve garantir um estado válido (valores dos atributos) para essa situação na qual o corredor 
> ficará preparado para uma nova corrida, antes de começá-la.

Repare que a interface `Competidor` estende a interface `Runnable` e, portanto, a sua classe deverá implementar tanto os métodos da interface `Competidor` quanto o método `run` da interface `Runnable`.
Por causa deste último método, **sua classe poderá depois ser usada como uma tarefa de uma thread**.

O método `run` deve simular uma única corrida feita pelo corredor.
Para isso, deve ser implementada uma estrutura de repetição que faça o corredor correr de acordo com a sua velocidade.
Para isso, implemente o seguinte algoritmo:

- Sinalize que o corredor agora está correndo.
- Enquando a distância já percorrida for menor que a distância da corrida:
  - O corredor corre mais um metro.
  - E a `thread` atual deve suspensa pelo tempo necessário para que a velocidade do corredor seja simulada.
    > Por exemplo: suponha que a velocidade de um corredor seja de 4 metros por segundo;
    > o que significa que ele corre um metro a cada 1/4 segundo (ou 250 milissegundos).
    > Logo, se ele acabou de correr um métro, a thread deve ser suspensa, neste caso, por 250 ms.
- E, por fim, sinalize que o corredor agora parou de correr.
  - Obs.: é necessário garantir que essa sinalização sempre ocorra independentemente do loop ter sido executado até o final ou de
    ter ocorrido alguma exceção na execução da thread.
  - Isso é importante para que o botão _Interromper Corrida_ funcione corretamente, já que a classe `Tela` chama o método ` interrupt`
    para a thread de cada corredor quando o usuário clica no botão, e isso gera uma exceção do tipo `InterruptedException`.

Dica:
- Em Java, a divisão de dois números inteiros retorna um número inteiro.
- Para que a divisão retorne um número decimal você pode usar algum valor não inteiro na divisão (como `1.0` em vez de `1`, por exemplo).

## Passo 2 - Criar competidores a partir de um arquivo

Tendo a classe `Corredor`, podemos agora criar os competidores da nossa corrida.

Para isso, vamos alterar o método `criarCompetidores` da classe `Tela`, acrescentando objetos da classe `Corredor` à lista de competidores.
Os objetos a serem criados devem vir de um arquivo texto.
Para tratar isso, faça o seguinte:

- Crie uma classe para a leitura dos dados dos corredores do arquivo.
- A classe deve ter um método estático que leia os dados do arquivo `corredores.txt` que está dentro da pasta `dados`.
- O método deve criar os corredores de acordo com os dados do arquivo.
  > Dica 1: O método `split` da classe String permite _quebrar_ uma String em várias usando um caractere como separador.
  >         Mas como `|` é um caractere especial, para utilizá-lo  como separador, é necessário usar `split("\\|")`.
  > Dica 2: O método `trim` da classe String retorna uma nova String sem os espaços do começo e do final da String. 
- E retornar a lista de corredores.

Por fim, use o método da classe criada acima dentro do método `criarCompetidores` da classe `Tela` para acrescentar os corredores
criados à lista de competidores do sistema.

Execute o programa.
Os corredores que presentes no arquivo devem agora aparecer na tela do programa.

## Passo 3 - Criar a thread de cada competidor

Para simular a corrida, nós vamos criar uma thread para cada competidor.
Como o método `run` da classe `Corredor` simula a velocidade de corrida de um corredor, se iniciarmos as threads ao mesmo tempo, conseguiremos simular a corrida de forma adequada (com todos correndo ao mesmo tempo).

Para fazer isso, altere o método `iniciarCorrida` da classe `Tela` para que cada corredor seja usado como uma tarefa de uma thread separada. Basicamente, você precisa percorrer a lista de competidores e, para cada um:
- prepará-lo para uma nova corrida (chamando o método `prepararParaNovaCorrida`), 
- e acrescentar uma thread para o competidor na lista de threads já existente.

Depois é necessário fazer um loop iniciando todas as threads criadas.

Execute o programa e teste sua implementação.
Note que, nesta versão, após iniciar a corrida, você precisa ficar clicando no botão `Atualizar` para conseguir ver o status atualizado da corrida.

## Passo 4 - Visualização automática da corrida

Usar o botão `Atualizar` não é nada prático, certo?
Vamos então criar agora mais uma thread e usá-la para que a atualização da corrida seja feita de forma automática.

Para tratar essa thread, vamos utilizar um conceito novo chamado **classe anônima**.
Uma classe anônima é atalho sintático fornecido pela linguagem, que nos permite de maneira bem prática:

- Criar uma classe que implementa uma interface.
- Instanciar um objeto dessa classe.
- E passar esse objeto como parâmetro para algum método ou construtor.

Nós sabemos que, para usar uma thread, precisamos criar um objeto da classe `Thread` que recebe como
parâmetro um objeto que é a tarefa da thread.

```java
Thread minhaThread = new Thread(tarefa);
```

Nós sabemos também que a variável `tarefa` precisa referenciar um objeto de uma classe que implementa a
interface `Runnable`.
Foi isso que acabamos de fazer para tratar as threads dos corredores.

O ponto aqui é que, com o conceito de classes anônimas, nós podemos definir a classe da tarefa,
instanciar um objeto dela e já passá-lo como parâmetro para o construtor da thread, tudo de uma vez.
O trecho de código abaixo traz um exemplo disso. 

- Veja que parece que estamos passando um código como parâmetro para o construtor da thread.

```java
Thread minhaThread = new Thread(
    new Runnable() {
        public void run() {
            // código a ser executado pela thread
        }                    
    }
);
```

O código é ainda mais maluco pois parece estar instanciando uma interface (`new Runnable()`) o que
sabemos que não é possível.

- Mas, na verdade, esse código define uma classe sem nome (_anônima_) que herda da interface `Runnable`,
  e já instancia um objeto dessa classe.
- Além disso, o código da classe sem nome já é fornecido (a implementação do método `run`).

Veja que, com isso, conseguimos já implementar o código que a thread vai executar junto com a criação do
objeto da thread.

- Repare que só faz sentido usar essa estratégia se o código implementado for utilizado apenas para essa thread.

Depois de todo esse contexto, vamos, enfim, tratar a atualização automática da corrida.
Para isso, altere o método `iniciarCorrida` da classe `Tela`:

1. Após o início das threads dos corredores, crie uma nova thread, passando como parâmetro um objeto de uma classe anônima que implemente a interface `Runnable`.
3. No método `run` da classe anônima, trate para que, enquanto ainda houver corredores correndo, a corrida seja atualizada e a thread suspensa por 100 milissegundos.
  - Obs.: para isso, crie um método na classe `Tela` que retorne se ainda existe algum corredor correndo.
4. Não se esqueça de iniciar a thread criada.

Execute o programa e teste sua implementação.
