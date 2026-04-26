# Instalação do Arch Linux (via archinstall)

Este documento detalha o processo de instalação do sistema base utilizando o instalador guiado oficial do Arch Linux.

> Confira também o guia pré-instalação em [docs/pre-install.md](pre-install.md)

---

## 🚀 Iniciando o Instalador

Inicie o instalador com o comando:

```bash
archinstall
```

Uma interface de texto será aberta. Navegue usando as setas do teclado e selecione com Enter.

---

## ⚙️ Configurações

A seguir estão os tópicos de configuração apresentados pelo instalador. Tentarei explicar o que é cada uma das opções, quais as escolhas que fiz e o porque. 

---

## Archinstall Language
Define apenas o idioma da interface do instalador.

✅ **Opção Escolhida:** `English`
* **Por quê?** Manter em inglês ajuda a se acostumar com a linguagem técnica e facilita a busca por erros e documentações oficiais durante o processo.

---

## Locales
Define o idioma principal do sistema, a codificação de caracteres e o layout do teclado.

✅ **Opções Escolhidas:**
* **Locale language:** `en_US.UTF-8`
  * *Por quê?* Este será o idioma padrão do sistema operacional após a instalação. Acostumar-se com o sistema em inglês facilita muito a resolução de problemas e a leitura de logs.
* **Locale encoding:** `UTF-8`
  * *Por quê?* É o padrão moderno de codificação de texto que consegue interpretar e exibir corretamente quase todos os caracteres e símbolos do mundo.
* **Keyboard layout:** `br-abnt2`
  * *Por quê?* É o padrão físico da esmagadora maioria dos teclados brasileiros.

---

## Mirrors and Repositories
Gerencia de onde o sistema baixará os pacotes. Os repositórios oficiais (`core` e `extra`) já vêm configurados de forma oculta.

**Opções configuradas:**
* **Optional repositories:** Repositórios extras para casos de uso específicos.
* **Mirror region:** Servidores espelho distribuídos geograficamente que hospedam cópias dos repositórios.

✅ **Opções Escolhidas:**
* **Optional repositories:** `multilib`
  * *Por quê?* Essencial para lidar com softwares e bibliotecas de 32 bits em um sistema de 64 bits. É um requisito obrigatório para quem pretende jogar (ex: Steam) ou usar drivers e aplicativos mais antigos.
* **Mirror region:** `Brazil`
  * *Por quê?* Escolher a região geográfica mais próxima melhora drasticamente a velocidade de download dos pacotes e atualizações.

---

## Disk Configuration
Nesta etapa, o instalador exige definições críticas sobre como o disco de destino será formatado, estruturado e protegido. As escolhas abaixo consideram uma instalação em **SSD único, sem Dual Boot**.

### Partitioning (Particionamento de disco)
Define como as divisões físicas ou lógicas serão criadas no disco.

> ⚠️ **Aviso:** Os passos abaixo irão formatar o disco selecionado. Certifique-se de ter feito backup de qualquer dado importante antes de prosseguir.

**Opções disponíveis:**
* **Use a best-effort default partition layout:** Modo automático. O instalador apaga o disco e cria o particionamento padrão recomendado (geralmente `/boot` EFI e `/` Raiz).
* **Manual Partitioning:** Abre ferramentas como `fdisk` ou `parted` para criar as partições manualmente. Ideal para Dual Boot.
* **Pre-mounted configuration:** Assume que o usuário já preparou as partições manualmente via terminal antes de rodar o `archinstall`.

✅ **Opção Escolhida:** `Use a best-effort default partition layout`
* **Por quê?** Como a instalação ocorre em um SSD dedicado e limpo, sem necessidade de preservar outro sistema, o modo automático é a via mais eficiente e segura para estruturar o boot e a raiz.

### Filesystem (Sistema de Arquivos)
Define a estrutura lógica usada para organizar e armazenar os dados no SSD.

