# Versionamento com Git Parte 2


## Sumário
  * [Introdução](#introduo)
  * [Configurando chave SSH](#configurando-chave-ssh)
  * [Apresentando merge automático do Git](#Apresentando-merge-automtico-do-git)
  * [Tratando merge manual](#tratando-merge-manual)
  * [Merge entre branches](#merge-entre-branches)
  * [Merge entre branches quando há conflito](#merge-entre-branches-quando-h-conflito)
  * [Desfazendo alterações](#desfazendo-alteraes)
  * [Backup de alterações que não foram enviado por commit](#backup-de-alteraes-que-no-foram-enviado-por-commit)
  * [Configurando aliases](#configurando-aliases)
  * [Conclusão](#concluso)

## Introdução

No **[artigo anterior](http://blog.codeshare.com.br/versionamento-com-git/)** sobre Git, abordamos muitas operações porém com cenários bem simples. Em nenhum deles tivemos alteração concorrentes por usuários diferentes em um mesmo arquivo. E isso é algo comum de acontecer em um ambiente de trabalho. Quando isso ocorre nem sempre a ferramenta consegue realizar os merges de forma automática, e essa tarefa acaba virando responsabilidade de quem está operando o repositório. Veremos como deixar essas tarefas mais fáceis.

Se você também está cansado de ficar digitando suas senhas toda vez que vai realizar um push, ensinaremos como utilizar as **chaves SSH**, onde antes de toda operação de push, o git irá procurar por ela e usá-la para sua autenticação no repositório remoto. Nesse artigo você também aprenderá sobre os alias, chega de ficar digitando tantos comandos um atrás de outros para tarefas que você acaba tendo que executar várias vezes.

## Configurando chave SSH

Antes de configurarmos nossa **chave SSH** temos que criá-la, para isso execute o comando abaixo:

```shell
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
  Generating public/private rsa key pair.
  Enter a file in which to save the key (/Users/you/.ssh/id_rsa): [Press enter]
  Enter passphrase (empty for no passphrase): [Type a passphrase]
  Enter same passphrase again: [Type passphrase again]
```

E você pode verificar por possíveis chaves cadastradas com o seguinte comando:

```shell
$ ls -al ~/.ssh
```

Depois de gerada a chave, vá para seu perfil no Github, vá para a área de configuração e no menu lateral esquerdo ache o menu **SSH and GPG keys**, aperte o botão **New SSH key**, e cole sua chave que você vai obter através do comando:

```shell
$ cat ~/.ssh/id_rsa.pub
```

Pronto com isso você não terá que ficar digitando sua senha do Git toda vez que for realizar um push. A autenticação será baseada na sua chave ssh.

![Adicionando chave SSH](../assets/img/post-git/personal-settings.png "Adicionando chave SSH")

## Apresentando merge automático do Git

É muito comum termos várias pessoas trabalhando em um mesmo projeto, com isso ocorrerão vezes onde duas ou mais pessoas irão modificar um mesmo arquivo, quando isso ocorre o Git faz um processo de merge dessas modificações, pegando tanto as alterações do usuário A e do usuário B e juntado elas no mesmo arquivo.

Para demonstrar esse comportamento teremos dois usuários, **mcqueide** e **macaulay** trabalhando no mesmo repositório, ambos usuários irão realizar uma modificação no nosso arquivo `index.html` e a ferramenta tratará esse merge de uma forma automática e inteligente.

Segue o arquivo atual no nosso repositório:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Treinamento GIT</title>
  </head>
  <body>
    <h1>Treinamento GIT</h1>
  </body>
</html>
```

O usuário **mcqueide** alterou o título da página para:

```html
...
<title>Treinamento Git</title>
...
```

Já o usuário **macaulay** alterou a tag `h1` no corpo da página:

```html
...
<h1>Treinamento Git</h1>
...
```

Agora ambos irão enviar essas modificações para o servidor remoto. O primeiro a enviar é o usuário **mcqueide**, ele realiza o `git commit` e depois faz um `git push` no repositório. Para ele foi tudo ok, não houve nenhum problema. Depois o usuário **macaulay** decide enviar suas alterações também, ele realiza seu `commit` até aí tudo bem, porém quando ele tenta realizar o `push` acontece o seguinte:

```shell
$ git push origin master
  Username for 'https://github.com': macaulay1001
  Password for 'https://macaulay1001@github.com':
  To https://github.com/mcqueide/treinamentoGIT.git
   ! [rejected]        master -> master (fetch first)
  error: failed to push some refs to 'https://github.com/mcqueide/treinamentoGIT.git'
  dica: Updates were rejected because the remote contains work that you do
  dica: not have locally. This is usually caused by another repository pushing
  dica: to the same ref. You may want to first integrate the remote changes
  dica: (e.g., 'git pull ...') before pushing again.
  dica: See the 'Note about fast-forwards' in 'git push --help' for details.
```

O git informa que o `push` do **macaulay** foi rejeitado, isso porque sua cópia local está desatualizada em relação ao repositório remoto, pois tem um novo commit do usuário **mcqueide**, o próprio git já dá uma dica, solicitando que ele atualize seu repositório para que fique atualizado em relação ao repositório remoto, depois disso o **macaulay** realiza o `pull` e o git direciona ele para um arquivo de texto, apresentando uma tela em seu terminal para ele digitar uma mensagem de merge:

```shell
Merge branch 'master' of https://github.com/mcqueide/treinamentoGIT
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# No ramo master
#
# Submissão inicial.
#
# Mudanças a serem submetidas:
#       new file:   index.html
#
```

> Não é necessário apagar as linhas que estão comentadas com #, porque elas não irão para sua mensagem de commit.

Feito isso o git já informa que foi realizado um merge automático e ele realiza novamente o comando `git push` e agora seu `push` é aceito.

```shell
$ git pull
  remote: Counting objects: 3, done.
  remote: Compressing objects: 100% (1/1), done.
  remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
  Unpacking objects: 100% (3/3), done.
  From https://github.com/mcqueide/treinamentoGIT
     23b5a50..27ee314  master     -> origin/master
  Mesclagem automática de index.html
  Merge made by the 'recursive' strategy.
   index.html | 2 +-
   1 file changed, 1 insertion(+), 1 deletion(-)

$ git push origin master
  Username for 'https://github.com': macaulay1001
  Password for 'https://macaulay1001@github.com':
  Counting objects: 6, done.
  Delta compression using up to 4 threads.
  Compressing objects: 100% (4/4), done.
  Writing objects: 100% (6/6), 589 bytes | 0 bytes/s, done.
  Total 6 (delta 2), reused 0 (delta 0)
  remote: Resolving deltas: 100% (2/2), completed with 1 local objects.
  To https://github.com/mcqueide/treinamentoGIT.git
     27ee314..62b3e3e  master -> master
```

Agora o arquivo está com as alterações dos dois usuários:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Treinamento Git</title>
  </head>
  <body>
    <h1>Treinamento Git</h1>
  </body>
</html>
```

E se olharmos o log do repositório irá aparecer o commit do primeiro usuário, o do segundo e o commit de merge.

```shell
$ git log
  commit 62b3e3e5bd3b27726d4192e57a6d2c03c870bde2
  Merge: 22fc69d 27ee314
  Author: macaulay1001 <macaulay1001@gmail.com>
  Date:   Mon Jan 30 19:02:48 2017 -0200

      Merge branch 'master' of https://github.com/mcqueide/treinamentoGIT

  commit 27ee3147e749611875f4ef23720cfc8fb01be482
  Author: mcqueide <mcqueide@gmail.com>
  Date:   Mon Jan 30 19:00:20 2017 -0200

      Atualizando title

  commit 22fc69db0da891cc3380f6d8db6bd3c04b11159b
  Author: macaulay1001 <macaulay1001@gmail.com>
  Date:   Mon Jan 30 18:59:57 2017 -0200

      Alterando h1
```

Infelizmente ainda ficou o commit de merge no nosso log, isso poderia ter sido evitado se o usuário macaulay estivesse em uma branch diferente, e depois tivesse realizado o envio dessas informações para branch master. Mas veremos isso em breve.

## Tratando merge manual

Nós vimos que a ferramenta é bem inteligente a ponto de conseguir fazer um merge automático, mas as alterações realizadas foram no mesmo arquivo porém em linhas diferentes. Agora o que aconteceria se as alterações tivessem sido na mesma linha? A ferramenta seria capaz ainda de realizar o merge automático, e como ela seria capaz disso?

Para realizarmos o teste, temos o arquivo `index.html` no seguinte estado:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Treinamento Git</title>
  </head>
  <body>
    <h1>Treinamento Git</h1>

    <footer>
      Copyright - 2017
    </footer>
  </body>
</html>
```

O usuário **mcqueide** irá adicionar o nome dele no **copyright** do site, então ele modifica a mensagem de copyright para:

```html
...
Copyright - 2017 | Mc.Queide
...
```

Depois disso ele já realiza o `commit` e o `push` dessas alterações. Agora o usuário **macaulay** também vai realizar uma alteração nessa mesma linha do **copyright**, adicionando o nome dele na mensagem de copyright.

```html
...
Copyright - 2017 | Macaulay
...
```

Porém o usuário **macaulay** está sem as alterações submetidas pelo usuário mcqueide, então ele realiza o `commit`, mas quando ele executa o `push` ele recebe a seguinte mensagem:

```shell
$ git commit -am "Adicionando Macaulay na mensagem de copyright"
  [master e5805f4] Adicionando Macaulay na mensagem de copyright
   1 file changed, 1 insertion(+), 1 deletion(-)

$ git push origin master
  Username for 'https://github.com': macaulay1001
  Password for 'https://macaulay1001@github.com':
  To https://github.com/mcqueide/treinamentoGIT.git
   ! [rejected]        master -> master (fetch first)
  error: failed to push some refs to 'https://github.com/mcqueide/treinamentoGIT.git'
  dica: Updates were rejected because the remote contains work that you do
  dica: not have locally. This is usually caused by another repository pushing
  dica: to the same ref. You may want to first integrate the remote changes
  dica: (e.g., 'git pull ...') before pushing again.
  dica: See the 'Note about fast-forwards' in 'git push --help' for details.
```

Seu push foi rejeitado novamente, devido o repositório remoto está uma revisão a frente do repositório local do usuário **macaulay**. Então assim como o usuário **macaulay** fez no exemplo anterior, ele atualiza sua branch local com o `git pull`. Mas quando ele executa o `pull`, é apresentado o seguinte resultado:

```shell
$ git pull
  remote: Counting objects: 3, done.
  remote: Compressing objects: 100% (1/1), done.
  remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
  Unpacking objects: 100% (3/3), done.
  From https://github.com/mcqueide/treinamentoGIT
     69e134b..5e78803  master     -> origin/master
  Mesclagem automática de index.html
  CONFLITO (conteúdo): conflito de mesclagem em index.html
  Automatic merge failed; fix conflicts and then commit the result.
```

Diferente da primeira vez, o git não foi capaz de realizar o merge automático, então ele passa a responsabilidade para o usuário. Se abrirmos o arquivo `index.html`, veremos que ele está no seguinte estado:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Treinamento Git</title>
  </head>
  <body>
    <h1>Treinamento Git</h1>

    <footer>
<<<<<<< HEAD
      Copyright - 2017 | Macaulay
=======
      Copyright - 2017 | Mc.Queide
\>>>>>>> 5e78803bb99fa975ca550eb8301ec105fe777925
    </footer>
  </body>
</html>
```

Para sinalizar onde houve o conflito, o git adiciona algumas marcações, ele envolve o trecho conflitante com **<<<<<<HEAD** e **>>>>>> Hash da Revisão**. O que está entre **<<<<<<< HEAD** e **=======** são as alterações local, o que está entre **=======** e **>>>>>>> 5e78803bb99fa975ca550eb8301ec105fe777925** são as alterações atual do repositório remoto. Então o git sinaliza onde houve o conflito e espera que o usuário o resolva. Para resolvermos esse conflito vamos remover a marcação do git e colocarmos os nomes dos dois usuários no copyright, deixando o arquivo com o seguinte estado:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Treinamento Git</title>
  </head>
  <body>
    <h1>Treinamento Git</h1>

    <footer>
      Copyright - 2017 | Macaulay & Mc.Queide
    </footer>
  </body>
</html>
```

Pronto, finalizamos a operação de merge manual, mas para o git esse arquivo ainda está com conflito, então temos que realizar o `git add` no arquivo, o `commit` do merge e após isso realizar o push para o repositório remoto.

```shell
$ git status
  No ramo master
  O seu ramo e 'origin/master' divergiram-se,
  e cada um tem 1 e 1 submissão, respectivamente.
    (use "git pull" to merge the remote branch into yours)
  Você tem caminhos não mesclados.
    (fixar conflitos e executar "git commit")

  Caminhos não mesclados:
    (usar "git add <arquivo>..." para marcar resolução)

  	ambos modificados:   index.html

  nenhuma modificação adicionada à submissão (utilize "git add" e/ou "git commit -a")

$ git add index.html

$ git status
  No ramo master
  O seu ramo e 'origin/master' divergiram-se,
  e cada um tem 1 e 1 submissão, respectivamente.
    (use "git pull" to merge the remote branch into yours)
  Todos os conflitos foram corrigidos mas você continua mesclando.
    (use  "git commit" para concluir a mesclagem)

  Mudanças a serem submetidas:

  	modified:   index.html

$ git commit -m "Realizando o merge no arquivo index.html"
  [master 2b4547a] Realizando o merge no arquivo index.html

$ git push origin master
  Username for 'https://github.com': macaulay1001
  Password for 'https://macaulay1001@github.com':
  Counting objects: 6, done.
  Delta compression using up to 4 threads.
  Compressing objects: 100% (4/4), done.
  Writing objects: 100% (6/6), 605 bytes | 0 bytes/s, done.
  Total 6 (delta 2), reused 0 (delta 0)
  remote: Resolving deltas: 100% (2/2), completed with 1 local objects.
  To https://github.com/mcqueide/treinamentoGIT.git
     5e78803..2b4547a  master -> master
```

## Merge entre branches

Nós simulamos o cenários onde 2 usuários trabalhavam na mesma branch, mais uma boa prática é separamos uma branch de desenvolvimento da nossa branch principal que é a master, dessa forma colocamos o conteúdo de desenvolvimento na branch master quando a funcionalidade estiver finalizada. Fazendo com o conteúdo da branch master sempre esteja estável.

Então vamos criar duas novas branches, uma para o usuário **mcqueide** e outra para o usuário **macaulay**, ambos irão desenvolver em suas branches particulares e depois mandar suas alterações para a branch master quando suas tarefas estiverem finalizadas.

```shell
mcqueide:/tmp/repo/treinamentoGit $ git checkout -b mcqueide
macaulay:/tmp/repo/treinamentoGit $ git checkout -b macaulay
```

Vamos partir nessa seção com nosso arquivo `index.html` no seguinte estado:

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Treinamento Git</title>
  </head>
  <body>
    <header>
        <h1>Título da Página</h1>
    </header>

    <main>
      Conteúdo da página
    </main>

    <footer>
      Rodapé da página
    </footer>
  </body>
</html>
```

O usuário **mcqueide** já está com seu ramo atualizado, e irá começar seu trabalho, sua primeira tarefa é alterar o título da página. Ele define o título da página como **TreinamentoGit**, depois que ele define o título da página ele realiza o `commit`.

```html
...
<h1>Treinamento Git</h1>
...
```

```shell
mcqueide:/tmp/repo/treinamentoGit $ git commit -am "Alterando título da página"
  [mcqueide a1c8022] Alterando título da página
   1 file changed, 1 insertion(+), 1 deletion(-)
```

Enquanto isso o **macaulay** está realizando sua atividade na sua branch particular,  ele define o rodapé da página, e o altera para `TreinamentoGit CopyRight 2017`.

Então o usuário **mcqueide** irá enviar seu `commit` para a branch master, lembrando que o commit que ele realizou foi na branch particular dele, então primeiros temos que levar esse commit para a branch master e depois enviá-lo para o repositório remoto. Primeiro ele faz o **checkout** para branch master, executa o `git pull` para ter certeza se não novas alterações, e em seguida faz o `merge` da **branch master** com sua branch. Depois de realizar o **merge**, se executarmos o `git log` veremos que o commit que ele realizou na **branch mcqueide**, já se encontra na branch master, então podemos enviar para o repositório remoto com o `git push`.

```shell
mcqueide:/tmp/repo/treinamentoGit $ git checkout master
  Switched to branch 'master'
  Your branch is up-to-date with 'origin/master'.
  mcqueide:/tmp/repo/treinamentoGit $ git pull
  Already up-to-date.

mcqueide:/tmp/repo/treinamentoGit $ git merge mcqueide
  Updating 3524039..a1c8022
  Fast-forward
   index.html | 2 +-
   1 file changed, 1 insertion(+), 1 deletion(-)

mcqueide:/tmp/repo/treinamentoGit $ git log
  commit a1c80223de43f1f33c6bcb2cbe1d460c5bee1407
  Author: John Mc.Queide <mcqueide@gmail.com>
  Date:   Sat Feb 4 11:44:44 2017 -0200

      Alterando título da página

mcqueide:/tmp/repo/treinamentoGit $ git push origin master
  Counting objects: 3, done.
  Delta compression using up to 4 threads.
  Compressing objects: 100% (2/2), done.
  Writing objects: 100% (3/3), 311 bytes | 0 bytes/s, done.
  Total 3 (delta 1), reused 0 (delta 0)
  remote: Resolving deltas: 100% (1/1), completed with 1 local objects.
  To git@github.com:mcqueide/treinamentoGIT.git
     3524039..a1c8022  master -> master
```

O usuário **macaulay** termina sua alteração e realiza o `commit` na sua branch.

```shell
macaulay:/tmp/repo/treinamentoGit $ git commit -am "Alterando rodapé da página"
  [macaulay 0aa318e] Alterando rodapé da página
   1 file changed, 1 insertion(+), 1 deletion(-)
```

Agora o usuário **macaulay** vai enviar suas alterações para o repositório remoto, lembrando que ele também está na sua branch particular. Como primeiro passo, o usuário **macaulay** faz o **checkout** para branch master e nela realiza o **pull**, nisso o git traz as alterações realizada pelo usuário **mcqueide** para sua branch local, deixando ela atualizada, então ele pode realizar o merge direto da branch master com a branch macaulay, porém isso gera mais um commit de merge no qual queremos evitar, então temos que atualizar sua branch primeiro. Sabendo disso o usuário **macaulay** muda para sua branch, e para atualizar ela executa o `git rebase`, o **rebase** vai trazer commit a commit e aplicar na branch destino e sem aplicar nenhum commit de merge, caso haja algum conflito. Depois de atualizar sua branch de trabalho, ele volta para a branch master e agora sim realiza o **merge** e envia as alterações para o repositório remoto.

```shell
macaulay:/tmp/repo/treinamentoGit $ git checkout master
  Switched to branch 'master'
  Your branch is up-to-date with 'origin/master'.

macaulay:/tmp/repo/treinamentoGit $ git pull
  remote: Counting objects: 3, done.
  remote: Compressing objects: 100% (1/1), done.
  remote: Total 3 (delta 1), reused 3 (delta 1), pack-reused 0
  Unpacking objects: 100% (3/3), done.
  From https://github.com/mcqueide/treinamentoGIT
    d173696..18ab87e  master     -> origin/master
  Updating d173696..18ab87e
  Fast-forward
  index.html | 2 +-
  1 file changed, 1 insertion(+), 1 deletion(-)

macaulay:/tmp/repo/treinamentoGit $ git checkout macaulay
  Switched to branch 'macaulay'

macaulay:/tmp/repo/treinamentoGit $ git rebase master macaulay
  First, rewinding head to replay your work on top of it...
  Applying: Alterando rodapé da página

macaulay:/tmp/repo/treinamentoGit $ git checkout master
  Switched to branch 'master'
  Your branch is up-to-date with 'origin/master'.

macaulay:/tmp/repo/treinamentoGit $ git merge macaulay
  Updating 18ab87e..e484439
  Fast-forward
  index.html | 2 +-
  1 file changed, 1 insertion(+), 1 deletion(-)

macaulay:/tmp/repo/treinamentoGit $ git push origin master
  Username for 'https://github.com': macaulay1001
  Password for 'https://macaulay1001@github.com':
  Counting objects: 3, done.
  Delta compression using up to 4 threads.
  Compressing objects: 100% (2/2), done.
  Writing objects: 100% (3/3), 322 bytes | 0 bytes/s, done.
  Total 3 (delta 1), reused 0 (delta 0)
  remote: Resolving deltas: 100% (1/1), completed with 1 local objects.
  To https://github.com/mcqueide/treinamentoGIT.git
    18ab87e..e484439  master -> master
```

## Merge entre branches quando há conflito

Acabamos de ver como realizar o **merge** entre branches, mas e quando ocorre conflito? Como podemos resolver eles?

Para simularmos esse cenário, vamos trabalhar com o seguinte arquivo `index.html`.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Treinamento Git</title>
  </head>
  <body>
    <header>
        <h1>Título da Página</h1>
    </header>

    <main>
      Conteúdo da página
    </main>

    <footer>
      Rodapé da página
    </footer>
  </body>
</html>
```

O usuário **mcqueide** vai iniciar suas alterações na página, ele começa alterando o título da página para `<h1>Treinamento Git</h1>`, e realiza o commit.

```shell
mcqueide:/tmp/repo/treinamentoGit $ git commit -am "Alterando o título da página"
  [mcqueide a8680e9] Alterando o título da página
  1 file changed, 1 insertion(+), 1 deletion(-)
```

Agora ele altera o corpo da página, com o seguinte conteúdo:

```html
...
<main>
  <p>Está é uma página para simulação de conflito com merge de branchs.</p>
</main>
...
```

E realiza o commit das alterações:

```shell
mcqueide:/tmp/repo/treinamentoGit $ git commit -am "Alterando o corpo da página"
  [mcqueide 3c96082] Alterando o corpo da página
   1 file changed, 1 insertion(+), 1 deletion(-)
```

E por último ele realiza uma alteração no rodapé da página:

```html
...
<footer>
  Treinamento Git | Copyright 2017
</footer>
...
```

E ele executa o commit:

```shell
mcqueide:/tmp/repo/treinamentoGit $ git commit -am "Alterando o rodapé da página"
  [mcqueide ae4a0b1] Alterando o rodapé da página
   1 file changed, 1 insertion(+), 1 deletion(-)
```

Então ele decide enviar os commits para o repositório remoto.

```shell
mcqueide:/tmp/repo/treinamentoGit $ git checkout master
  Switched to branch 'master'
  Your branch is up-to-date with 'origin/master'.

mcqueide:/tmp/repo/treinamentoGit $ git pull
  Already up-to-date.

mcqueide:/tmp/repo/treinamentoGit $ git merge mcqueide
  Updating 1df326a..ae4a0b1
  Fast-forward
   index.html | 6 +++---
   1 file changed, 3 insertions(+), 3 deletions(-)

mcqueide:/tmp/repo/treinamentoGit $ git push origin master
  Username for 'https://github.com': mcqueide
  Password for 'https://mcqueide@github.com':
  Counting objects: 9, done.
  Delta compression using up to 4 threads.
  Compressing objects: 100% (6/6), done.
  Writing objects: 100% (9/9), 914 bytes | 0 bytes/s, done.
  Total 9 (delta 3), reused 0 (delta 0)
  remote: Resolving deltas: 100% (3/3), completed with 1 local objects.
  To https://github.com/mcqueide/treinamentoGIT.git
     1df326a..ae4a0b1  master -> master
```

O usuário **macaulay** estava trabalhando paralelamente com o usuário **mcqueide**, e portanto não têm as alterações do outro usuário na sua branch. Ele também realiza alterações no documento. Ele começa alterando o título da página e logo em seguida executa o `git commit`.

```html
...
<h1>TREINAMENTO GIT</h1>
...
```

```shell
macaulay:/tmp/repo/treinamentoGit $ git commit -am "Atualizando o título do documento"
  [macaulay 92aa3d4] Atualizando o título do documento
   1 file changed, 1 insertion(+), 1 deletion(-)
```

Agora o usuário **macaulay** atualiza o conteúdo do rodapé realizando o commit em seguida.

```html
...
<footer>
 TREINAMENTO GIT | COPYRIGHT 2017
</footer>
...
```

```shell
macaulay:/tmp/repo/treinamentoGit $ git commit -am "Atualizando o rodapé do documento"
  [macaulay afab70c] Atualizando o rodapé do documento
   1 file changed, 1 insertion(+), 1 deletion(-)
```

Terminando essas alterações, ele deseja enviar suas alterações para o repositório remoto. Para isso, ele realiza o mesmo processo que já realizamos algumas vezes aqui, ele vai mudar para a branch master, atualizar ela, e depois voltar para a **branch macaulay** para atualizar ela com o conteúdo da master.

```shell
macaulay:/tmp/repo/treinamentoGit $ git checkout master
  Switched to branch 'master'
  Your branch is up-to-date with 'origin/master'.

macaulay:/tmp/repo/treinamentoGit $ git pull
  remote: Counting objects: 9, done.
  remote: Compressing objects: 100% (3/3), done.
  remote: Total 9 (delta 3), reused 9 (delta 3), pack-reused 0
  Unpacking objects: 100% (9/9), done.
  From https://github.com/mcqueide/treinamentoGIT
     1df326a..ae4a0b1  master     -> origin/master
  Updating 1df326a..ae4a0b1
  Fast-forward
   index.html | 6 +++---
   1 file changed, 3 insertions(+), 3 deletions(-)

macaulay:/tmp/repo/treinamentoGit $ git checkout macaulay
  Switched to branch 'macaulay'
```

Então agora ele está pronto para realizar o `rebase`, porém ao realizar o rebase ele obtém o seguinte resultado na sua tela do terminal.

```shell
macaulay:/tmp/repo/treinamentoGit $ git rebase master macaulay
  First, rewinding head to replay your work on top of it...
  Applying: Atualizando o título do documento
  Using index info to reconstruct a base tree...
  M	index.html
  Falling back to patching base and 3-way merge...
  Mesclagem automática de index.html
  CONFLITO (conteúdo): conflito de mesclagem em index.html
  error: Failed to merge in the changes.
  Patch failed at 0001 Atualizando o título do documento
  The copy of the patch that failed is found in: .git/rebase-apply/patch

  When you have resolved this problem, run "git rebase --continue".
  If you prefer to skip this patch, run "git rebase --skip" instead.
  To check out the original branch and stop rebasing, run "git rebase --abort".
```

Essa mensagem informa que houve conflito durante o `rebase`, lembrando que o git rebase aplica commit a commit na branch que está sendo atualizada, na aplicação do primeiro commit houve um conflito que deve ser tratado, e o próprio git já dá uma dica que após  o tratamento do merge o usuário pode dar continuidade do rebase com o comando `git rebase --continue`. Então vamos tratar o primeiro conflito.

```html
...
<header>
<<<<<<< ae4a0b1e66a0fcbd58fcf0f59894f4e1dd4f7ab2
    <h1>Treinamento Git</h1>
=======
  <h1>TREINAMENTO GIT</h1>
\>>>>>>> Atualizando o título do documento
</header>
...
```

O primeiro conflito foi exatamente na tag header, que foi a aplicação do primeiro commit, o usuário macaulay escolhe o título que ele mesmo colocou.

```html
<header>
      <h1>TREINAMENTO GIT</h1>
</header>
```

Depois ele realiza o `git add` para informar para o git que o `merge` foi realizado e executa o `git rebase --continue` para dar continuidade no processo de rebase.

```shell
macaulay:/tmp/repo/treinamentoGit $ git add index.html

macaulay:/tmp/repo/treinamentoGit $ git rebase --continue
  Applying: Atualizando o título do documento
  Applying: Atualizando o rodapé do documento
  Using index info to reconstruct a base tree...
  M	index.html
  Falling back to patching base and 3-way merge...
  Mesclagem automática de index.html
  CONFLITO (conteúdo): conflito de mesclagem em index.html
  error: Failed to merge in the changes.
  Patch failed at 0002 Atualizando o rodapé do documento
  The copy of the patch that failed is found in: .git/rebase-apply/patch

  When you have resolved this problem, run "git rebase --continue".
  If you prefer to skip this patch, run "git rebase --skip" instead.
  To check out the original branch and stop rebasing, run "git rebase --abort".
```

E novamente ocorre um novo conflito, porém dessa vez podemos perceber que o git conseguiu aplicar o primeiro e o segundo commit, dando o conflito apenas no terceiro commit que é o do rodapé, onde os dois usuário realizaram modificações.

```html
...
<footer>
<<<<<<< d7aa763ca2ae4899f414fe25f39bf45f27c31ce5
  Treinamento Git | Copyright 2017
=======
  TREINAMENTO GIT | COPYRIGHT 2017
\>>>>>>> Atualizando o rodapé do documento
</footer>
...
```

Dessa vez o usuário **macaulay** escolhe manter o trecho do usuário **mcqueide**.

```html
...
<footer>
  Treinamento Git | Copyright 2017
</footer>
...
```

E novamente executa o `git add` para informar ao git que foi realizado o merge. E executa o `git rebase --continue` novamente. E como dessa vez era o último commit conflitante então ele aplica o commit e finaliza o processo.

```shell
macaulay:/tmp/repo/treinamentoGit $ git add index.html

macaulay:/tmp/repo/treinamentoGit $ git rebase --continue
  Applying: Atualizando o rodapé do documento
```

Agora o usuário **macaulay** pode retornar para a branch master e continuar o processo de envio de suas alterações para o repositório remoto.

```shell
macaulay:/tmp/repo/treinamentoGit $ git checkout master
  Switched to branch 'master'
  Your branch is up-to-date with 'origin/master'.

macaulay:/tmp/repo/treinamentoGit $ git merge macaulay
  Updating ae4a0b1..d717d81
  Fast-forward
   index.html | 4 ++--
   1 file changed, 2 insertions(+), 2 deletions(-)

macaulay:/tmp/repo/treinamentoGit $ git push origin master
  Username for 'https://github.com': macaulay1001
  Password for 'https://macaulay1001@github.com':
  Counting objects: 6, done.
  Delta compression using up to 4 threads.
  Compressing objects: 100% (4/4), done.
  Writing objects: 100% (6/6), 569 bytes | 0 bytes/s, done.
  Total 6 (delta 2), reused 0 (delta 0)
  remote: Resolving deltas: 100% (2/2), completed with 1 local objects.
  To https://github.com/mcqueide/treinamentoGIT.git
     ae4a0b1..d717d81  master -> master
```

## Desfazendo alterações

E o que fazemos quando queremos desfazer alguma alteração? Podemos ter várias respostas para essa pergunta, tudo irá depender do status que nosso arquivo se encontra.

Quando o arquivo foi modificado mas não foi adicionado para realização de commit podemos fazer esse arquivo voltar para seu estado original com o `git checkout nome_do_arquivo`.

Para simularmos esse cenário vamos utilizar o seguinte arquivo como início.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Treinamento Git</title>
  </head>
  <body>
    <header>
      <h1>TREINAMENTO GIT</h1>
    </header>

    <main>
    </main>

    <footer>
       Treinamento Git | Copyright 2017
    </footer>
  </body>
</html>
```

Dentro da tag main adicione qualquer texto, por exemplo `testando git checkout em arquivo`. Após isso execute o `git status`.

```shell
mcqueide:/tmp/repo/treinamentoGit $ git status
  No ramo master
  Your branch is up-to-date with 'origin/master'.
  Changes not staged for commit:
    (utilize "git add <arquivo>..." para atualizar o que será submetido)
    (utilize "git checkout -- <arquivo>..." para descartar mudanças no diretório de trabalho)

  	modified:   index.html

  nenhuma modificação adicionada à submissão (utilize "git add" e/ou "git commit -a")
```

> Quando o arquivo tiver o nome igual a de uma branch existente, você pode adicionar -- para o git saber que trata-se de um arquivo. Ficando git checkout -- nome_do_arquivo.

Note que o git já oferece algumas dicas do que você pode fazer com esse arquivo. Ele fala que podemos descartar as alterações feitas com o comando `git checkout`. Então vamos fazer isso e executar o `git status` para ver se ainda tem alguma alteração.

```shell
mcqueide:/tmp/repo/treinamentoGit $ git checkout index.html

mcqueide:/tmp/repo/treinamentoGit $ git status
  No ramo master
  Your branch is up-to-date with 'origin/master'.
  nada a submeter, diretório de trabalho vazio
```

Ao abrir o arquivo você pode notar que o arquivo foi restaurado para o estado anterior. O `git checkout` resolveu esse caso, mas e se já tivéssemos executado o `git add`? O `git checkout` não vai conseguir resolver. Então vamos ao exemplo. Vamos adicionar novamente qualquer texto na tag main. Depois adicione o arquivo mas não faça o commit e execute o `git status`.

```shell
mcqueide:/tmp/repo/treinamentoGit $ git add index.html

mcqueide:/tmp/repo/treinamentoGit $ git status
  No ramo master
  Your branch is up-to-date with 'origin/master'.
  Mudanças a serem submetidas:
    (use "git reset HEAD <file>..." to unstage)

  	modified:   index.html
```

Note que o git novamente dá dicas do que podemos fazer, para esse caso ele sugere que execute o comando `git reset HEAD nome_arquivo`, com isso o git retira o arquivo do estado para ser enviado. Se executarmos o git status novamente veremos que ele só retirou o arquivo do stage, mas não desfez as alterações, para fazer isso podemos executar novamente o `git checkout` e executar o `git status` para verificar se realmente foi desfeitas as alterações.

```shell
mcqueide:/tmp/repo/treinamentoGit $ git reset HEAD index.html
  Unstaged changes after reset:
  M	index.html

mcqueide:/tmp/repo/treinamentoGit $ git status
  No ramo master
  Your branch is up-to-date with 'origin/master'.
  Changes not staged for commit:
    (utilize "git add <arquivo>..." para atualizar o que será submetido)
    (utilize "git checkout -- <arquivo>..." para descartar mudanças no diretório de trabalho)

  	modified:   index.html

  nenhuma modificação adicionada à submissão (utilize "git add" e/ou "git commit -a")

mcqueide:/tmp/repo/treinamentoGit $ git checkout index.html

mcqueide:/tmp/repo/treinamentoGit $ git status
  No ramo master
  Your branch is up-to-date with 'origin/master'.
  nada a submeter, diretório de trabalho vazio
```

Se tivermos realizado o `commit` podemos voltar para algum estado anterior desfazendo as alterações do commit com o `git reset`. Para simularmos esse cenário, vamos utilizar o arquivo `index.html` com o seguinte estado.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Treinamento Git</title>
  </head>
  <body>
    <header>
    </header>

    <main>
    </main>

    <footer>
    </footer>
  </body>
</html>
```

Vamos adicionar um título e rodapé na página e realizar um commit.

```html
...
<header>
  Treinamento Git
</header>
...
<footer>
  Treinamento Git | CopyRight 2017
</footer>
...
```

```shell
mcqueide:/tmp/repo/treinamentoGit $ git commit -am "Adicionando título e rodapé na página"
  [master 0ed3c86] Adicionando título e rodapé na página
   1 file changed, 2 insertions(+)
```

Vamos agora adicionar algum texto no corpo da página. E realizar outro commit.

```html
...
<main>
  Testa o git reset em commit.
</main>
...
```

```shell
mcqueide:/tmp/repo/treinamentoGit $ git commit -am "Adicionando corpo na página"
  [master 09cbef2] Adicionando corpo na página
   1 file changed, 1 insertion(+)
```

Se executarmos o `git log` veremos que os dois commit estão lá. Mas digamos que não estamos satisfeitos com a última alteração enviada e desejamos desfazê-la. Para isso vamos recorrer ao comando `git reset`, passando o identificador do commit obtido no `git log` para qual queremos retornar. E depois podemos executar o `git checkout` para desfazer as alterações do arquivo.

```shell
mcqueide:/tmp/repo/treinamentoGit $ git log
  commit 09cbef21fe83420a68139ad55b9fd62dcbd9995d
  Author: John Mc.Queide <mcqueide@gmail.com>
  Date:   Sat Feb 11 11:07:47 2017 -0200

      Adicionando corpo na página

  commit 0ed3c86a97fec223d7ae11680d6a5c0086dc07ed
  Author: John Mc.Queide <mcqueide@gmail.com>
  Date:   Sat Feb 11 10:58:02 2017 -0200

      Adicionando título e rodapé na página

mcqueide:/tmp/repo/treinamentoGit $ git reset 0ed3c86a97fec223d7ae11680d6a5c0086dc07ed
  Unstaged changes after reset:
  M	index.html

mcqueide:/tmp/repo/treinamentoGit $ git log
  commit 0ed3c86a97fec223d7ae11680d6a5c0086dc07ed
  Author: John Mc.Queide <mcqueide@gmail.com>
  Date:   Sat Feb 11 10:58:02 2017 -0200

      Adicionando título e rodapé na página

mcqueide:/tmp/repo/treinamentoGit $ git checkout index.html
```

No exemplo, nós desfizemos apenas um commit, mas podemos desfazer quantos commit desejarmos, é necessário apenas passar o commit para qual desejamos retornar.

Mas e se o commit que queremos desfazer está entre outros commits? Se executarmos o `git reset`, ele irá desfazer inclusive os commits que não queremos desfazer. Para isso vamos usar o `git revert`, com ele podemos escolher apenas o commit que queremos reverter especificamente.

Para isso vamos preparar nosso teste. Vamos voltar no arquivo `index.html` para o seguinte estado novamente.

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Treinamento Git</title>
  </head>
  <body>
    <header>
    </header>

    <main>
    </main>

    <footer>
    </footer>
  </body>
</html>
```

Vamos adicionar um título na página e realizar o commit logo em seguida.

```html
...
<header>
  Treinamento Git
</header>
...
```

```shell
mcqueide:/tmp/repo/treinamentoGit $ git commit -am "Adicionando título na página"
  [master 3e19069] Adicionando título na página
   1 file changed, 1 insertion(+)
```

Agora adicionaremos um rodapé da página e fazendo o commit em seguida novamente.

```html
...
<footer>
  Treinamento Git | CopyRight 2017
</footer>
...
```

```shell
mcqueide:/tmp/repo/treinamentoGit $ git commit -am "Adicionando rodapé"
  [master 425e08d] Adicionando rodapé
   1 file changed, 1 insertion(+)
```

Vamos para a nossa terceira alteração, adicione um corpo na página agora.

```html
...
<main>
  Testando o git reset
</main>
...
```

```shell
mcqueide:/tmp/repo/treinamentoGit $ git commit -am "Adicionando corpo"
  [master 9e42004] Adicionando corpo
   1 file changed, 1 insertion(+)
```

Mas não estamos satisfeitos com a mensagem que colocamos no rodapé, e queremos desfazer essa alteração. O problema é que ela está entre a alteração do título e do corpo. Então vamos utilizar o `git revert` para desfazer apenas o commit do rodapé. Para iniciar execute o `git log` para obtermos o identificador do commit, e depois execute o `git revert`.

```shell
mcqueide:/tmp/repo/treinamentoGit $ git log
  commit 9e42004c4a873646bfc692f705c4ae43f5d34c34
  Author: John Mc.Queide <mcqueide@gmail.com>
  Date:   Sat Feb 11 11:32:50 2017 -0200

      Adicionando corpo

  commit 425e08dbe86907dd157ba37e7907d028b232fb79
  Author: John Mc.Queide <mcqueide@gmail.com>
  Date:   Sat Feb 11 11:30:06 2017 -0200

      Adicionando rodapé

  commit 3e19069c9b4f45cca30bee93637d542765b5eddf
  Author: John Mc.Queide <mcqueide@gmail.com>
  Date:   Sat Feb 11 11:27:10 2017 -0200

      Adicionando título na página

mcqueide:/tmp/repo/treinamentoGit $ git revert 425e08dbe86907dd157ba37e7907d028b232fb79
```

O git irá abrir um arquivo temporário para passarmos uma mensagem de commit.

```shell
Revertendo o commit "Adicionando rodapé"

This reverts commit 425e08dbe86907dd157ba37e7907d028b232fb79.

# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# No ramo master
# Seu ramo está à frente de 'origin/master' por 6 submissões.
#   (use "git push" to publish your local commits)
#
# Mudanças a serem submetidas:
#       modified:   index.html
#
```

> Lembrando que o que está com # no início da linha não irá na mensagem de commit, por que é linha comentada.

Depois se executarmos o `git log` novamente veremos que temos um commit de revert, e nosso arquivo ficou sem as alterações realizada no commit revertido.

```shell
mcqueide:/tmp/repo/treinamentoGit $ git log
  commit fbdb67f8441b52734458784af252ea439d50c00e
  Author: John Mc.Queide <mcqueide@gmail.com>
  Date:   Sat Feb 11 11:38:58 2017 -0200

      Revertendo o commit "Adicionando rodapé"

      This reverts commit 425e08dbe86907dd157ba37e7907d028b232fb79.

  commit 9e42004c4a873646bfc692f705c4ae43f5d34c34
  Author: John Mc.Queide <mcqueide@gmail.com>
  Date:   Sat Feb 11 11:32:50 2017 -0200

      Adicionando corpo

  commit 425e08dbe86907dd157ba37e7907d028b232fb79
  Author: John Mc.Queide <mcqueide@gmail.com>
  Date:   Sat Feb 11 11:30:06 2017 -0200

      Adicionando rodapé

  commit 3e19069c9b4f45cca30bee93637d542765b5eddf
  Author: John Mc.Queide <mcqueide@gmail.com>
  Date:   Sat Feb 11 11:27:10 2017 -0200

      Adicionando título na página
```

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Treinamento Git</title>
  </head>
  <body>
    <header>
      Treinamento Git
    </header>

    <main>
      Testando o git reset
    </main>

    <footer>
    </footer>
  </body>
</html>
```

## Backup de alterações que não foram enviado por commit

E quando temos alterações realizadas em algum arquivos e que não foram enviadas no commit ainda, e queremos realizar o `git pull`, o git apresenta um alerta, impedindo de realizarmos a operação, explicando que nossas alterações podem ser perdidas. O que podemos fazer para resolver isso? Uma alternativa seria enviar essas alterações com o commit, mas e se não quisermos enviar essas alterações ainda porque elas não estão finalizadas. Então que tal fazer o backup das alterações em um outro arquivo e depois executar o `git checkout` para desfazer as alterações no arquivo versionado, realizar o `git pull`, e então trazer as alterações nas quais estamos trabalhando de volta para o arquivo. Isso resolveria o problema, mas será que o git não tem uma forma mais fácil para resolvermos esse cenário?

Apresento o `git stash`, ao executá-lo o git move todos os arquivos que estão modificados para um local separado. Deixando o repositório local sem modificações e pronto para fazer a operação de atualização.

O nosso usuário **macaulay** ficou um tempo sem atualizar seu repositório, e agora quer atualizar ele só que o git não deixa.

```shell
macaulay:/tmp/repo/treinamentoGit $ git pull
  remote: Counting objects: 17, done.
  remote: Compressing objects: 100% (5/5), done.
  remote: Total 17 (delta 7), reused 17 (delta 7), pack-reused 0
  Unpacking objects: 100% (17/17), done.
  From https://github.com/mcqueide/treinamentoGIT
     d717d81..fbdb67f  master     -> origin/master
  Updating d717d81..fbdb67f
  error: Your local changes to the following files would be overwritten by merge:
  	index.html
  Please, commit your changes or stash them before you can merge.
  Aborting
```

Porém ele não finalizou seu trabalho ainda e não quer enviar ele incompleto. Note que o git dá uma dica, ele avisa o usuário para realizar o commit das alterações ou realizar o stash delas. Vamos realizar o `stash` e em seguida o `git status` para verificar o estado dos arquivos.

```shell
macaulay:/tmp/repo/treinamentoGit $ git stash
  Saved working directory and index state WIP on master: d717d81 Atualizando o rodapé do documento
  HEAD is now at d717d81 Atualizando o rodapé do documento

macaulay:/tmp/repo/treinamentoGit $ git status
  No ramo master
  Seu ramo está atrás de 'origin/master' em 7 submissões, e pode ser avançado.
    (use "git pull" to update your local branch)
  nada a submeter, diretório de trabalho vazio
```

Como falado antes o git fez uma cópia dos arquivos, e deixou o repositório inalterado, com isso podemos realizar o `git pull`.

```shell
macaulay:/tmp/repo/treinamentoGit $ git pull
  Updating d717d81..fbdb67f
  Fast-forward
   index.html | 5 ++---
   1 file changed, 2 insertions(+), 3 deletions(-)
```

Agora o git busca as alterações e aplica no repositório local. Mas agora como usuário recupera a cópia dos seus arquivos realizado pelo git. Com o comando `git stash list`, o git apresenta os stash disponíveis.

```shell
macaulay:/tmp/repo/treinamentoGit $ git stash list
stash@{0}: WIP on master: d717d81 Atualizando o rodapé do documento
```

Para aplicarmos as alterações novamente, temos duas alternativas, podemos fazer com o `git stash apply` ou `git stash pop`. A diferença entre os dois é que o apply apenas aplica as modificações deixando o stash ainda disponível e esse pode ser removido depois com o `git stash drop`, enquanto o pop recupera e descarta o último stash. Vamos fazer o teste com o **apply**.

```shell
macaulay:/tmp/repo/treinamentoGit $ git stash apply stash@{0}
  No ramo master
  Seu ramo está à frente de 'origin/master' por 1 submissão.
    (use "git push" to publish your local commits)
  Changes not staged for commit:
    (utilize "git add <arquivo>..." para atualizar o que será submetido)
    (utilize "git checkout -- <arquivo>..." para descartar mudanças no diretório de trabalho)

  	modified:   index.html

  nenhuma modificação adicionada à submissão (utilize "git add" e/ou "git commit -a")
```

Depois de aplicar podemos remover o `stash` com o comando `git stash drop`.

```shell
macaulay:/tmp/repo/treinamentoGit $ git stash drop stash@{0}
  Dropped stash@{0} (3e54be25fdda2eee3e55c463e25e167ff4af1422)
```

Para pegarmos o último `stash` e já removê-lo logo em seguida podemos usar o `git stash pop`.

```shell
macaulay:/tmp/repo/treinamentoGit $ git stash pop
  No ramo master
  Seu ramo está à frente de 'origin/master' por 1 submissão.
    (use "git push" to publish your local commits)
  Changes not staged for commit:
    (utilize "git add <arquivo>..." para atualizar o que será submetido)
    (utilize "git checkout -- <arquivo>..." para descartar mudanças no diretório de trabalho)

  	modified:   index.html

  nenhuma modificação adicionada à submissão (utilize "git add" e/ou "git commit -a")
  Dropped refs/stash@{0} (d65e6496144c2fbd76b29e85ed278f70a1dea6c0)
```

Se executarmos o `git stash list`, veremos que não tem nenhum stash.

## Configurando aliases

Sabe aquele dica que o professor de matemática falava só depois que ele explicou todo o conteúdo? Então agora vou apresentá-los o **alias**. O alias é um apelido que você escolhe para um comando git ou a execução de uma cadeia de comandos, tornando aquele comando menos verboso, mais claro para você, ou deixá-lo de uma forma que você já esteja acostumado, caso já use alguma outra ferramenta de versionamento.

Para fazer configuração de aliases, devemos alterar nosso arquivo `.gitconfig`, e se ele não existir podemos apenas criar e configurá-lo. O local desse arquivo é na pasta do seu usuário no sistema operacional. Se for windows ele estará em `C:\Users\seu_usuario\.gitconfig`, se for linux ou mac você pode encontrá-lo em `/home/seu_usuario/.gitconfig`.

Para configurar nosso primeiro alias, vamos abrir o arquivo `.gitconfig` e adicionar uma nova linha com **[alias]** caso não exista, com isso é como se tivéssemos adicionando umas nova seção de configuração no nosso arquivo. Abaixo dessa linha iremos configurar nossos aliases.

O primeiro a ser configurado será o alias para commit, para quem já usou o SVN, talvez esteja acostumado a verificar o status dos arquivos com `st`.

```shell
[alias]
	st = status
```

Agora toda vez que você for querer verificar o status dos arquivos no seu repositório local é só executar o comando `git st`. Se você quiser adicionar outros aliases, é só criar novas linhas e passar o alias e seu comando respectivo. Como no exemplo a seguir.

```shell
[alias]
	st = status
	ci = commit
	br = branch
	df = diff
```

Além de aliases para simples comandos, podemos criar para execução de vários comando seguidos, para isso é só adicionar `!` no começo da linha e encadear os comandos com `&&`, e os comandos serão executados na ordem em que foram declarados um após o outro. Isso pode facilitar por exemplo o processo de envio dos arquivos modificados para a branch master, antes tínhamos que fazer o checkout para a branch master, atualizar ela, depois voltar para a branch particular, rodar o rebase para então voltar para a branch master novamente, realizar o merge e enviar os arquivos com o git push. Para simplificarmos todo esse processo podemos apenas executar `git envia`. Sério, para isso, só precisamos configurar o alias.

```shell
[alias]
	envia = !git checkout master && git pull && git checkout sua_branch && git rebase master sua_branch && git checkout master && git merge sua_branch && git push
```

> Lembrando que isso não isenta o usuário de fazer tratamentos de conflitos manuais, se houver algum conflito a execução será interrompida para ser realizado o tratamento.

Agora para realizar o processo que precisávamos de vários comando, precisamos apenas executar `git envia`.

## Conclusão

Nesse artigo vimos algumas operações mais avançadas. Espero ter ajudado a remover o medo que muitos possuem nas operações de merge, e que tirem um bom proveito do uso das **chaves SSH**. Mas não vimos tudo o que o git nos permite fazer ainda. Para isso, o git conta com uma boa documentação com exemplos inclusive. A documentação do Git está disponível nesse **[link](https://git-scm.com/doc)**. Espero que tenham gostado e até o próximo artigo.

### Escrito por [John Mc.Queide](https://linkedin.com/in/mcqueide/en)