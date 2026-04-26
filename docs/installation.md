# Instalação do Arch Linux (via archinstall)

Este documento detalha o processo de instalação do sistema base utilizando o instalador guiado oficial do Arch Linux.

> ⚠️ **Aviso:** Os passos abaixo irão formatar o disco selecionado. Certifique-se de ter feito backup de qualquer dado importante antes de prosseguir.

> Confira também o guia pré-instalação em [docs\pre-install.md](pre-install.md)

---

## 🚀 Iniciando o Instalador

Inicie o instalador com o comando:

```bash
archinstall
```

Uma interface de texto será aberta. Navegue usando as setas do teclado e selecione com Enter.

---

## ⚙️ Configurações

A seguir estão os tópicos de configuração apresentados pelo instalador. Tentarei explicar o que é cada uma das opções, quais as escolhas que fiz e o porque. Lembrando que es

> *Seguirei exatamente a ordem das opções que o instalador apresenta. Lembrando que estou utilizando a versão 4.3-1 do archinstall*

Abaixo está o roteiro das opções que configurei neste projeto:

---

## Archinstall language
Isto defini apenas o idioma do instalador. Mantenha em inglês para se acostumar com a linguagem tecnica.

**Archinstall language:** English

---

## Locales
Define o idioma do sistema e teclado.

**Locale language:** en_US.UTF-8
*Esse sim é o idioma que seu sistema vai ficar depois de instalado. Se acostume com o inglês. Vai facilitar muito sua vida.*

**Locale encoding:** UTF-8
*É a forma como seu sistema interpreta e exibe texto, especialmente caracteres especiais. UTF-8 é um padrão moderno que consegue representar praticamente todos os caracteres do mundo.*

**Keyboard layout:** br-abnt2
*Padrão da maioria dos teclados brasileiros.*

---

## Mirrors and repositories

**Repositories**
São literalmente repositórios onde os pacotes do Arch Linux estão armazenados e constantemente atualizados. O archinstall já trará os principais **core** e **extra** automaticamente de forma oculta, então não precisamos nos preocupar com isso.

Em **Optional repositories** adicionei apenas a opção **multilib** para lidar com softwares e bibliotecas de 32 bits. Muito útil pra quem pretender jogar, ou usar softwares e driver antigos.

**Mirror region**
Mirros espelhos do repositório principal, são como se fossem cópias ou "backup" dos repositórios espalhados pelo mundo todo. Escolha a região geográfica mais próxima de onde você está vai melhorar a velocidade de download dos pacotes. No meu caso **Brazil**.

---

## Disk configuration

Nesta etapa, o instalador do Arch Linux exige definições críticas sobre como o disco de destino será formatado, estruturado e protegido. Abaixo estão as análises de todas as opções oferecidas pelo instalador e as justificativas das escolhas feitas para este ambiente (Laptop, SSD único, sem Dual Boot).

### Partitioning (Particionamento de disco)
Define como as divisões físicas ou lógicas serão criadas no disco.

**Opções disponíveis:**
* **Use a best-effort default partition layout:** Modo automático. O instalador apaga o disco e cria o particionamento padrão recomendado (geralmente uma partição EFI de boot e uma partição Raiz `/`), alinhando os setores automaticamente.
* **Manual Partitioning:** Abre ferramentas de particionamento (como `fdisk` ou `parted`) para que o usuário crie cada partição manualmente, definindo tamanhos exatos. Ideal para Dual Boot ou layouts complexos.
* **Pre-mounted configuration:** Assume que o usuário já particionou, formatou e montou os discos via terminal de comando (no diretório `/mnt`) antes de iniciar o script `archinstall`.

✅ **Opção Escolhida:** `Use a best-effort default partition layout`
* **Por quê?** Como a instalação ocorre em um SSD dedicado e limpo, sem necessidade de preservar outro sistema operacional (Dual Boot), o modo automático é a via mais eficiente, rápida e com menor margem de erro humano para estruturar o boot e a raiz.