**Opções disponíveis:**
* **ext4:** O sistema tradicional do Linux. Focado em estabilidade absoluta.
* **btrfs:** Sistema moderno (*Copy-on-Write*). Oferece *snapshots*, compressão nativa e subvolumes.
* **xfs:** Focado em escalabilidade para servidores e arquivos gigantescos.
* **f2fs:** Otimizado especificamente para prolongar a vida útil de memórias flash (SSDs/NVMe).

✅ **Opção Escolhida:** `btrfs`
* **Por quê?** O objetivo é explorar o uso de **snapshots** para segurança e reversão rápida de atualizações problemáticas. Os subvolumes permitem gerenciar o sistema de forma flexível.

### Configurações Avançadas do Btrfs
Ao escolher o `btrfs`, o instalador permite ativar recursos específicos.

**Opções analisadas:**
* **Copy-on-Write (CoW):** Grava alterações em novos blocos em vez de sobrescrever dados.
* **Compressão (zstd):** Comprime arquivos no momento da gravação.

✅ **Opções Escolhidas:** `CoW Habilitado` e `Compressão (zstd) Habilitada`
* **Por quê?** O CoW é o recurso fundamental que permite a criação de snapshots instantâneos. A compressão `zstd` maximiza o espaço e prolonga a vida útil do SSD, podendo até melhorar o desempenho de leitura.

### Gerenciador de Volumes Lógicos (LVM)
Permite criar partições dinâmicas redimensionáveis.

✅ **Opção Escolhida:** `No (Não)`
* **Por quê?** O `btrfs` já provê o gerenciamento de volumes e snapshots nativamente. O LVM adicionaria uma camada de complexidade desnecessária para este cenário.

### Criptografia de Disco (LUKS) e Iteration Time
Configuração de segurança para proteger os dados em repouso.

**Opções analisadas:**
* **Enable (LUKS):** Criptografa o disco inteiro.
* **Iteration Time:** Define a dificuldade e o tempo de processamento para validar a senha contra ataques de força bruta.

✅ **Opções Escolhidas:** `Criptografia Habilitada` com `Iteration Time de 2000ms`
* **Por quê?** Sendo um laptop focado em mobilidade, proteger dados sensíveis contra roubo de hardware é indispensável. O tempo de **2000ms** (2 segundos) oferece o equilíbrio ideal entre segurança criptográfica robusta e agilidade no boot diário.

### Gerenciamento de Snapshots (Btrfs)
Ferramenta para automatizar a criação de pontos de restauração.

**Opções analisadas:**
* **Timeshift:** Interface gráfica amigável. Isola os backups do sistema e protege os arquivos do usuário (`/home`) durante restaurações.
* **Snapper:** Focado em terminal, com integração profunda ao gerenciador de pacotes para automação pesada.

✅ **Opção Escolhida:** `Timeshift`
* **Por quê?** Oferece uma separação lógica e segura entre o sistema operacional e os dados pessoais. Se o sistema quebrar após um update, a restauração do Timeshift recupera a raiz (`/`) sem apagar documentos recentes da sua `/home`.

### Memória Virtual (Swap)
Define como o sistema lida com o excesso de consumo de memória RAM.

**Opções disponíveis:**
* **Swap Partition:** Partição física dedicada (obrigatório para hibernação).
* **Swap File:** Arquivo de swap na partição raiz.
* **zRAM:** Cria um bloco de RAM comprimida para atuar como swap ultrarrápido.

✅ **Opção Escolhida:** `zRAM` (Ativado por padrão no archinstall)
* **Por quê?** O fluxo de uso deste laptop foca na **suspensão** (fechar a tampa), não na hibernação. O uso do `zRAM` evita gravações no SSD, aumentando sua durabilidade, e é muito mais rápido que qualquer disco físico.

> 💡 **Contexto de Hardware (32GB RAM / 1TB SSD):** > Com 32GB de RAM, criar um Swap físico seria desperdício de espaço. O `zRAM` atua como uma rede de segurança leve. Da mesma forma, o SSD de 1TB oferece folga abundante para armazenar os snapshots do `btrfs` ao longo do tempo.

---

## Bootloader
O software responsável por carregar o kernel do Linux após a inicialização do hardware (UEFI).

