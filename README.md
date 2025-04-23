- [Guia de utilização do Gitea](#guia-de-utilização-do-gitea)
  - [Endereço](#endereço)
  - [Criação de usuário](#criação-de-usuário)
    - [Cadastro de chave SSH](#cadastro-de-chave-ssh)
  - [Package registry](#package-registry)
    - [Token de acesso ao package registry](#token-de-acesso-ao-package-registry)
    - [Publicação de pacotes no package registry](#publicação-de-pacotes-no-package-registry)
    - [Tornando o package registry privado](#tornando-o-package-registry-privado)
  - [Criação de repositório](#criação-de-repositório)
    - [Acesso via VS Code ou Cursor](#acesso-via-vs-code-ou-cursor)
    - [Configuração de Actions](#configuração-de-actions)
    - [Habilitando Renovate Bot](#habilitando-renovate-bot)
    - [Vinculando um pacote a um repositório](#vinculando-um-pacote-a-um-repositório)
  - [Configuração de acessos](#configuração-de-acessos)
    - [Criando uma organização](#criando-uma-organização)
    - [Criando um time na organização](#criando-um-time-na-organização)
    - [Adicionando membros ao time](#adicionando-membros-ao-time)
    - [Adicionando um repositório a uma organização](#adicionando-um-repositório-a-uma-organização)
    - [Adicionando um time a um repositório](#adicionando-um-time-a-um-repositório)

# Guia de utilização do Gitea

## Endereço

https://gitea.platform.lwsa.tech

## Criação de usuário

Crie um usuário no _Gitea_ clicando em `Sign in` e depois em `Sign in with gitea-prod`. 

### Cadastro de chave SSH

Clique em `User Settings` -> `SSH / GPG Keys` e cadastre a parte pública da sua chave SSH.

## Package registry

### Token de acesso ao package registry

Clique em `User Settings` -> `Applications` -> `Manage Access Tokens` e crie um novo token de acesso.

Em `Token Name` coloque `Package Registry Token`. Clique em `Select permissions` e marque `package`: `Read and Write`. Pressione o botão `Generate Token` e copie o valor do token.

### Publicação de pacotes no package registry

```bash
mkdir hello-gitea
cd hello-gitea
```

Crie um arquivo chamado `Dockerfile` com o seguinte conteúdo:

```dockerfile
FROM alpine:latest
CMD ["echo", "Alo Gitea!"]
```

```bash
# Substitua <package registry token> pelo token de acesso ao package registry
export GITEA_TOKEN=<package registry token>
docker build -t hello-gitea .
# Substitua pelo seu usuário do Gitea
docker tag hello-gitea gitea.platform.lwsa.tech/gilberto.mautner/hello-gitea:v1
docker login gitea.platform.lwsa.tech \
  -u nobody \
  -p $GITEA_TOKEN
# Substitua pelo seu usuário do Gitea
docker push gitea.platform.lwsa.tech/gilberto.mautner/hello-gitea:v1
```

Confira no _Gitea_ clicando no seu usuário e depois em `Packages`.

### Tornando o package registry privado

No modelo de acesso do _Gitea_, a visibilidade de pacotes depende da visibilidade do usuário. Por _default_, o usuário é público, de forma que os pacotes associados a ele também são públicos. 

Para tornar o package registry privado, é necessário alterar a visibilidade do usuário.

No seu avatar, clique em `Settings` -> `Profile` e altere `User Visibility` para `Limited`.

Com isso, imagens de containers publicadas no package registry passam a ser privadas, podendo ser acessadas usando o token de acesso ao package registry.

## Criação de repositório

Criaremos um repositório migrando a partir de um repositório existente no GitHub.

No canto superior direito, clique em `+` e `New Migration`.

Em `Clone from URL`, preencha com: `https://github.com/gmautner/python-web-app.git`.

Em `Repository Name`, preencha com: `python-web-app`.

Em `Visibility`, selecione `Make repository private`.

Clique em `Migrate Repository`.

### Acesso via VS Code ou Cursor

Na página principal do repositório recém criado, clique em `Code`, `SSH` e copie o link do repositório. Com este link abra o repositório no VS Code ou Cursor.

### Configuração de Actions

Edite o arquivo `.github/workflows/ci.yml` no VS Code ou Cursor.

Na chave `images`, substitua `gilberto.mautner` pelo seu usuário do Gitea.

Observe que o arquivo `.github/workflows/ci.yml` utiliza um segredo chamado `PACKAGE_REGISTRY_TOKEN`. Para criar o segredo no _Gitea_, acesse o repositório, clique em `Settings` -> `Actions` -> `Secrets` e adicione um novo segredo chamado `PACKAGE_REGISTRY_TOKEN` com o valor do token de acesso ao package registry criado anteriormente.

Prossiga com `git push` para o repositório. Observe, no _Gitea_, que a Actions foi acionada e que o pacote foi publicado.

### Habilitando Renovate Bot

Clique em `Settings` -> `Collaborators` e adicione `renovate-bot` com direito de `Write`.

### Vinculando um pacote a um repositório

No menu direito superior, clique no seu avatar e `Profile`. Selecione `Packages` e clique no pacote a ser vinculado. Selecione `Settings` -> `Link to repository` e selecione o repositório a ser vinculado.

## Configuração de acessos

### Criando uma organização

No canto superior direito, clique em `+` e `New Organization`.

Preencha `Organization Name` com o nome da organização, exemplo `bu-org`.

Em `Visibility`, selecione `Limited`. Mantenha marcado `Repository admin can add or remove access for teams`.

Clique em `Create Organization`.

### Criando um time na organização

Na página da organização, clique em `New Team`. Escolha um nome para o time, exemplo `devops`.

Mantenha `Repository access`: `Specific repositories`. 

Para que membros do time tenham acesso de escrita aos repositórios, selecione `Permission`: `Administrator Access`.

Para um controle mais granular, selecione `Permission`: `General Access` e escolha as permissões aplicáveis.  

Clique em `Create Team`.

### Adicionando membros ao time

Basta clicar em `Add Team Member`

### Adicionando um repositório a uma organização

Na página da organização, clique em `New Repository`.

### Adicionando um time a um repositório

Na página do repositório acesse `Settings` -> `Collaborators` e selecione `Add Team` para adicionar o time desejado.