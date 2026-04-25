# Pré-Instalação do Arch Linux

Este documento descreve o processo de preparação antes da instalação do **Arch Linux** realizado neste projeto.

---

## 📥 Download da ISO

A ISO oficial pode ser obtida em:

* https://archlinux.org/download/

O site recomenda o download via Torrent, mas pra quem preferir, no fim da pagina tem opções de download direto.

---

## 💾 Criação do pendrive bootável

Como estou fazendo a instalação direta no meu HD principal, precisarei de montar a imagem em um pendrive bootável.

### Método utilizado neste projeto: Ventoy

Passos:

1. Baixar o Ventoy do site oficial https://www.ventoy.net/en/download.html
2. Instalar o Ventoy diretamente no pendrive
3. Copiar a ISO do Arch Linux para o dispositivo

> 💡 Escolhi o Ventoy por permitir múltiplas ISOs no mesmo pendrive e pela facilidade de instalação.

---

## 🖥️ Boot pelo pendrive

1. Reinicie o computador
2. Acesse o menu de boot (geralmente `F2`, `F12`, `DEL` ou `ESC`)
3. Selecione o pendrive

---

## 🌐 Verificar conexão com a internet

A instalação do Arch Linux precisa de conexão com internet, então verifique sua conexão.

Teste com:

```bash
ping archlinux.org
```

### Cabo (Ethernet)

A conexão via cabo normalmente é automática sem precisar de configurações.

### Wi-Fi

Para conexão por Wi-FI, utilize o comando `iwctl` para abrir o modo interativo:

* <https://wiki.archlinux.org/title/Iwd>

```bash
iwctl
```

Depois os comando abaixo para encontrar a rede Wi-Fi e realizar a conexão:

```bash
device list # lista os dispositivos de rede
station wlan0 scan # escaneia as redes Wi-Fi disponíveis
station wlan0 get-networks # lista as redes encontradas
station wlan0 connect NOME_DA_REDE # faz a conexão com a rede informada (solicitará a senha da rede se houver)
exit # fecha o modo interativo
```

---

## 🛠️ Método de Instalação

O Arch Linux oferece tanto a instalação manual quanto a guiada. Para garantir um ambiente base funcional e focar os estudos na configuração do Hyprland e Wayland, optei pelo **archinstall**.

> 📝 *Nota: Esta escolha permite uma transição mais suave para o Arch, garantindo que o particionamento e a base do sistema sejam feitos seguindo os padrões oficiais.*

## 🆙 Atualização do instalador (archinstall)

Antes de iniciar a instalação guiada, atualize o script para a versão mais recente disponível nos repositórios:

```bash
pacman -Syu archinstall
```

---

## 🚧 Próximos passos

Iniciar o processo de instalação executando o comando archinstall.

(Esses passos serão documentados em `installation.md`)