**Opções disponíveis:**
* **GRUB:** Tradicional, altamente customizável, ideal para Dual Boot.
* **Systemd-boot:** Moderno, veloz e integrado nativamente ao Arch. Usa arquivos de texto simples.
* **EFISTUB / Limine / rEFInd:** Opções alternativas para inicialização direta via placa-mãe ou gerenciamento visual avançado.

✅ **Opção Escolhida:** `Systemd-boot`
* **Por quê?** Sendo uma instalação dedicada apenas ao Arch Linux, ele oferece a inicialização mais rápida e uma configuração mais limpa, alinhada à filosofia KISS (*Keep It Simple, Stupid*). Ele lida perfeitamente com o envio das chaves do LUKS ao sistema.

---

## Kernel
O "motor" do sistema operacional. O Arch Linux recomenda manter mais de um kernel instalado como medida de segurança (Fallback).

**Opções disponíveis:**
* **linux:** A versão oficial (Vanilla) mais recente.
* **linux-lts:** *Long-Term Support*. Focado estritamente em estabilidade.
* **linux-hardened:** Focado em segurança agressiva.
* **linux-zen:** Otimizado para desktop, mantendo a responsividade do sistema sob altas cargas de trabalho.

✅ **Opções Escolhidas:** `linux-zen` (Principal) e `linux-lts` (Fallback)
* **Por quê?** O `linux-zen` tirará o máximo proveito da arquitetura híbrida do processador i9, mantendo a interface fluida durante compilações pesadas ou jogos. O `linux-lts` atuará como "estepe": caso uma atualização do kernel Zen conflite com o driver da NVIDIA, basta reiniciar, selecionar o LTS no bootloader e continuar trabalhando.

---

## Unified Kernel Images (UKI)
Define como os arquivos de inicialização do sistema são gerados.

**Opções disponíveis:**
* **Disabled:** Parâmetros, kernel e `initramfs` são armazenados em vários arquivos separados.
* **Enabled:** Empacota todos os componentes de boot em um único arquivo binário executável (`.efi`).

✅ **Opção Escolhida:** `Enabled`
* **Por quê?** O UKI moderniza e fortalece o boot. Ele encapsula as configurações de descriptografia LUKS e o kernel de forma limpa e segura, trabalhando em perfeita harmonia com o `systemd-boot`.

---

## Hostname
Define o nome de identificação do computador na rede local (Wi-Fi, LAN, Bluetooth).

✅ **Opção Escolhida:** `archland` (Arch Linux + Hyprland)

---

## Authentication
Configuração das contas de acesso e definição da hierarquia de privilégios.

✅ **Opções Escolhidas:**
* **Root Password:** Deixado em branco (Desativado).
* **User Account:** Criado o usuário principal.
* **Superuser Privileges (Sudo):** Ativado (`Yes`).
* **Justificativa (Princípio do Menor Privilégio):** A prática mais segura no Linux moderno é desabilitar o login direto com a conta root (evitando um vetor de ataque). O usuário criado recebe privilégios de `sudo`, garantindo que ações administrativas destrutivas só ocorram quando explicitamente solicitadas e validadas por senha.

---

## Profile
Define o escopo dos pacotes base, determinando a fundação gráfica do sistema.

**Opções disponíveis:**
* **Desktop:** Instala um ambiente gráfico completo (ex: KDE, GNOME).
* **Minimal:** Instala apenas a base via linha de comando (TTY).
* **Server / Xorg:** Perfis voltados para infraestrutura ou configuração crua do X11.

✅ **Opção Escolhida:** `Minimal`
* **Por quê?** Apesar do hardware potente (i9 + RTX 4070), esta é uma decisão estritamente educacional. O objetivo é inicializar em um terminal de texto puro para configurar os drivers proprietários da NVIDIA, o servidor gráfico (Wayland) e o ambiente final (Hyprland) de forma 100% manual, proporcionando controle absoluto e aprendizado profundo da arquitetura do Linux.

---

## Network Configuration
Gerencia conexões de rede (Cabo e Wi-Fi). Etapa **crítica** no perfil Minimal para garantir internet no primeiro boot.

