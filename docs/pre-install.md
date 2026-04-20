# Pré-Instalação do Arch Linux


Este documento descreve o processo de preparação antes da instalação do **Arch Linux** realizado neste projeto.


> ⚠️ Observação: este guia reflete meu processo de aprendizado e pode evoluir com o tempo.


---


## 📥 Download da ISO


A ISO oficial pode ser obtida em:


* https://archlinux.org/download/


O site recomenda o download via Torrent, mas pra quem prefere no fim da pagina tem opções de download direto.


---


## 💾 Criação do pendrive bootável


Como estou fazendo a instalação direta no meu HD principal, precisarei de montar a imagem em um pendrive bootável.


### Método utilizado neste projeto: Ventoy


Passos:


1. Baixar o Ventoy do site oficial https://www.ventoy.net/en/download.html
2. Instalar o Ventoy diretamente no pendrive
3. Copiar a ISO do Arch Linux para o dispositivo


> 💡 Escolhi o Ventoy por permitir múltiplas ISOs no mesmo pendrive e pela facilidade de intalação.


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

<https://wiki.archlinux.org/title/Iwd>

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

## Preparação do pacman (opcional)

No *Arch Linux* o *pacman* é o gerenciador de pacotes oficial do sistema, ou seja, é a ferramenta que você usa para instalar, atualizar, remover e gerenciar programas no sistema.

<https://wiki.archlinux.org/title/Pacman>

> O pacman já vem configurado por padrão e para o processo de instalação não é necessário nenhum ajuste, porém nas pesquisas que eu fiz, é comum na comunidade ajustar algumas configurações antes de iniciar a intalação.

Abra o arquivo de configuração e procure pelos parâmetros a seguir:

```bash
nano /etc/pacman.conf
```

### *ParallelDownloads*
Este parâmetro define quantos downloads podem ser feitos ao mesmo tempo.
* Se sua conexão for lenta ou instável mantenha em 5 ou menos.
* Se tiver uma boa conexão de internet aumente para 10.
* Evite um valor maior que 10 para não saturar a rede ou causar instabilidade nos mirrors.


### *VerbosePkgList*
Este parâmetro ativa o modo de saída mais detalhado, fazendo com que o pacman mostre mais informações sobre os pacotes que estão sendo instalados/atualizados.
Se tiver comentado (com um # antes) retire o # para ativar o parâmetro, ou mantenha o # para inativar. 

> Eu ativei pois acredito que vá me ajudar no processo de aprendizagem.

### *Color*
Quando este parâmetro está habilitado o pacman usa cores para destacar informações na saída de comandos, como:

Verde → pacotes instalados/atualizados com sucesso
Vermelho → erros ou pacotes removidos
Azul → pacotes opcionais ou informações secundárias

Isso ajuda a distinguir rapidamente tipos diferentes de mensagens, sem precisar ler cada linha com atenção.

Se tiver comentado (com um # antes) retire o # para ativar o parâmetro, ou mantenha o # para inativar. 

### ILoveCandy
Esse é um easter egg do pacman. É um parâmetro oculto e puramente estético.
Ativar faz com que as barras de progresso no terminal fiquem parecidas com o personagem do jogo Pac-Man se movendo.
Inutíl porém divertido.
Para ativar é necessáro adicionar manualmente uma linha no arquivo de configuração.
Recomendo colocar logo abaixo de Color por serem ajustes visuais.

### Resumo
* ParallelDownloads → downloads simultaneos
* VerbosePkgList → retorno mais detalhado
* Color → ativa cores no retorno
* ILoveCandy → easter egg


## 🚧 Próximos passos

Iniciar o processo de instalação

(Esses passos serão documentados em `instalacao.md`)
