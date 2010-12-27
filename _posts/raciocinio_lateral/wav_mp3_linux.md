title: Convertendo wav para mp3 em dois passos + 1
tags: audio, dica, linux, mp3, wav

### Passo 1

    sudo apt-get install lame

### Passo 2

    :::bash
    for f in *.wav; do lame --vbr-new -V 3  "$f" "${f%.wav}.mp3"; done

### Extra

Crie um script com a linha de comando acima. Precisarás dela novamente!:D 
