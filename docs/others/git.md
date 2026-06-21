# Instalação e Configuração do Git

Este guia mostra como instalar o Git no Arch Linux e como realizar a configuração inicial necessária para começar a usá-lo.

## Sumário

1. [O que é o Git](#o-que-é-o-git)
2. [Instalação](#instalação)
3. [Verificando a instalação](#verificando-a-instalação)
4. [Configuração inicial](#configuração-inicial)
5. [Configurando autenticação SSH (opcional)](#configurando-autenticação-ssh-opcional)
6. [Comandos básicos para começar](#comandos-básicos-para-começar)
7. [Problemas comuns](#problemas-comuns)

---

## O que é o Git

Git é um sistema de controle de versão distribuído, usado para acompanhar mudanças em arquivos de código-fonte e coordenar o trabalho entre várias pessoas em um mesmo projeto.

---

## Instalação

No Arch Linux, o Git é instalado através do gerenciador de pacotes `pacman`:

```bash
sudo pacman -S git
```

Para manter o Git atualizado junto com o restante do sistema, basta rodar uma atualização completa:

```bash
sudo pacman -Syu
```

---

## Verificando a instalação

Após instalar, abra o terminal e execute:

```bash
git --version
```

A saída esperada deve ser algo semelhante a:

```
git version 2.45.0
```

Se o comando não for reconhecido, verifique se o Git foi adicionado corretamente ao PATH do sistema.

---

## Configuração inicial

Antes de começar a usar o Git, é necessário configurar seu nome e e-mail. Essas informações serão associadas a todos os commits que você fizer.

```bash
git config --global user.name "Seu Nome"
git config --global user.email "seuemail@exemplo.com"
```

### Outras configurações úteis

**Definir o editor padrão** (exemplo usando o VS Code):

```bash
git config --global core.editor "code --wait"
```

**Definir o nome padrão da branch principal:**

```bash
git config --global init.defaultBranch main
```

**Habilitar cores no terminal:**

```bash
git config --global color.ui auto
```

**Verificar todas as configurações atuais:**

```bash
git config --list
```

**Verificar uma configuração específica:**

```bash
git config user.name
```

As configurações ficam salvas no arquivo `~/.gitconfig`.

---

## Configurando autenticação SSH (opcional)

Para evitar digitar usuário e senha a cada operação com repositórios remotos (como GitHub, GitLab ou Bitbucket), é recomendado configurar uma chave SSH.

### 1. Gerar uma nova chave SSH

```bash
ssh-keygen -t ed25519 -C "seuemail@exemplo.com"
```

Pressione Enter para aceitar o local padrão de salvamento e, se desejar, defina uma senha (passphrase) para a chave.

### 2. Adicionar a chave ao agente SSH

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

### 3. Copiar a chave pública

```bash
cat ~/.ssh/id_ed25519.pub
```

Copie o conteúdo exibido.

### 4. Adicionar a chave à sua conta (exemplo: GitHub)

1. Acesse **Settings > SSH and GPG keys**.
2. Clique em **New SSH key**.
3. Cole a chave copiada e salve.

### 5. Testar a conexão

```bash
ssh -T git@github.com
```

Se tudo estiver correto, você verá uma mensagem de autenticação bem-sucedida.

---

## Comandos básicos para começar

| Comando | Descrição |
|---|---|
| `git init` | Inicializa um novo repositório Git na pasta atual |
| `git clone <url>` | Clona um repositório remoto para a máquina local |
| `git status` | Mostra o estado atual dos arquivos (modificados, novos, etc.) |
| `git add <arquivo>` | Adiciona um arquivo à área de staging |
| `git add .` | Adiciona todos os arquivos modificados à área de staging |
| `git commit -m "mensagem"` | Cria um commit com as alterações adicionadas |
| `git push` | Envia os commits locais para o repositório remoto |
| `git pull` | Atualiza o repositório local com as alterações do remoto |
| `git branch` | Lista as branches existentes |
| `git checkout -b <nome>` | Cria e muda para uma nova branch |
| `git log` | Exibe o histórico de commits |

---

## Problemas comuns

**"git: command not found"**
Verifique se o Git foi instalado corretamente e se o caminho está incluído na variável `PATH`.

**Erro de permissão ao usar SSH**
Verifique se a chave pública foi adicionada corretamente à sua conta na plataforma remota e se o agente SSH está em execução.

**Mensagem pedindo usuário/senha repetidamente em HTTPS**
Considere migrar para autenticação via SSH ou configurar um *credential helper*:

```bash
git config --global credential.helper cache
```

---

## Referências

- [Documentação oficial do Git](https://git-scm.com/doc)
- [Git - Guia de referência rápida](https://git-scm.com/docs)