### Filesystem (Sistema de Arquivos)
Define a estrutura lógica usada para organizar, armazenar e recuperar os dados no SSD.

**Opções disponíveis:**
* **ext4:** O sistema tradicional e consolidado do Linux. Focado em extrema estabilidade, simplicidade e confiabilidade.
* **btrfs:** Um sistema moderno baseado no conceito *Copy-on-Write* (CoW). Oferece recursos avançados como *snapshots* (pontos de restauração), compressão nativa de dados e subvolumes dinâmicos.
* **xfs:** Focado em altíssima performance e escalabilidade, ideal para servidores e grandes volumes de dados.
* **f2fs:** Desenvolvido especificamente para memórias flash (SSDs, NVMe). Otimiza ciclos de leitura e gravação para prolongar a vida útil do hardware.

✅ **Opção Escolhida:** `btrfs`
* **Por quê?** O objetivo é explorar o uso de **snapshots** para segurança e reversão de atualizações. Seus subvolumes permitem gerenciar o sistema e dados do usuário de forma flexível no mesmo espaço de disco.

### Configurações Avançadas do Btrfs
Ao escolher o Btrfs, o instalador permite configurar seus recursos principais.

**Opções analisadas:**
* **Copy-on-Write (CoW):** Em vez de sobrescrever dados, o sistema grava as alterações em novos blocos. Garante integridade contra quedas de energia e permite snapshots instantâneos.
* **Compressão (zstd):** Comprime arquivos "on-the-fly". Economiza espaço, reduz o desgaste das células do SSD e pode aumentar a velocidade de leitura/escrita em processadores modernos.

✅ **Opção Escolhida:** `CoW Habilitado` e `Compressão (zstd) Habilitada`
* **Por quê?** O CoW é essencial para a funcionalidade de snapshots. A compressão `zstd` foi ativada para maximizar a eficiência e longevidade do SSD.

### Gerenciador de Volumes Lógicos (LVM)
Permite agrupar discos físicos e criar partições dinâmicas redimensionáveis.

**Opções disponíveis:**
* **Yes:** Cria volumes lógicos antes de formatar.
* **No:** Grava o sistema de arquivos diretamente na partição física.

✅ **Opção Escolhida:** `No (Não)`
* **Por quê?** O Btrfs já provê nativamente o gerenciamento de volumes e snapshots. O LVM adicionaria uma camada de complexidade desnecessária para este cenário.

### Criptografia de Disco (LUKS) e Iteration Time
Configuração de segurança para proteger os dados em repouso.

**Opções disponíveis (Criptografia):**
* **Enable (LUKS):** Criptografa o disco inteiro. Exige senha a cada inicialização. Protege dados em caso de perda do hardware.
* **Disable:** Disco não criptografado. Maior velocidade de boot, mas os dados ficam expostos se o SSD for removido.

**Opções disponíveis (Iteration Time):**
* Define o tempo de processamento para validar a senha (proteção contra brute-force).
* *Exemplo 10000ms:* Segurança extrema, mas atrasa o boot em 10 segundos.
* *Exemplo 2000ms:* Padrão da indústria, oferece ótima defesa com apenas 2 segundos de espera.

✅ **Opções Escolhidas:** `Criptografia Habilitada` com `Iteration Time de 2000ms`
* **Por quê?** Sendo um laptop focado em mobilidade que armazena dados sensíveis, a criptografia é indispensável. O tempo de **2000ms** oferece o equilíbrio ideal entre segurança criptográfica e agilidade no boot diário.

### Gerenciamento de Snapshots (Btrfs)
Define a ferramenta responsável por automatizar, organizar e restaurar os pontos de restauração (snapshots) do sistema de arquivos.

**Opções disponíveis:**

* **Timeshift:** Ferramenta com interface gráfica amigável, semelhante à Restauração do Sistema do Windows. Por padrão, realiza o backup apenas da raiz (/) do sistema, protegendo os arquivos pessoais do usuário (na /home) de serem sobrescritos durante um rollback (restauração).

