# Versionamento com Git

## Sumário
  * [Introdução](#introduo)
  * [Dando os primeiros passos com GIT](#dando-os-primeiros-passos-com-git)
  * [Começando trabalhar com repositório remoto](#comeando-trabalhar-com-repositrio-remoto)
  * [Trabalhando com branches](#trabalhando-com-branches)
  * [Retornando conteúdo da branch de desenvolvimento para a master](#retornando-contedo-da-branch-de-desenvolvimento-para-a-master)
  * [Conclusão](#concluso)

## Introdução

Quando trabalhamos em equipe, podem ocorrer vezes em que temos que trabalhar numa mesma funcionalidade, portanto acontece vezes que realizamos alterações num mesmo arquivo, ou até mesmo em um mesmo trecho de código. Então como resolver essas situações, devemos combinar com os colegas de equipe quem irá alterar o arquivo primeiro, para só assim enviar o arquivo com as novas alterações para quem estiver aguardando na fila?

As ferramentas de controle de versão vêm para resolver esse e outros problemas, onde uma ou mais pessoas irão trabalhar com suas cópias de arquivos local, e enviar elas para um servidor centralizado que é responsável por controlar as modificações e versões desses arquivos. A ferramenta irá verificar se o arquivo está atualizado com a última versão disponível no servidor, porque se não tiver, o usuário terá que atualizá-lo para só assim submeter suas modificações, garantindo que o que está no servidor sempre está atualizado.

Além de controlar a versão dos arquivos, os controles de versão mantêm um log das alterações realizadas nos arquivos, mostrando os arquivos alterados, mensagem descritiva da operação e quem a realizou. Outra vantagem é que também funciona como uma forma de cópia de segurança, onde podemos recuperar um determinado arquivo em um determinado estado no passado.

O mercado de controle de versão já conta com bastantes soluções, algumas bastante usadas são o SVN e o GIT, aqui trataremos do GIT.

## Dando os primeiros passos com GIT

Você pode obter o GIT diretamente no site oficial, [download Git](https://git-scm.com/download), que possui versões para os principais sistemas operacionais Windows, Linux e Mac. Caso você seja usuário de Linux, o site já mostra o processo de instalação para as principais distribuições, por linha de comando de uma forma bem simples.

Se você estiver na plataforma Windows você pode realizar os comandos do git no terminal Git Bash, que é instalado junto com o instalação do Git, se você for usuário de Linux ou Mac, pode fazer uso do terminal do seu sistema operacional.

Para iniciarmos um repositório git em nossa máquina podemos executar o comando: `git init`. Com isso será apresentado na nossa tela uma mensagem semelhante a essa:

```shell
$ git init
  Initialized empty Git repository in /tmp/treinamentoGIT/.git/.
```

Simples assim, a parti de agora esse diretório é um repositório local Git.

Agora vamos adicionar um arquivo à essa pasta, crie um arquivo `.html` ou qualquer outro arquivo nessa pasta.

Aqui criei um `index.html`:

```html
<!DOCTYPE html>
<html>
<head>
	<title>Treinamento GIT</title>
</head>
<body>
	<h1>Treinamento GIT</h1>
</body>
</html>
```

Nós criamos o arquivo porém não informamos ainda para o Git gerenciá-lo. Se executarmos o `git status`, nós veremos nosso arquivo, e o git informará que nosso arquivo `index.html` não é um arquivo monitorado.

```shell
$ git status
  No ramo master

  Submissão inicial.

  Arquivos não monitorados:
    (utilize "git add <arquivo>..." para incluir o que será submetido)

  	index.html

  nada adicionado ao envio mas arquivos não registrados estão presentes (use "git add" to registrar)
```

Para mudarmos sua situação podemos executar o comando `git add index.html`. Agora nosso arquivo está pronto para ser submetido.

```shell
$ git add index.html
  No ramo master

  Submissão inicial.

  Mudanças a serem submetidas:
    (utilize "git rm --cached <arquivo>..." para não apresentar)

  	new file:   index.html
```

Com isso agora conseguimos realizar o commit de nossas alteração com o comando `git commit`, em seguida será aberto um arquivo de texto temporário no seu terminal semelhante a esse:

```shell
GNU nano 2.5.3                            Arquivo:/tmp/treinamentoGIT/.git/COMMIT_EDITMSG                                                                                                                       

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

Você não precisa se preocupar com essas linhas com **#**, elas não irão aparecer na sua mensagem de commit. Elas são meramente descritiva, informam os arquivos que estão sendo submetidos, assim como o estado da alteração, se é um arquivo novo, se é alteração ou exclusão. Pode digitar a mensagem que você achar mas apropriada para o que você realizou, por exemplo **Meu primeiro commit**. Agora feche o arquivo. Agora irá aparecer algo semelhante a isso no seu terminal:

```shell
[master (root-commit) 94cccf2] Meu primeiro commit
 1 file changed, 11 insertions(+)
 create mode 100644 index.html
```

Porém se for a primeira vez que você utiliza o Git na máquina então você receberá uma mensagem assim antes:

```shell
*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.
```

Isso aparece porque o git não sabe quem é o usuário, e ele já adianta uma dica de como resolver isso, a ferramenta pede para você executar os comandos :

```shell
$ git config --global user.email "you@example.com"
$ git config --global user.name "Your Name"
```

Se você quer que seu usuário fique configurado para todos os repositórios do seu usuário da máquina então você pode executar o comando oferecido pelo Git, mas se você quiser que essas informações sejam atribuídas ao repositório específico  no qual está trabalhando então execute os comandos sem o **- -global**:

```shell
$ git config user.email "you@example.com"
$ git config user.name "Your Name"
```

Para verificar que seu commit foi realmente realizado execute o comando `git log`.

```shell
$ git log
commit 94cccf23026b15f78d1c11ce3ae79351a3e30eb6
Author: seu_usuario <seu_usuario@github.com>
Date:   Thu Jan 26 13:28:31 2017 -0200

    Meu primeiro commit
```

Isso mostra os detalhes do commit realizado, na primeira linha temos o hash do commit, na segunda o usuário e e-mail do usuário que realizou a alteração, na terceira temos a data em que foi realizada.

Nós temos outra opção ao realizar o commit, nós podemos realizá-lo já adicionando todos os arquivos para serem comitados. Para isso vamos adicionar um novo parágrafo no nosso arquivo.

```html
<!DOCTYPE html>
<html>
<head>
  <title>Treinamento GIT</title>
</head>
<body>

  <h1>Treinamento GIT</h1>

  <p>Está página tem como objetivo servir de teste para as operação do GIT</p>

</body>
</html>
```

Com isso vamos realizar mais um commit, dessa vez vamos utilizar o parâmetro -am já adicionando todos os arquivo e passando a mensagem já na operação de commit, mas antes vamos verificar o estado do nosso repositório com o `git status`:

```shell
$ git status

No ramo master
Changes not staged for commit:
  (utilize "git add <arquivo>..." para atualizar o que será submetido)
  (utilize "git checkout -- <arquivo>..." para descartar mudanças no diretório de trabalho)

	modified:   index.html

nenhuma modificação adicionada à submissão (utilize "git add" e/ou "git commit -a")
```

Dessa vez percebemos que o estado do nosso arquivo está `modified` já que ele foi modificado. Vamos realizar o commit:

```shell
$ git commit -am "Adicionado parágrafo na página"

[master 2d1a2cf] Adicionado título na página
 1 file changed, 2 insertions(+)
```

> Perceba que o `git commit -am "mensagem"` é o mesmo que estivéssemos executando `git add --all` e depois o `git commit -m "mensagem"` em seguida.

## Começando trabalhar com repositório remoto

Se executarmos o `git log` veremos todos os commits realizados. Porém todas as tarefas realizadas ainda estão apenas em nosso computador, como outros usuários irão colaborar com o nosso projeto? O git trabalha de duas forma, com o repositório local, e com o remoto. O local, como dá-se a entender, está disponível apenas em sua máquina, o remoto fica disponível para a rede, onde qualquer usuário que tenha permissão de acesso podem obter ou enviar cópias de seus arquivos, deixando disponível para todos.

Existem alguns repositórios livres na internet para que você possa submeter seus arquivos, um bastante famoso é o [Git Hub](http://github.com), ele é um repositório livre e grátis, na forma grátis você tem um limite de espaço e seu repositório fica aberto para todos da comunidade verem, e se tiver interesse, tem opção de adicionar colaboradores para o seu projeto, dando assim permissão que outros contribuam com seu projeto. A versão paga, garante uma privacidade dos seus fontes e um espaço maior de repositório.

![Criando novo repositório no Git Hub](../assets/img/post-git/create-new-repository-github.png "Criando um novo repositório")

Nesse material faremos uso do Git Hub, mas sinta-se livre para utilizar qualquer outro repositório remoto da sua preferência. Primeiro passo, crie uma conta se você ainda não tiver e depois um repositório treinamentoGIT, agora vamos enviar nossas modificações para nosso repositório remoto, para isso vamos adicionar esse repositório remoto no nosso repositório local. Para isso vamos executar primeiramente o comando `git remote`, que mostra os repositórios remotos em nossa máquina. Como ainda não temos nenhum, nada irá aparecer. Então vamos adicionar o primeiro:

```shell
$ git remote add origin http://github.com/seu_usuario_no_github/treinamentoGIT.git
```

> O `origin` no comando é o nome que estamos dando para esse repositório remoto, a `url` em seguida é o endereço do nosso repositório remoto.

Feito isso se executarmos o comando `git remote` novamente veremos que aparece um repositório `origin` na nossa linha de comando:

```shell
$ git remote

origin
```

Agora que adicionamos um repositório remoto, podemos enviar nosso código para esse repositório com o comando git push. Como será nosso primeiro envio devemos informar para qual repositório remoto e qual branch estamos enviando os arquivos. Para isso usamos o comando `git push origin master`. Depois de executar o comando o git perguntará qual seu usuário e senha desse repositório remoto.

> Para ficar bem claro o `origin` é o nome do repositório remoto que adicionamos, o `master` é o nome da branch.

```shell
$ git push origin master

Username for 'http://github.com': digite_seu_usuário
Password for 'http://seu_usuario@github.com’:
Counting objects: 6, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (6/6), 555 bytes | 0 bytes/s, done.
Total 6 (delta 1), reused 0 (delta 0)
To http://github.com/treinamentoGIT.git
 * [new branch]      master -> master
```

Nós fizemos o cenário de quando criamos nosso repositório local e enviamos ele para um repositório remoto, mas e se formos colaborar com um projeto já existente, que já possua um repositório remoto? Então o primeiro passo será obter ele. Para fazermos isso, basta ir para um diretório que achar mais conveniente e executar o comando `git clone`. Com isso o git irá criar um repositório local na sua máquina com todos os arquivos e histórico de modificações (log) desse repositório.

![Como clonar repositório GitHub](../assets/img/post-git/how-to-clone-git-repository.png "Como clonar repositório GitHub")

```shell
$ git clone http://github.com/treinamentoGIT.git
  Cloning into 'treinamentoGIT'...
  Username for 'http://github.com': seu_usuario
  Password for 'http://seu_usuario@github.com':
  remote: Counting objects: 6, done.
  remote: Compressing objects: 100% (4/4), done.
  remote: Total 6 (delta 1), reused 0 (delta 0)
  Unpacking objects: 100% (6/6), done.
  Checking connectivity... done.
```

## Trabalhando com branches

Quando trabalhamos com versionamento de controle, normalmente isolamos nossa linha de desenvolvimento da linha de produção, branch master, que é uma linha onde as funcionalidades já estão implementadas completamente. Por isso é comum criarmos uma branch para que sirva para o desenvolvimento de funcionalidades enquanto as mesmas não estão finalizadas. Para isso vamos criar uma nova branch desenvolvimento, primeiro vamos visualizar as branches locais em nossa máquina com o comando `git branch`, o git irá responder que temos apenas a branch master. Para criamos uma nova branch execute:

```shell
$ git branch desenvolvimento
```

Ao executar novamente `git branch`, agora é retornado as branches master e desenvolvimento, e o git ainda informa qual branch está em uso no momento destacando ela com um asterisco.

```shell
$ git branch

desenvolvimento
* master
```

Para mudarmos para nossa branch de desenvolvimento devemos executar o comando `git checkout desenvolvimento`. Depois disso já estaremos na nossa outra branch.

> Normalmente quando criamos uma branch queremos mudar para ela logo em seguida. podemos fazer isso com o comando `git checkout -b desenvolvimento`

Agora vamos continuar nosso trabalho. Na nossa linha de desenvolvimento, vamos alterar nosso arquivo `index.html`, adicionando um novo parágrafo `<p>Começando o desenvolvimento na branch de desenvolvimento</p>`:

```html
<!DOCTYPE html>
<html>
<head>
  <title>Treinamento GIT</title>
</head>
<body>

  <h1>Treinamento GIT</h1>

  <p>Começando o desenvolvimento na branch de desenvolvimento</p>

</body>
</html>
```

Vamos realizar o commit e enviar as alterações para nosso repositório.

```shell
$ git add index.html
$ git commit -m "Criando a branch de desenvolvimento"
  [master 7dd7472] Criando a branch de desenvolvimento
   1 file changed, 4 insertions(+), 2 deletions(-)
$ git push origin desenvolvimento
  Username for 'http://github.com': user_name
  Password for 'http://user_name@github.com':
  Total 0 (delta 0), reused 0 (delta 0)
  remote:
  remote: To create a merge request for desenvolvimento, visit:
  remote:   http://github.com/treinamentoGIT/merge_requests/new?merge_request%5Bsource_branch%5D=desenvolvimento
  remote:
  To http://github.com/treinamentoGIT.git
   * [new branch]      desenvolvimento -> desenvolvimento
```

Feito isso, o git cria nossa nova branch, desenvolvimento, no repositório remoto juntamente com o commit realizado. Vamos realizar um `git pull` para atualizar nosso repositório e verificar se o git realmente criou nossa branch remota. Ao realizar o comando, o git informa que não sabe a qual branch no repositório remoto se refere a nossa branch de desenvolvimento local.

```shell
$ git pull
  Username :
  Password :
  There is no tracking information for the current branch.
  Please specify which branch you want to merge with.
  See git-pull(1) for details.

    git pull <remote> <branch>

  If you wish to set tracking information for this branch you can do so with:

    git branch --set-upstream-to=origin/<branch> master
```

Para corrigirmos isso podemos vincular nossa branch de desenvolvimento a nossa branch de desenvolvimento no repositório remoto.

```shell
$ git push -u origin desenvolvimento
  Username :
  Password :
  Branch desenvolvimento set up to track remote branch desenvolvimento from origin.
  Everything up-to-date
```

Com isso o git informa que nossa branch desenvolvimento local está track com a branch remota desenvolvimento, e agora podemos realizar o comando git pull livremente.

Agora, e se tivermos uma branch no repositório remoto que não temos no nosso local? Se executarmos o `git branch nome_da_branch` ou `git branch -b nome_da_branch`, estaremos apenas criando uma branch no nosso repositório local e o git em momento nenhum saberá que ela deveria estar ligada a alguma branch remota.

Para criarmos uma branch local que esteja relacionado com uma branch remota devemos executar o comando `git branch -t nome_da_branch origin/nome_da_branch_remota`.

Para fazermos o teste vamos para um outro diretório qualquer, por exemplo `/tmp`, e faça o clone do seu projeto, depois execute o comando `git branch` para visualizar quais branches locais estão nesse repositório, feito isso execute o comando `git branch -r` para verificar as branches remotas. Vamos criar nossa branch local apontando para nosso repositório remoto `origin` e para a branch `desenvolvimento`.

```shell
/tmp $ git clone http://github.com/treinamentoGIT.git
$ cd treinamentoGIT
/tmp/treinamentoGIT $ git branch
  * master
/tmp/treinamentoGIT $ git branch -r
    origin/HEAD -> origin/master
    origin/desenvolvimento
    origin/master
/tmp/treinamentoGIT $ git branch -t desenvolvimento origin/desenvolvimento
  Branch desenvolvimento set up to track remote branch desenvolvimento from origin.
/tmp/treinamentoGIT $ git branch
    desenvolvimento
  * master
```

## Retornando conteúdo da branch de desenvolvimento para a master

Nós finalizamos, e agora como retornar o conteúdo da branch desenvolvimento para nossa branch master? Para realizarmos isso teremos que primeiro ir para nossa branch master e atualizar ela. Isso é necessário quando estamos desenvolvendo com outras pessoas num mesmo repositório, então nossa master local pode estar desatualizada em relação a master remota, além disso temos que trazer os novos commits  realizados nela para nossa branch desenvolvimento também de forma que ela fique sincronizada com a master antes de realizarmos a operação de merge.

Primeiro vamos mudar para nossa branch master com o `git checkout master`, depois atualizamos ela com o `git pull`. Feito isso, temos que atualizar nossa branch de desenvolvimento como o novo conteúdo da master, para realizar isso temos duas opções, podemos fazer uso do `git merge`, porém com ele poderemos sujar nosso histórico de modificações com uma operação a mais de merge caso haja algum conflito de arquivos, então a forma mais indicada é utilizar o `git rebase`, com ele conseguiremos fazer a atualização da nossa branch de desenvolvimento sem a necessidade de um commit de merge. Depois temos que voltar para nossa branch master para realizar o merge da branch de desenvolvimento, dessa vez não haverá conflitos porque nossa branch de desenvolvimento já está com todos os commits da master, por causa do rebase. Feito o merge, só nos falta realizar o `git push` para enviar as atualização da master local para a remota.

```shell
$ git checkout master
  Switched to branch 'master'
  Seu ramo está à frente de 'origin/master' por 1 submissão.
    (use "git push" to publish your local commits)
$ git pull
  Username for 'http://github.com': seu_usuario
  Password for 'http://seu_usuario@github.com':
  Already up-to-date.
$ git checkout desenvolvimento
  Switched to branch 'desenvolvimento'
  Your branch is up-to-date with 'origin/desenvolvimento'.
$ git rebase master desenvolvimento
  First, rewinding head to replay your work on top of it...
  Fast-forwarded desenvolvimento to master.
$ git checkout master
  Switched to branch 'master'
  Seu ramo está à frente de 'origin/master' por 1 submissão.
    (use "git push" to publish your local commits)
$ git merge desenvolvimento
  Already up-to-date.
$ git push
  Username for 'http://github.com': seu_usuario
  Password for 'http://seu_usuario@github.com':
  Total 0 (delta 0), reused 0 (delta 0)
  To http://github.com/desenvolvimento/treinamentoGIT.git
     2d1a2cf..7dd7472  master -> master
```

## Conclusão

Esse material apresentou algumas operações básicas do Git, mostrando como manter um repositório local e remoto. Para não estendê-lo tanto, teremos a continuação dele em um próximo post, onde abordaremos alguns outros cenários, como escrita concorrente em um mesmo arquivo e até mesmo no mesmo trecho do arquivo, realizando assim as famosas e temidas operações de merge.

### Escrito por [John Mc.Queide](https://linkedin.com/in/mcqueide/en)