# Guia de utilização do Gitea

## Criação de usuário

Crie um usuário no _Gitea_ logando-se via fonte de autenticação corporativa.

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
vi Dockerfile
```

```dockerfile
FROM alpine:latest
CMD ["echo", "Alo Gitea!"]
```

```bash
# Substitua <package registry token> pelo token de acesso ao package registry
export GITEA_TOKEN=<package registry token>
docker build -t hello-gitea .
# Substitua pelo host e usuário do Gitea
docker tag hello-gitea forge-gitea.global.staging.platform-tools.ingress.sh/gilberto.mautner/hello-gitea:v1
docker login forge-gitea.global.staging.platform-tools.ingress.sh \
  -u nobody \
  -p $GITEA_TOKEN
docker push forge-gitea.global.staging.platform-tools.ingress.sh/gilberto.mautner/hello-gitea:v1
```

Confira no _Gitea_ clicando no seu usuário e depois em `Packages`.

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

Nas chaves `registry` e `tags`, substitua `forge-gitea.global.staging.platform-tools.ingress.sh` pelo host do seu Gitea. Na chave `tags`, substitua `gilberto.mautner` pelo seu usuário do Gitea.

Observe que o arquivo `.github/workflows/ci.yml` utiliza um segredo chamado `PACKAGE_REGISTRY_TOKEN`. Para criar o segredo no _Gitea_, acesse o repositório, clique em `Settings` -> `Actions` -> `Secrets` e adicione um novo segredo chamado `PACKAGE_REGISTRY_TOKEN` com o valor do token de acesso ao package registry criado anteriormente.

Prossiga com `git push` para o repositório. Observe, no _Gitea_, que a Actions foi acionada e que o pacote foi publicado.

### Habilitando Renovate Bot

Clique em `Settings` -> `Collaborators` e adicione `renovate-bot` com direito de `Write`.

### Vinculando um pacote a um repositório

No menu direito superior, clique no seu avatar e `Profile`. Selecione `Packages` e clique no pacote a ser vinculado. Selecione `Settings` -> `Link to repository` e selecione o repositório a ser vinculado.