* **Snapper:** Ferramenta avançada desenvolvida pela SUSE. Focada em linha de comando, permite integração profunda com o gerenciador de pacotes (pacman) para criar snapshots automáticos antes e depois de cada atualização.

✅ **Opção Escolhida:** `Timeshift`

* **Por quê?** O Timeshift oferece uma interface gráfica intuitiva e uma separação lógica e segura entre o sistema operacional e os dados do usuário. Isso garante que, ao reverter uma atualização problemática do sistema, documentos, downloads e configurações pessoais recentes permaneçam intactos na pasta /home.

### Memória Virtual (Swap)
Define como o sistema lidará com situações de alto consumo da memória RAM física.

**Opções disponíveis:**

* **Swap Partition (Partição):** Uma divisão física e engessada no SSD. É o método tradicional, obrigatório para quem precisa hibernar o computador.

* **Swap File (Arquivo):** Um arquivo flexível alocado dentro do próprio sistema de arquivos (ex: na raiz /). Em sistemas Btrfs, exige a desativação manual do Copy-on-Write (CoW) e da compressão apenas para este arquivo.

* **zRAM (Swap na RAM):** Uma abordagem moderna que cria um bloco de armazenamento comprimido dentro da própria memória RAM, em vez de usar o disco físico.

✅ **Opção Escolhida:** zRAM (Ativado via default do archinstall / zram-generator)

* **Por quê?** O fluxo de uso do laptop baseia-se exclusivamente em suspensão, dispensando a hibernação (que exigiria swap físico no disco). O uso do zRAM é tecnicamente superior neste cenário, pois evita ciclos constantes de escrita e leitura no SSD (aumentando sua vida útil) e oferece um desempenho consideravelmente mais rápido, já que a RAM é ordens de grandeza mais veloz que qualquer SSD.

> 💡 **Contexto de Hardware (32GB RAM / 1TB SSD)**
> As escolhas de particionamento e memória virtual foram validadas especificamente para o hardware alvo:
> * **Sobre o Swap:** Com **32GB de RAM** e ausência de uso de hibernação, criar um Swap físico no disco seria um desperdício de armazenamento. O **zRAM** atua como uma rede de segurança leve que só consome recursos se estritamente necessário.
> * **Sobre o Btrfs:** O SSD de **1TB** oferece folga abundante de armazenamento para suportar o crescimento natural do espaço ocupado pelos *snapshots* do Btrfs ao longo do tempo.

---

## Bootloader

O software responsável por carregar o kernel do Linux após a inicialização do hardware (UEFI).

**Opções disponíveis:**

* **GRUB:** O bootloader mais tradicional e amplamente compatível. Altamente customizável, ideal para cenários complexos de Dual Boot e permite temas visuais ricos.

* **Systemd-boot:** Solução moderna, rápida e minimalista, nativamente integrada ao sistema base do Arch. Configuração baseada em arquivos de texto simples.

* **EFISTUB:** Permite que a placa-mãe (UEFI) inicie o kernel do Linux diretamente, sem intermediários. Extremamente rápido, mas complexo para gerenciar atualizações e parâmetros de kernel.

* **Limine:** Bootloader moderno, elegante e fácil de configurar, que vem ganhando tração na comunidade por aliar simplicidade a opções visuais.

* **rEFInd:** Um gerenciador de boot com foco em interface gráfica rica e auto-descoberta de sistemas, ideal para máquinas com múltiplos sistemas operacionais.

✅ **Opção Escolhida:** `Systemd-boot`

* **Por quê?** Sendo uma instalação dedicada (apenas Arch Linux) em hardware moderno (UEFI), o systemd-boot oferece o tempo de inicialização mais rápido e a configuração mais limpa e alinhada com a filosofia KISS (Keep It Simple, Stupid) do Arch. Ele gerencia perfeitamente a passagem de parâmetros da criptografia LUKS sem a sobrecarga visual ou estrutural do GRUB.

