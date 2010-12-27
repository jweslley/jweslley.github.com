title: Brincando com números primos, usando Scala!
tags: scala

Nos últimos meses, tenho dedicado algum tempo para aprender [Scala][Scala]. Então, decidi que é chegada a hora de ajudar a divulgar esta excelente linguagem. Nada como um problema clássico para começar: calcular números primos utilizando o Crivo de Eratóstenes. Vale lembrar que o foco deste post não é mostrar como calcular números primos usando o Crivo de Eratóstenes, mas como fazê-lo em Scala. A imagem abaixo mostra como funciona o Crivo, mais informações sobre podem ser obtidas [aqui][Crivo].

![Crivo de Eratóstenes](http://upload.wikimedia.org/wikipedia/commons/c/c8/Animation_Sieve_of_Eratosth-2.gif)

Fonte: [Wikipedia](http://en.wikipedia.org/wiki/Sieve_of_Eratosthenes)

Sem meias palavras, a solução COMPLETA é listada em seguida:

    :::scala
    def primos = crivo(Stream from 2)
    def crivo(s: Stream[Int]): Stream[Int] = Stream.cons(s.head, crivo(s.tail filter { _ % s.head != 0 }))

### Explicando...

O código acima define duas funções: _primos_ e _crivo_. A primeira, _primo_, retorna uma lista de todos os números primos a partir de 2 -- mas não se preocupe, os números apenas serão calculados à medida que forem necessários, isto é possivel utilizando-se [Streams][Stream]. A segunda, _crivo_, executa a lógica do crivo. Utilizando Streams ela seleciona o primeiro elemento(que é primo) da lista de números passada como argumento e elimina/filtra todos os múltiplos do número selecionado da cauda da lista. Pelo que vc já deve ter percebido, Streams consistem de um elemento inicial, a cabeça da lista(_head_), e uma lista representando os demais elementos, a cauda(_tail_).

### Playground!

Agora que temos uma lista de números primos, vamos usá-la para:

Calcular e imprimir os `N` primeiros números primos

    :::scala
    primos take 10 foreach println

Calcular e imprimir todos os números primos menores que 120 (como aparece na imagem acima)

    :::scala
    primos takeWhile {_ < 120} foreach println

Calcular e imprimir o 100° número primo

    :::scala
    print(primos(100))

Calcular e imprimir do 100° ao 110° número primo

    :::scala
    primos.slice(100,110)foreach println

Há várias outras possibilidades. Veja documentação da classe [Stream][Stream] e divirta-se!

Até a próxima!

[Scala]: http://www.scala-lang.org/ ""
[Stream]: http://www.scala-lang.org/docu/files/api/scala/Stream.html ""
[Crivo]: http://pt.wikipedia.org/wiki/Crivo_de_Erat%C3%B3stenes ""
