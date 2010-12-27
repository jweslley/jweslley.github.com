title: Dica Linux: Aumente sua produtividade ao usar o comando 'cd'
tags: alias, bash, cd, dica, linux

Um dos comandos mais utilizados durante a utilização de um terminal Linux/Unix, sem dúvida, é o `cd`. Para melhorar sua produtividade apresentarei alguns hacks para este comando.

### 1. Subindo na árvore de diretórios eficientemente com aliases

Navegar em uma estrutura de diretórios muito profunda é bastante cansativo. Imagine o trabalho de Shrek para voltar para o seu diretório `/home/shrek`:

    shrek@pantano:~/fotos/viagem/reino/tao/tao/tao/distante $ cd ../../../../../../..
    shrek@pantano:~ $ 

Ao invés de executar `cd ../../../../../../..` para subir na árvore de diretórios, adicione os seguintes aliases para o seu `~/.bashrc` e reinicie o terminal:

    :::bash
    alias ..="cd .."
    alias ..2="cd ../.."
    alias ..3="cd ../../.."
    alias ..4="cd ../../../.."
    alias ..5="cd ../../../../.."
    alias ..6="cd ../../../../../.."
    alias ..7="cd ../../../../../../.."

Usando:

    shrek@pantano:~/fotos/viagem/reino/tao/tao/tao/distante $ ..7
    shrek@pantano:~ $

### 2. Alternar entre os dois últimos diretórios

    shrek@pantano:~ $ cd fotos/viagem/reino/tao/tao/tao/distante
    shrek@pantano:~/fotos/viagem/reino/tao/tao/tao/distante $ pwd
    /home/shrek/fotos/viagem/reino/tao/tao/tao/distante
    shrek@pantano:~/fotos/viagem/reino/tao/tao/tao/distante $ cd -
    shrek@pantano:~ $ cd -
    shrek@pantano:~/fotos/viagem/reino/tao/tao/tao/distante $

### 3. Usar `shopt -s cdspell` para corrigir automagicamente nomes de diretórios digitados errados

    jonhnny@pc$ cd Docmentos
    bash: cd: Docmentos: Arquivo ou diretório inexistente
    jonhnny@pc$ shopt -s cdspell
    jonhnny@pc$ cd Docmentos
    jonhnny@pc:~/Documentos $ pwd
    /home/jonhnny/Documentos

Nota: "Esqueci" de digitar a letra 'u' de 'Documentos'!

### 4. Usando CDPATH ou como tornar o comando `cd` promíscuo

A variável de ambiente CDPATH define o diretório base para o comando `cd`, em outras palavras, você poderá navegar entre os diretórios sem especificar o diretório-pai.

    jonhnny@pc$ pwd
    /home/jonhnny
    jonhnny@pc$ cd projeto-x
    bash: cd: projeto-x: Arquivo ou diretório inexistente
    jonhnny@pc$ export CDPATH=/local/workspace
    jonhnny@pc$ cd projeto-x
    /local/workspace/projeto-x
    jonhnny@pc:/local/workspace/projeto-x $ pwd
    /local/workspace/projeto-x

Nota: Não foi preciso especificar o diretório-pai: `/local/workspace`

Gostou disto? Não?! Então ignore. Caso sim, exporte a variável CDPATH no seu `~/.bashrc` e reinicie o terminal para usufruir deste hack:

    :::bash
    export CDPATH=.:~:/local/workspace