---

## Kernel
O Arch Linux permite a instalação simultânea de múltiplos kernels. Utilizar mais de um é uma prática recomendada para criar uma redundância (Fallback), garantindo que o sistema sempre possa ser inicializado caso uma atualização recente quebre o kernel principal.

**Opções disponíveis:**

* **linux:** A versão oficial (Vanilla) mais recente. Traz o suporte mais atualizado a hardwares novos.

* **linux-lts:** Long-Term Support. Versão mais antiga e madura, focada exclusivamente em estabilidade para servidores.

* **linux-hardened:** Focado em segurança paranoica. Mitiga vulnerabilidades desativando certos recursos do kernel, mas pode causar incompatibilidade com softwares comuns e jogos.

* **linux-zen:** Otimizado para desktop e multimídia. Ajusta o escalonador de processos para focar na responsividade (fluidez da interface) sob cargas pesadas de trabalho.

✅ **Opção Escolhida:** `linux-zen` (Principal) e `linux-lts` (Fallback)

* O `linux-zen` atuará como o kernel principal. Suas otimizações de agendamento (scheduler) tirarão o máximo proveito do processador i9 e da RAM para manter fluidez extrema durante desenvolvimento e jogos.

* O `linux-lts` (Long-Term Support) atuará como o "estepe". Se uma atualização bleeding-edge do Zen conflitar com o driver da NVIDIA, poderei selecionar o LTS no menu de boot e continuar trabalhando normalmente.

---

## Unified Kernel Images (UKI)
Define como os arquivos de inicialização do sistema são gerados e armazenados.

**Opções disponíveis:**

* **Disabled (Tradicional):** O kernel, o initramfs, os microcódigos e os parâmetros de inicialização ficam armazenados em arquivos separados no diretório /boot.

* **Enabled (UKI):** Empacota todos os componentes essenciais de boot em um único arquivo binário executável (.efi).

✅ **Opção Escolhida:** `Enabled`

* **Por quê?** O uso do UKI moderniza e fortalece o processo de boot. Em conjunto com o bootloader systemd-boot e a criptografia LUKS (escolhidos nas etapas anteriores), o arquivo unificado garante que os parâmetros sensíveis de descriptografia e o kernel estejam encapsulados juntos de forma limpa, rápida e segura.

---

## Hostname
Define o nome pelo qual o computador será reconhecido na rede local (Wi-Fi, LAN, Bluetooth).

✅ **Opção Escolhida:** archland (Arch Linux + Hyprland)

## Authentication
Configuração das contas de acesso e definição da hierarquia de privilégios do sistema operacional.

**Opções analisadas:**

* **Root Password:** Define uma senha para a conta de superadministrador direto.

* **User Account:** Criação do usuário padrão para o dia a dia.

* **Superuser Privileges (Sudo):** Adiciona o usuário recém-criado ao grupo wheel, concedendo-lhe o direito de executar comandos administrativos usando a própria senha.

✅ **Opções Escolhidas:**

* **Root Password:** Deixado em branco (Desativado)

* **User Account:** Criado o usuário principal.

* **Superuser Privileges:** Sim (Sudo ativado)

**Justificativa de Segurança (Princípio do Menor Privilégio):** A prática mais recomendada e moderna de segurança no Linux é desabilitar o login direto com a conta root. Ao fazer isso, evitamos um vetor de ataque direto. O usuário principal foi criado e recebeu permissões de sudo. Dessa forma, o uso diário para desenvolvimento e jogos ocorre em um ambiente sem privilégios destrutivos, e o poder administrativo só é evocado pontualmente (mediante senha do usuário) quando estritamente necessário (como na instalação de pacotes ou atualizações).

---

## Profile

O perfil define o escopo dos pacotes base que serão instalados, determinando a fundação do sistema operacional.

**Opções analisadas:**

