# Gitea

## Uso

### Criação de usuário

Crie um usuário no _Gitea_ logando-se via fonte de autenticação corporativa.

### Cadastro de chave SSH

Clique em `User Settings` -> `SSH / GPG Keys` e cadastre a parte pública da sua chave SSH.

### Token de acesso ao package registry

Clique em `User Settings` -> `Applications` -> `Manage Access Tokens` e crie um novo token de acesso.

Em `Token Name` coloque `Package Registry Token`. Clique em `Select permissions` e marque `package`: `Read and Write`. Pressione o botão `Generate Token` e copie o valor do token.

### Criação de repositório

Crie um novo repositório no _Gitea_ chamado `python-web-app`.

Clique em `Settings` -> `Actions` -> `Secrets` e adicione um novo segredo chamado `PACKAGE_REGISTRY_TOKEN` com o valor do token de acesso ao package registry.



