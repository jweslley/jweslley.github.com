title: Gráficos de barras com intervalo de confiança em R
tags: dica, r

Ontem, precisei criar um gráfico de barras com intervalo de confiança decente. Resolvi usar [R][R]. Problema: pouco conhecimento de R. Para evitar que você passe mesmo aperto, vou mostrar aqui como fiz.

## Contextualizando

Estava comparando dois _softwares_, chamarei-os de *A* e *B*. Cada uma deles, foi submetido a uma série de experimentos em quatro cenários distintos. No fim dos experimentos, produzi um arquivo com os valores das médias e dos intervalos de confiança tanto para *A* quanto para *B* nos quatro cenários. O arquivo tinha o seguinte formato:

    A AICI AICS B BICI BICS
    60 55 65 50 45 50
    90 85 95 80 78 82
    120 115 125 90 86 94
    150 145 155 100 90 110

Onde cada linha representa os valores de um determinado cenário, com exceção da primeira linha (representa o nome das colunas). Enquanto as colunas seguem a seguinte definição:

* Coluna A: Os valores das médias de *A*.
* Coluna AICI: Os intervalos de confiança inferior de *A*.
* Coluna AICS: Os intervalos de confiança superior de *A*.
* Coluna B: Os valores das médias de *B*.
* Coluna BICI: Os intervalos de confiança inferior de *B*.
* Coluna BICS: Os intervalos de confiança superior de *B*.

Assim, o script R abaixo resolveu o problema:

    :::R
    library(gplots)

    # carrega o arquivo com os dados
    data <- read.table("results.dat", header=T, sep=" ")

    # nome do arquivo .png onde o gráfico será plotado
    png(filename="results-barplot.png", bg="white")

    # auto-descritivo
    legenda <- c("Cenário 1","Cenário 2","Cenário 3","Cenário 4")
    cores <- c("lightblue", "mistyrose", "lightcyan", "lavender")

    # crio uma matriz com 2 colunas: médias de A e B, respectivamente.
    medias <- as.matrix(data[, c(1, 4)])

    # crio uma matriz com 2 colunas:
    # intervalos de confiança inferior de A e B, respectivamente.
    ic.inferior <- as.matrix(data[, c(2, 5)])

    # crio uma matriz com 2 colunas:
    # intervalos de confiança superior de A e B, respectivamente.
    ic.superior <- as.matrix(data[, c(3, 6)])

    # Importante: As matrizes criadas acima (medias, ic.inferior e ic.superior)
    # precisam ter as mesmas dimensões.

    barplot2(medias, # A matriz a ser plotada
      beside=TRUE, # Plota as barras lado-a-lado
      col=cores, # As cores das barras
      legend=legenda, # A legenda
      ylim=c(0, 200), # O limite máximo do eixo y
      main="A x B", # Titulo principal
      ylab="Valor", # Label do eixo y
      plot.ci=TRUE, # TRUE, deve-se plotar os intervalos de confiança
      ci.l=ic.inferior, # A matriz dos limites inferiores dos intervalos de confiança
      ci.u=ic.superior, # A matriz dos limites superiores dos intervalos de confiança
      font.main=4, cex.axis=1.2, cex.lab=1.5, cex.names=1.5 # Firulas adicionais
    )

E, o gráfico produzido foi este:

![Gráficos de barras com intervalo de confiança em R](http://4.bp.blogspot.com/_WfgfddE/SxAxrrZP-_I/AAAAAAAAAio/zUyU0a52x0Y/s320/results-barplot.png)

[R]: http://www.r-project.org/ "The R Project for Statistical Computing"