* **Desktop:** Instala um ambiente gráfico completo (KDE, GNOME) e utilitários pré-configurados.

* **Minimal:** Instala apenas os pacotes básicos de linha de comando. Requer configuração manual de rede, áudio, servidor gráfico e interface após a instalação.

* **Server:** Focado em serviços de infraestrutura sem interface gráfica.

* **Xorg:** Instala apenas o servidor gráfico X11 básico, sem ambiente de trabalho.

✅ **Opção Escolhida:** `Minimal`

* **Por quê?** Embora a máquina possua hardware de alta performance (i9 + RTX 4070) destinado a desenvolvimento e jogos, a opção pelo perfil Minimal foi uma decisão estritamente educacional. O objetivo é inicializar o sistema em um console de texto puro (TTY) para construir o ambiente gráfico, configurar os drivers proprietários da NVIDIA e os subsistemas de áudio e rede de forma 100% manual. Essa abordagem proporciona um entendimento profundo da arquitetura interna do Linux e controle absoluto sobre os pacotes instalados no SSD.

---

## Network Configuration
Define como o sistema operacional vai gerenciar as conexões de rede (Cabo e Wi-Fi). Esta etapa é crítica no perfil Minimal, pois o acesso à internet via terminal é obrigatório para a posterior instalação do ambiente gráfico.

**Opções analisadas:**

* **Copy ISO network configuration:** Copia a configuração temporária do Live-USB. Inadequado para o uso dinâmico de um laptop.

* **Manual configuration:** Exige a escrita manual de arquivos de configuração de IP e roteamento. Inviável para redes Wi-Fi dinâmicas.

* **Use Network Manager (default backend):** Instala o gerenciador de rede padrão da indústria Linux (usando wpa_supplicant). Traz ferramentas robustas para linha de comando e integração perfeita com futuras interfaces gráficas.

* **Use Network Manager (iwd backend):** Utiliza o mesmo gerenciador, mas com o motor iwd da Intel, otimizando a velocidade de conexão e roaming em redes Wi-Fi.

✅ **Opção Escolhida:** `Use Network Manager (default backend)`

* **Por quê?** Sendo um laptop, a mobilidade (Wi-Fi) é essencial. O NetworkManager é obrigatório para facilitar a vida do usuário. O principal fator decisivo para a escolha do perfil Minimal é que o NetworkManager inclui a ferramenta nmtui (uma interface de texto interativa). Isso permitirá escanear redes Wi-Fi e digitar a senha facilmente no primeiro boot (TTY), garantindo conexão com a internet para baixar e compilar o restante do sistema operacional.

---

## Applications

### Audio
Gerencia como o som é processado e entregue aos fones e alto-falantes do laptop, além de lidar com fluxos de mídia.

**Opções analisadas:**

* **PulseAudio:** O servidor de som tradicional do Linux. Robusto, porém com arquitetura legada e maior latência.

* **PipeWire:** O servidor multimídia de nova geração do Linux. Criado do zero para ter latência ultrabaixa (ideal para jogos e produção de áudio) e para lidar com fluxos complexos de vídeo e segurança em ambientes Wayland.

✅ **Opção Escolhida:** `PipeWire`

* **Por quê?** A decisão de utilizar o PipeWire está diretamente ligada ao objetivo futuro de instalar o Hyprland (um compositor Wayland). No ecossistema Wayland, o PipeWire é fundamental não apenas para áudio de alta fidelidade e baixa latência (aproveitando o hardware de ponta), mas é um requisito técnico obrigatório para o compartilhamento de tela (Screen Sharing) em aplicativos como Discord, OBS Studio e navegadores via xdg-desktop-portal.

### Bluetooth
Ativa ou desativa o suporte a dispositivos sem fio. Essencial para a mobilidade do laptop.

✅ **Opção Escolhida:** `Enabled`

