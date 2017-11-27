# Golang: Goroutines e Channels

## Introdução

Olá galera, estive um pouco ocupado esses tempos por isso demorei a escrever um novo post para a **CodeShare**.
Hoje venho trazer um assunto novo (que ainda não foi mostrado aqui na **CodeShare**), mas que já venho estudando e
trabalhando em alguns projetos: **Golang!!!**

Não importa qual linguagem você utiliza, mas provavelmente você em algum momento teve a necessidade de trabalhar com
programação concorrente. Caso tenha passado por isso você deve saber que fazer isso pode ser chato e muitas vezes o código
se torna complexo e não muito atrativo.

A linguagem **Go** não é algo tão novo, ela foi lançada em **2009**, porém apenas ano passado passei a dar uma atenção maior
a ela e começar a estudar com mais seriedade. Cada dia que se passa gosto mais de trabalhar com ela, gosto da sintaxe, dos paradigmas
da linguagem, da simplicidade, etc... Poderia falar **N** motivos aqui, mas não vou gastar o meu e o seu tempo com isso. Irei mostrar
como é simples trabalhar com programação concorrente utilizando **Go** e duas features fantásticas usadas para isso: `Goroutines` e `Channels`, então vamos lá!

## Goroutines

A definição de `Goroutine` é bem simples: `Goroutine` é uma função que é capaz de ser executada simultaneamente com outras funções, ou seja, de forma concorrente.

Primeiro vamos ver como criamos e como é a execução de uma função normal em **Go**:

```go
// main.go
package main

import "fmt"

func plus(a int, b int) int {
  return a + b
}

func main() {
  fmt.Println("1+2=", plus(1, 2))
  fmt.Println("2+5=", plus(2, 5))
  fmt.Println("4+9=", plus(4, 9))
}
```

No arquivo acima criamos a função `plus` que recebe dois parâmetros e retorna a soma dos mesmos, a execução dessa função ocorre normalmente, pois não invocamos ela como uma `Goroutine`, dessa maneira os nossos resultados sempre serão apresentados na mesma ordem, pois ao chamar a função `plus` ele espera a execução completa da função para depois continuar a execução da próxima instrução. Porém com algumas alterações podemos fazer esse código trabalhar de forma concorrente. Vamos alterar nosso arquivo deixando ele da seguinte maneira:

```go
// main.go
package main

import "fmt"

func plus(a, b int) {
	fmt.Println(a + b)
}

func main() {
	go plus(1, 2)
	go plus(3, 4)
	go plus(5, 6)

	var input string
	fmt.Scanln(&input)
}
```

Olhando o código acima você irá notar algumas diferenças com nosso código anterior. Ao chamarmos a nossa função `plus` estamos utilizando a palavra reservada `go`, dessa maneira estamos dizendo que queremos executar a função `plus` como uma `Goroutine`, ou seja, ela agora será executada de forma concorrente. Quando for invocada, ela não vai esperar que a execução da função `plus` termine para iniciar a execução da instrução seguinte.

Ao executar esse arquivo **N** vezes, podemos obter os resultados em ordem diferente, pois as `Goroutines` podem terminar sua execução em tempos diferentes.

Outro detalhe no código são as duas últimas linhas, usamos essa instrução `fmt.Scanln(&input)` para **parar** a execução do programa, fazendo com que ele espere um **input** do usuário. Isso é feito pois como as `Goroutines` trabalham de forma concorrente, ao tirar isso o programa poderia terminar sua execução antes mesmo de vermos os resultados serem escritos no console.

E é isso, bem simples criar e trabalhar com `Goroutines` não é mesmo? Mas você pode se perguntar: e caso eu queira que duas `Goroutines` comuniquem entre si, como posso garantir a integridade do meu código sendo que elas trabalham de forma concorrente? Sem problemas, é para isso que usamos os `channels`, vamos ver isso agora!

## Channels

`Channels` em **Go** são utilizados para fazer com que duas `Goroutines` se comuniquem e para sincronizar a execução de seus códigos. Vamos utilizar criar um segundo exemplo para entender como um `channel` funciona:

```go
// main.go
package main

import (
	"fmt"
	"time"
)

func sender(c chan string) {
	for i := 0; ; i++ {
		c <- "Hello"
	}
}

func printer(c chan string) {
	for {
		msg := <-c
		fmt.Println(msg)
		time.Sleep(time.Second * 1)
	}
}

func main() {
	var exampleChannel chan string = make(chan string)

	go sender(exampleChannel)
	go printer(exampleChannel)

	var input string
	fmt.Scanln(&input)
}
```

Então vamos entender como nosso código funciona. Primeiro temos duas funções: `sender` e `printer`, ambas recebem como parâmetro um `channel` de strings e executam operações com esse `channel`. Antes de entrar em mais detalhes vamos ver como criamos um `channel`.

A criação do `channel` está aqui: `var exampleChannel chan string = make(chan string)`. Esse código faz com que criemos um `channel` de strings, ou seja, nosso channel só poderá receber strings. Usamos a função `make()` em **Go** para iniciar alguns tipos de dados como `channels`, `maps`, `slices` (mas não vamos aprofundar nisso por agora).

Depois de criado, passamos nosso `channel` para as duas `Goroutines` para que elas possam trabalhar com o mesmo. A função `sender` tem apenas um propósito, enviar mensagens para o nosso `channel`. Isso é feito através dessa instrução: `c <- "Hello"`. Como disse anteriormente a sintaxe de **Go** é muito bonita e leve, ao vermos esse código podemos ver claramente que estamos enviando uma string para o nosso `channel`.

A função `printer` também tem apenas um propósito: enquanto tiver mensagens em nosso `channel` ele vai imprimir a mensagem enviada e esperar um segundo antes de continuar a execução. O comando `msg := <-c` é bem simples, ele pega uma mensagem de nosso `channel` e atribui à variável `msg`, em conjunto com o comando `for`, enquanto tiver alguma mensagem em nosso `channel` vamos pegar essa mensagem e atribuir para nossa variável `msg`. O comando `time.Sleep(time.Second * 1)` apenas pára a execução de nosso programa por um segundo.

Enquanto não apertarmos **Enter** no console, nossas duas `Goroutines` continuaram a ser executadas e irão comunicar uma com a outra através de nosso `channel`.

### Buffered Channels

Por padrão os `channels` em **Go** não possuem buffer, ou seja, eles apenas aceitam um **input** no `channel`, por exemplo: `c <- "Hello"` caso tenha um **output** correspondente, por exemplo: `msg := <-c`. Mas podemos criar `channels` com buffer em **Go** e esses `channels` podem receber um número limitado de **inputs** mesmo sem um **output** correspondente. Veja o código abaixo:

```go
// main.go
package main

import "fmt"

func main() {
	messageChannel := make(chan string, 3)

	messageChannel <- "Message 1"
	messageChannel <- "Message 2"
	messageChannel <- "Message 3"

	fmt.Println(<-messageChannel)
	fmt.Println(<-messageChannel)
	fmt.Println(<-messageChannel)
}
```

No código acima, criamos um `channel` com um buffer com limite de três mensagems: `messageChannel := make(chan string, 3)` e logo após isso enviamos três mensagens para o `channel` mesmo sem um **output** criado. Após isso utilizamos o comando `fmt.Println(<-messageChannel)` três vezes para recuperar as mensagens que enviamos ao `channel` anteriormente.

Caso você tente enviar uma quarta mensagem ao `channel` iremos receber um **fatal error**, pois o limite do buffer de nosso `channel` é de três mensagens.

## Conclusão

Muito bem pessoal, lógico que isso é apenas uma introdução a essas features do **Go**, mas como vocês puderam ver, trabalhar com programação concorrente em **Go** é bem simples, a sintaxe é fácil de compreender e o código fica muito bonito. Em breve teremos mais material sobre **Go** aqui pela **CodeShare**!!!

Espero que tenham gostado e até o próximo artigo!!! Abraços!!!

### Escrito por [Wendell Adriel](https://wendelladriel.com)