✅ **Opção Escolhida:** `Use Network Manager (default backend)`
* **Por quê?** A mobilidade de um laptop exige praticidade com Wi-Fi. O NetworkManager é a ferramenta padrão da indústria e inclui o `nmtui` — uma interface gráfica de terminal. Isso permite escanear redes e conectar ao Wi-Fi facilmente a partir da "tela preta" (TTY), possibilitando o download do restante do sistema e da interface gráfica.

---

## Applications
Definição de serviços essenciais de suporte a hardware, segurança e energia.

### Audio
✅ **Opção Escolhida:** `PipeWire`
* **Por quê?** Além de oferecer latência ultrabaixa, o PipeWire é um **requisito técnico obrigatório** no ecossistema Wayland (Hyprland) para gerenciar fluxos de vídeo. Sem ele, ferramentas como compartilhamento de tela no Discord ou gravação via OBS Studio não funcionarão adequadamente.

### Bluetooth
✅ **Opção Escolhida:** `Enabled`
* **Por quê?** Praticidade para mouses, fones e transferências. Ativar agora economiza a configuração manual do `bluetooth.service`.

### Print Service
✅ **Opção Escolhida:** `Enabled`
* **Por quê?** Deixa a infraestrutura (CUPS) pré-instalada para detecção e uso de impressoras, evitando problemas futuros de drivers.

### Firewall
✅ **Opção Escolhida:** `ufw` (Uncomplicated Firewall)
* **Por quê?** Alinhado à filosofia Minimal, o `ufw` oferece proteção robusta usando comandos extremamente simples e diretos via terminal, descartando a complexidade do `firewalld`.

### Power Management
✅ **Opção Escolhida:** `power-profiles-daemon`
* **Por quê?** Gerencia com eficiência a arquitetura híbrida do i9, oferecendo perfis equilibrados entre performance e bateria. Possui integração muito mais fácil com ferramentas do Hyprland do que o antigo `tuned`.

---

## Pacotes Adicionais (Additional Packages)
No perfil Minimal, essa seleção forma o "kit de sobrevivência" necessário para a configuração manual no terminal.

✅ **Pacotes Inseridos:**
`git` `base-devel` `neovim` `network-manager-applet`

**Justificativa Técnica:**
* **`git` e `base-devel`:** A infraestrutura obrigatória para clonar e compilar pacotes do **AUR** (Arch User Repository). Essenciais para baixar os complementos comunitários do Hyprland.
* **`neovim`:** Editor de texto via linha de comando. Indispensável para editar arquivos críticos (como `/etc/fstab` e o `hyprland.conf`) antes de possuir uma interface gráfica instalada. *(Nota: `nano` também pode ser utilizado se preferir maior simplicidade).*
* **`network-manager-applet`:** Fornece o ícone de bandeja de rede. Garantirá que, após instalar o Wayland, o gerenciamento de Wi-Fi possa ser feito com o mouse diretamente na barra de tarefas.

---

## Timezone and Automatic Time Sync (NTP)
Configuração geográfica para precisão temporal do sistema.

✅ **Configurações:**
* **Timezone:** `America/Sao_Paulo`
* **Automatic Time Sync (NTP):** `Enabled`

**Justificativa Técnica:**
* O fuso horário garante que agendamentos do sistema e *logs* de desenvolvimento estejam sincronizados com a rotina. 
* A ativação do **NTP** é um requisito de segurança crítico: relógios dessincronizados causam rejeição de certificados SSL (impedindo navegação web segura) e falhas na validação de assinaturas criptográficas pelo `pacman` durante a instalação de pacotes.

---

## 🏁 Conclusão e Próximos Passos

Com a finalização destas etapas, o `archinstall` executará a formatação do disco, a configuração da criptografia LUKS e a instalação do sistema base. Ao concluir, o sistema estará pronto para o primeiro boot, selecione a opção `Reboot`, iniciando em um ambiente de terminal puro (TTY).

A jornada de construção da interface gráfica, drivers de vídeo e produtividade continua no próximo guia:

👉 **Acesse:** [docs/post-install.md](docs/post-install.md)