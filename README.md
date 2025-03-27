# Gitea

## Uso

### Criação de usuário

Crie um usuário no _Gitea_ logando-se via fonte de autenticação corporativa.

### Cadastro de chave SSH

Clique em `User Settings` -> `SSH / GPG Keys` e cadastre a parte pública da sua chave SSH.

### Package registry

#### Token de acesso ao package registry

Clique em `User Settings` -> `Applications` -> `Manage Access Tokens` e crie um novo token de acesso.

Em `Token Name` coloque `Package Registry Token`. Clique em `Select permissions` e marque `package`: `Read and Write`. Pressione o botão `Generate Token` e copie o valor do token.

#### Publicação de pacotes no package registry

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

#### Vinculando um pacote a um repositório

No menu direito superior, clique no seu avatar e `Profile`. Selecione `Packages` e clique no pacote a ser vinculado. Selecione `Settings` -> `Link to repository` e selecione o repositório a ser vinculado.

### Criação de repositório

Crie um novo repositório no _Gitea_ chamado `python-web-app`.

Clique em `Settings` -> `Actions` -> `Secrets` e adicione um novo segredo chamado `PACKAGE_REGISTRY_TOKEN` com o valor do token de acesso ao package registry.



