# Guia de Instalação e Configuração do Zsh + Starship (Arch Linux)

Este guia mostra como instalar o Zsh, definir como shell padrão, e configurar o prompt **Starship** junto com os plugins **zsh-autosuggestions** e **zsh-syntax-highlighting**. As teclas de atalho são configuradas considerando o **Kitty** como emulador de terminal.

## Sumário

1. [O que é o Zsh](#o-que-é-o-zsh)
2. [Instalação do Zsh](#instalação-do-zsh)
3. [Definindo o Zsh como shell padrão](#definindo-o-zsh-como-shell-padrão)
4. [Instalando o Starship](#instalando-o-starship)
5. [Instalando zsh-autosuggestions e zsh-syntax-highlighting](#instalando-zsh-autosuggestions-e-zsh-syntax-highlighting)
6. [Configuração final do .zshrc](#configuração-final-do-zshrc)
7. [Configurando teclas de atalho (Kitty)](#configurando-teclas-de-atalho-kitty)
8. [Verificando tudo funcionando](#verificando-tudo-funcionando)
9. [Problemas comuns](#problemas-comuns)

---

## O que é o Zsh

Zsh (Z Shell) é um shell Unix moderno, compatível com o Bash, mas com recursos extras de autocompletar, customização e suporte avançado a plugins e temas.

---

## Instalação do Zsh

```bash
sudo pacman -S zsh
```

---

## Definindo o Zsh como shell padrão

Para que o Zsh seja iniciado automaticamente em todos os novos terminais, troque o shell padrão do seu usuário:

```bash
chsh -s /usr/bin/zsh
```

Depois, faça logout e login novamente (ou reinicie o terminal) para que a mudança tenha efeito.

Para verificar qual é o shell atual:

```bash
echo $SHELL
```

> Na primeira vez que o Zsh for executado, ele pode exibir um assistente de configuração inicial (`zsh-newuser-install`). Você pode pressionar `q` para sair e configurar manualmente, como veremos a seguir.

---

## Instalando o Starship

O Starship é um prompt rápido, multiplataforma e altamente customizável, escrito em Rust.

### 1. Instalar o pacote

```bash
sudo pacman -S starship
```

### 2. Ativar no Zsh

Adicione a seguinte linha ao final do arquivo `~/.zshrc`:

```bash
eval "$(starship init zsh)"
```

### 3. Instalar a JetBrains Mono Nerd Font

Para que os ícones do prompt sejam exibidos corretamente, é necessário instalar uma Nerd Font. Neste guia usaremos a **JetBrains Mono Nerd Font**:

```bash
sudo pacman -S ttf-jetbrains-mono-nerd
```

Depois de instalada, configure essa fonte no Kitty (veja a seção [Configurando teclas de atalho (Kitty)](#configurando-teclas-de-atalho-kitty) para detalhes do arquivo de configuração) adicionando a linha abaixo ao `~/.config/kitty/kitty.conf`:

```ini
font_family JetBrainsMono Nerd Font
```

Depois de salvar, recarregue a configuração do Kitty reabrindo o terminal.

### 4. Criar o arquivo de configuração (opcional)

```bash
starship preset nerd-font-symbols -o ~/.config/starship.toml
```

Esse comando aplica um preset pronto com símbolos de Nerd Font. Você pode editar `~/.config/starship.toml` posteriormente para personalizar módulos, cores e formato do prompt.

---

## Instalando zsh-autosuggestions e zsh-syntax-highlighting

Esses dois plugins são instalados e configurados juntos, pois trabalham em conjunto para melhorar a experiência de digitação no terminal:

- **zsh-autosuggestions**: sugere comandos automaticamente com base no seu histórico, exibindo-os em cinza claro à frente do cursor.
- **zsh-syntax-highlighting**: destaca a sintaxe dos comandos digitados em tempo real (comandos válidos em verde, inválidos em vermelho, etc.).

### 1. Instalar via pacman

```bash
sudo pacman -S zsh-autosuggestions zsh-syntax-highlighting
```

### 2. Ativar no `.zshrc`

Adicione as linhas abaixo ao final do arquivo `~/.zshrc`, **nessa ordem** — o `zsh-syntax-highlighting` precisa ser sempre o último plugin carregado:

```bash
source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh
source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```

---

## Configurando teclas de atalho

Por padrão, o Zsh nem sempre mapeia todas as teclas de edição de linha (como `Home`, `End`, `Ctrl+Backspace` ou `Ctrl+Delete`) da forma esperada. As sequências abaixo foram verificadas especificamente para o **Kitty**, que por padrão usa o protocolo de teclado no modo legado (compatível com xterm) para essas combinações.

Adicione os binds abaixo ao seu `~/.zshrc` (antes da linha do `zsh-syntax-highlighting`, pois ele também faz uso do sistema de `bindkey`):

```bash
# Ir para o início da linha (Home)
bindkey "^[[H" beginning-of-line
bindkey "^[[1~" beginning-of-line

# Ir para o final da linha (End)
bindkey "^[[F" end-of-line
bindkey "^[[4~" end-of-line

# Mover uma palavra para trás (Ctrl + ←)
bindkey "^[[1;5D" backward-word

# Mover uma palavra para frente (Ctrl + →)
bindkey "^[[1;5C" forward-word

# Apagar a palavra anterior (Ctrl + Backspace)
bindkey "^H" backward-kill-word

# Apagar o caractere sob o cursor (Delete)
bindkey "^[[3~" delete-char

# Apagar a palavra seguinte (Ctrl + Delete)
bindkey "^[[3;5~" kill-word
```

> **Nota sobre o Kitty:** o `Ctrl+Backspace` no Kitty envia, por padrão, o código `^H` (Ctrl-H) em vez de uma sequência `CSI`, por isso esse bind é diferente dos demais.

### Tabela de referência

| Widget | Ação | Atalho no Kitty |
|---|---|---|
| `beginning-of-line` | Move o cursor para o início da linha | `Home` |
| `end-of-line` | Move o cursor para o final da linha | `End` |
| `backward-word` | Move o cursor uma palavra para trás | `Ctrl + ←` |
| `forward-word` | Move o cursor uma palavra para frente | `Ctrl + →` |
| `backward-kill-word` | Apaga a palavra anterior ao cursor | `Ctrl + Backspace` |
| `delete-char` | Apaga o caractere sob o cursor | `Delete` |
| `kill-word` | Apaga a palavra seguinte ao cursor | `Ctrl + Delete` |

> **Observação:** se você usar outro emulador de terminal além do Kitty, esses códigos de escape podem ser diferentes. Para descobrir a sequência real enviada por uma tecla, use o comando `cat -v`, pressione a combinação desejada e observe a saída (finalize com `Ctrl+C`):

```bash
cat -v
```

### Aplicando as alterações

Depois de adicionar os binds, recarregue o `.zshrc`:

```bash
source ~/.zshrc
```

---

## Configuração final do `.zshrc`

Ao final de todas as instalações, seu arquivo `~/.zshrc` deve conter, na ordem abaixo:

```bash
# Plugins
source /usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh

# Teclas de atalho
bindkey "^[[H" beginning-of-line
bindkey "^[[1~" beginning-of-line
bindkey "^[[F" end-of-line
bindkey "^[[4~" end-of-line
bindkey "^H" backward-kill-word
bindkey "^[[3~" delete-char
bindkey "^[[3;5~" kill-word
bindkey "^[[1;5D" backward-word
bindkey "^[[1;5C" forward-word

source /usr/share/zsh/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh

# Prompt
eval "$(starship init zsh)"
```

> **Importante:** o `zsh-syntax-highlighting` deve ser carregado **depois** do `zsh-autosuggestions` e dos `bindkey`, e de preferência ser uma das últimas linhas do arquivo. O `starship init` pode vir antes ou depois sem problemas.

Após editar o arquivo, recarregue a configuração:

```bash
source ~/.zshrc
```

---

## Verificando tudo funcionando

- **Starship**: o prompt deve mudar de aparência imediatamente após o `source ~/.zshrc`.
- **JetBrains Mono Nerd Font**: os ícones do prompt (branch do git, símbolos de linguagens, etc.) devem aparecer corretamente, sem caixas ou pontos de interrogação.
- **zsh-autosuggestions**: comece a digitar um comando já usado antes; uma sugestão em cinza deve aparecer. Pressione `→` (seta para a direita) para aceitá-la.
- **zsh-syntax-highlighting**: digite um comando válido (ex.: `ls`) e veja se ele fica verde; digite algo inválido (ex.: `lsx`) e veja se fica vermelho.
- **Teclas de atalho**: teste `Home`, `End`, `Delete`, `Ctrl+Backspace`, `Ctrl+Delete`, `Ctrl+←` e `Ctrl+→` durante a edição de um comando.

---

## Problemas comuns

**O prompt do Starship não muda após a instalação**
Verifique se a linha `eval "$(starship init zsh)"` está presente no `~/.zshrc` e se você recarregou o shell com `source ~/.zshrc` ou abriu um novo terminal.

**Ícones aparecem como caixas ou pontos de interrogação**
Confirme se o pacote `ttf-jetbrains-mono-nerd` foi instalado e se a linha `font_family JetBrainsMono Nerd Font` está presente no `~/.config/kitty/kitty.conf`. Recarregue o Kitty com `Ctrl+Shift+F5` após a alteração.

**zsh-autosuggestions não sugere nada**
Confirme que a linha de `source` está correta e que o caminho `/usr/share/zsh/plugins/zsh-autosuggestions/zsh-autosuggestions.zsh` existe:

```bash
ls /usr/share/zsh/plugins/zsh-autosuggestions/
```

**Cores de sintaxe não aparecem**
Confirme que a linha do `zsh-syntax-highlighting` está **no final** do arquivo `~/.zshrc`, após qualquer outro plugin e após os `bindkey`.

**Zsh não inicia automaticamente ao abrir o terminal**
Verifique se o shell padrão foi alterado corretamente:

```bash
cat /etc/passwd | grep $USER
```

A linha deve terminar com `/usr/bin/zsh`. Caso contrário, rode novamente o `chsh -s /usr/bin/zsh`.

**Teclas como Home, End ou Delete não funcionam como esperado**
Confirme que o Kitty não está com o protocolo de teclado avançado (`kitty_keyboard`) forçado em modo incompatível com o legado. Use `cat -v` para identificar a sequência real enviada pela tecla e ajuste os `bindkey` correspondentes no `~/.zshrc`.

---

## Referências

- [Starship - Documentação oficial](https://starship.rs/guide/)
- [zsh-autosuggestions - GitHub](https://github.com/zsh-users/zsh-autosuggestions)
- [zsh-syntax-highlighting - GitHub](https://github.com/zsh-users/zsh-syntax-highlighting)
- [Kitty - Documentação oficial](https://sw.kovidgoyal.net/kitty/)
- [Arch Wiki - Zsh](https://wiki.archlinux.org/title/Zsh)
