# 🛠️ Troubleshooting (Solução de Problemas)

Este documento registra os obstáculos encontrados durante a instalação do Arch Linux e as respectivas soluções aplicadas.

## 💾 Ambiente Live / Pré-Instalação

### Erro: Partition / too full

**Sintoma:**
Ao executar `pacman -Syu archinstall`, o sistema reporta falta de blocos livres, mesmo com HD vazio.

**Causa:**
O espaço em RAM (cowspace) do Arch ISO é limitado por padrão.

Quando o boot é feito pelo pendrive, o sistema operacional roda inteiramente na memória RAM (através de um sistema de arquivos temporário chamado runlib). O erro diz que a partição / (que nesse caso é a sua RAM) não tem espaço suficiente para baixar e descompactar as atualizações e o novo archinstall.

**Solução:**
Expandir o espaço em RAM (cowspace)

Pode dizer ao sistema para reservar um pouco mais de espaço da sua memória RAM para o sistema de arquivos temporário (cowspace). Como o Arch Live é leve, isso geralmente é seguro

Execute o comando para aumentar o espaço para 2GB (ou quanto sua RAM permitir)

```bash
mount -o remount,size=2G /run/archiso/cowspace
```