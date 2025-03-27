# Guia de instalação do Gitea

## Preparação do blueprint

Use como base o _blueprint_ disponível em [blueprint.yaml](https://github.com/lwsa-tech/platform-tools-workloads/blob/main/staging/blueprints/forge/blueprint.yaml).

O valor de `GITEA__mailer__PASSWD` no segredo `smtp` do _blueprint_ deve ser gerado e atualizado segundo orientação no repo [platform-infra](https://github.com/lwsa-tech/platform-infra).

Ao aplicar pela primeira vez o _blueprint_, manter comentada a linha:

```yaml
# GITEA__service__ALLOW_ONLY_EXTERNAL_REGISTRATION: "true"
```

## Criação do usuário admin

No primeiro acesso, criar um usuário `admin`. Cadastre a senha no Keeper.

No menu do _Gitea_, clicar em `Admin Settings` -> `Identity & Access` -> `Authentication Sources` e adicionar a fonte de autenticação corporativa.

Uma vez adicionada a fonte de autenticação, descomentar a linha:

```yaml
GITEA__service__ALLOW_ONLY_EXTERNAL_REGISTRATION: "true"
```
e aplicar o _blueprint_ novamente via `git push` e operação de `Refresh`/`Sync` no _Argo CD_.

Logar-se via fonte de autenticação corporativa como o usuário que deverá ser o administador do _Gitea_ para que fique cadastrado.

Logar-se novamente como usuário `admin` e, no menu do _Gitea_, clicar em `Admin Settings` -> `Identity & Access` -> `User Accounts` e editar o usuário que se tornará o administrador, habilitado `Is Administrator` e `Update User Account`.

## Configuração do acesso SSH

Editar, no _blueprint_, a linha:

```yaml
# Configurar com um hostname que aponte para o IP do load balancer criado para a porta 22 do serviço
GITEA__server__SSH_DOMAIN: "191.252.224.94.nip.io"
```

Colocando um _host_ que aponte para o IP do load balancer criado para a porta 22 do serviço.

## Configuração do Action Runner

No menu do _Gitea_, clicar em `Admin Settings` -> `Actions` -> `Runners` e clicar em `Create new Runner`.

Configurar o servidor _Action Runner_ usando as instruções do repo [gitea-act-runner-ansible](https://github.com/lwsa-tech/gitea-act-runner-ansible)

- Copie o valor do token exibido no _Gitea_
- Utilize a _URL_ raiz do _Gitea_
- Copie o link da [release](https://gitea.com/gitea/act_runner/releases) _stable_ mais recente.

## Configuração do Renovate Bot

`Admin Settings` -> `Identity & Access` -> `User Accounts` -> `Create User Account`

`Authentication Source`: `Local`

`Username`: `renovate-bot`

`Email`: `bot@bot.com`

`Password`: gere uma senha forte e anote-a no Keeper.

Desative `Require user to change password`

Logue-se no _Gitea_ como o usuário `renovate-bot` e, no menu do _Gitea_, clicar em `User Settings` -> `Applications` -> `Generate New Token`.

`Token Name`: `renovate-bot`

`Select permissions`: `Read and Write` em `issue` e `repository`.

Ao criar o token, copie o valor do token e anote-o no Keeper num _hidden field_ com _label_ `Token` no segredo criado no Keeper para o `renovate-bot`.

`Create...` -> `New migration` -> `GitHub`

`Clone from URL`: `https://github.com/lwsa-tech/gitea-renovate.git`

`Repository name`: `gitea-renovate`

`Visibility`: `Make repository private`

Clique em `Migrate Repository`.

Edite o arquivo `config.js` atualizando o _entpoint_ para o endereço do _Gitea_.

No repositório recém criado, clicar em `Settings` -> `Actions` -> `Secrets` -> `Add Secret`, nome `RENOVATE_TOKEN` e no valor coloque o token criado para o `renovate-bot`.