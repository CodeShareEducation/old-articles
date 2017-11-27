# Animações com CSS3: Primeiros Passos

# Básicos de animação em CSS3

A animação tem sido um dos principais pilares da interactividade na internet. Sejam filmes, gifs animados, animação em flash; quando usadas correctamentes, as animações são da ferramentas de interactividade mais poderosas. Trazem vida ao ecrã e adicionam uma nova camada de User Experience ao nosso conteúdo.

Hoje não vos venho falar da história da animação na web, nem na complexidade associada à mesma e muito menos o porquê do flash ter caído em desuso. Hoje venho-vos falar em como usar CSS3 para animar elementos nas nossas páginas.

Animações em CSS são como transições que tomaram esteórides! Em vez de termos um estado inicial e um estado final, com as animações em CSS temos a possibilidade de utilizar diversos estados intermédios com os quais podemos criar animações mais sofisticadas e complexas.

O simples facto de serem mais fáceis e compactas que as animações feitas em Javascript, tornam esta ferramenta perfeita para pequenas animações na web. Não só são simples e rápidas de criar, são também elas bastante poderosas. Grande parte destas vantagens vêm do facto das animações serem geridas pelo browser e não pelo nosso código.

### Utilizando Animações com CSS3

O componente principal de uma animação em CSS é o componente `@keyframes`. `@keyframes` é a componente CSS onde a animação é criada. Este componente é composto por 3 propriedades principais:

- O nome da animação
- Os estados da animação de 0% a 100% (ou utilizando as keywords `from` e `to` que corresponde a **0%** e **100%** respectivamente)
- Estilos CSS. Estes estilos representam a animação e casa um dos seus estados.

Por exemplo, vamos considerar o nosso amiguinho, o pequeno quadrado vermelho:

```html
<html>
    <head>
        <style>
        div {
            width: 100px;
            height: 100px;
            background-color: red;
        }
        </style>
    </head>
    <body>
        <div></div>
    </body>
</html>
```

O nosso objectivo é criar uma animação que altera a cor do nosso quadrado vermelho. Usando a notação que vimos à pouco, esta animação tem o seguinte aspecto:

```css
@keyframes example {
    0% {background-color: red;}
    100% {background-color: yellow;}
}
```

ou o equivalente:

```css
@keyframes example {
    from {background-color: red;}
    to {background-color: yellow;}
}
```

Agora que temos a nossa animação criada, temos que a chamar dentro do selector CSS que queremos animar. Para esse efeito usamos a propriedade `animation`. A propriedade `animation` pode ter múltiplas propriedades:

- `animation-name:` o nome da animação criada em `@keyframes`
- `animation-duration:` a duração do frame temporal
- `animation-timing-function:` a velocidade da animação
- `animation-delay:` o tempo antes da animação começar
- `animation-iteration-count:` quantas vezes a animação vai ser mostrada
- `animation-direction:` dá-nos a habilidade de mudar a direcção do loop ( de trás para a frente, por exemplo )

Com esta informação vamos então completar a animação do nosso quadrado vermelho. Quando tudo estiver definido, o nosso pequeno quadrado vermelho passará a ser o nosso pequeno quadrado amarelo! Para completar a animação vamos então adicionar as seguintes linhas à nossa `div`:

```css
div {
    width: 100px;
    height: 100px;
    background-color: red;
    animation-name: example;
    animation-duration: 4s;
    }
```

Estamos explicitamente a dizer que a animação `(@keyframes)` a utilizar se chama `example`, e que a animação tem a duração de 4 segundos.

Ter uma animação com apenas 2 estados é a mesma coisa que ter uma transição. Por isso vamos adicionar mais um estado para que possamos testemunhar melhor o comportamento e o poder de uma animação.

Vamos então definir que entre passar de vermelho para amrelo, queremos que o nosso quadrado seja verde. Para obter este efeito vamos adicionar um novo estado, 50%, desta forma:

```css
@keyframes example {
    0% {background-color: red;}
    50% {background-color: green;}
    100% {background-color: yellow;}
}
```

Como podem ver, estamos a passar de vermelho, para verde e finalmente para amarelo. Quantos mais estado adicionarmos, mais rica será a nossa animação.

### Prefixos de Vendor

Os prefixos de vendor foram criados originalmente para permitir que os developers de browsers pudessem suportar declarações experimentais cd CSS. O prefixo de vendor basicamente diz "esta é a forma da Mozilla interpretar esta proposta", por exemplo.
É uma boa prática utilizar os vários prefixos de vendor nas nossas animações, porque permite que diferentes browsers possam renderizar as coisas à sua maneira. Olhando para o nosso código, usando prefixos de vendor, seria o seguinte:

```css
@-webkit-keyframes example{
    0% {background-color: red;}
    100% {background-color: yellow;}
}
@-moz-keyframes example {
    0% {background-color: red;}
    100% {background-color: yellow;}
}
@-o-keyframes example {
    0% {background-color: red;}
    100% {background-color: yellow;}
}
@keyframes example {
    0% {background-color: red;}
    100% {background-color: yellow;}
}
```

E o CSS correspondente:

```css
div {
    width: 100px;
    height: 100px;
    background-color: red;
    animation-name: example;
    -webkit-animation-name: example;
    animation-duration: 4s;
    -webkit- animation-duration: 4s;
    }
```

### Concluindo

Há muito a dizer acerca de animações usando CSS3. Utilizando as ferramentas que vimos neste pequeno tutorial é apenas uma questão de utilizar mais estados por forma a criar animações mais robustas e complexas. Podem também encontrar muitos mais exemplos de animações em CSS3 na internet para inspiração.

Por exemplo, podem conferir neste [fiddle](https://jsfiddle.net/DailyMatters/vpet4v1e/) a continuação da aventura do nosso quadrado vermelho.

Espero que tenham gostado desta introdução às animações usando CSS3. Partilhem as vossas experiências nos comentários.

Até uma próxima!

### Escrito por [Claudio Ribeiro](https://github.com/DailyMatters)