* **Por quê?** Como estamos em um laptop, o Bluetooth é quase obrigatório para fones de ouvido, mouses ou transferência de arquivos. Ativá-lo agora poupa o trabalho de configurar o serviço bluetooth.service manualmente depois.

### Print Service
Garante a infraestrutura necessária para detecção e uso de impressoras.

✅ **Opção Escolhida:** `Enabled`

* **Por quê?** Instala o suporte para impressoras. Mesmo que você não use agora, em um laptop de trabalho, é prudente ter a base instalada para evitar "lutas" com drivers no futuro.

### Firewall
Segurança de rede

**Opções analisadas:**

* **ufw (Uncomplicated Firewall):** Uma interface simplificada para o iptables/nftables. Focada em facilidade de uso via terminal.

* **firewalld:** Gerenciador de firewall dinâmico com suporte a zonas de confiança.

✅ **Opção Escolhida:** `ufw`

Por quê? Em alinhamento com o perfil Minimal, o ufw oferece uma proteção robusta com comandos simples e diretos, facilitando a administração do sistema sem a complexidade de zonas do firewalld.

### Power Management

* **power-profiles-daemon:** Solução moderna e leve que oferece perfis de energia (Desempenho, Equilibrado, Economia) integrados ao kernel e interfaces gráficas.

* **tuned:** Sistema de monitoramento e tunagem profunda de parâmetros do kernel baseado em perfis de carga de trabalho.

✅ **Opção Escolhida:** `power-profiles-daemon`

* **Por quê?** Para um laptop de alta performance com arquitetura híbrida (i9 13ª Gen), o power-profiles-daemon fornece o equilíbrio ideal entre performance para jogos/desenvolvimento e economia de bateria, além de ser facilmente integrável a ferramentas de terceiros no ambiente Hyprland.

---

## Pacotes Adicionais (Additional Packages)
Definição de pacotes extras a serem instalados junto com o sistema base. No perfil Minimal, a seleção estratégica destes pacotes forma um "kit de sobrevivência" essencial para a posterior configuração manual da interface gráfica (Hyprland).

**Pacotes Inseridos:**
* `git`
* `base-devel`
* `neovim`
* `network-manager-applet`

**Justificativa Técnica:**

* **`git`** e **`base-devel`**: Formam a infraestrutura obrigatória para acessar, baixar e compilar softwares do AUR (Arch User Repository). Como o ecossistema do Hyprland depende fortemente de pacotes comunitários (como helpers AUR, status bars e widgets), estas ferramentas permitem a construção do software a partir do código-fonte (processo que será acelerado pelo processador i9).

* **`nano`** (ou **`neovim`**): Editor de texto via linha de comando. É indispensável para editar arquivos de configuração do sistema (como /etc/fstab, configurações de rede e o hyprland.conf) diretamente no TTY, antes da existência de uma interface gráfica.

* **`network-manager-applet`**: Fornece o front-end gráfico (ícone de bandeja do sistema) para o serviço NetworkManager. Ele garantirá que, assim que o ambiente gráfico Wayland estiver rodando, o gerenciamento de redes Wi-Fi e Bluetooth possa ser feito visualmente através da barra de tarefas.

## Timezone and Automatic time sync (NTP)
Configuração geográfica e cronológica para garantir a precisão temporal do sistema.

**Configurações:**

* **Timezone:** `America/Sao_Paulo`

* **Automatic Time Sync (NTP):** `Enabled`

✅ **Justificativa Técnica:**

**Precisão de Logs e Tarefas:** O fuso horário America/Sao_Paulo garante que todos os logs de desenvolvimento e agendamentos de sistema estejam sincronizados com a rotina do usuário.

**Integridade Criptográfica e de Rede:** A ativação do NTP é fundamental para a segurança. A sincronização constante com servidores de tempo atômico evita falhas na validação de certificados SSL em navegadores e assegura que as assinaturas criptográficas dos pacotes instalados via pacman sejam validadas corretamente, prevenindo bloqueios de segurança durante atualizações de sistema.