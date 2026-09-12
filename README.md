# 📱 Guia de Instalação: Android 16 PIXELOS + Root no Moto G22 (AOSP on ARM64)

Bem-vindo ao projeto de instalação da Custom ROM **Android 16 PIXELOS (GSI)** para o Motorola Moto G22. Este guia detalhado vai te ensinar o passo a passo para fazer backup do seu dispositivo atual, desbloquear o bootloader, e instalar o sistema completo com Root pré-embutido (boot vacinado).

⚠️ **AVISO IMPORTANTE:** Os arquivos disponibilizados neste repositório não contêm dados sensíveis do autor (como IMEI, NVRAM, persist, etc.). Você deve manter o **SEU** backup dessas partições caso precise restaurar. Faça tudo por sua conta e risco.

---

## 📥 Download dos Arquivos da ROM (Google Drive)
Os arquivos de instalação (`super.img`, `boot.img`, `vbmeta`, etc.) são muito grandes para o GitHub. Baixe todos eles diretamente na nuvem clicando no botão abaixo:

[**➔ BAIXAR ARQUIVOS DA ROM NO GOOGLE DRIVE**](https://drive.google.com/drive/folders/1qEm0Zwo9CYq6NgC-KsH146OJLhwb6fxn?usp=drive_link)

---

## 🛠️ Requisitos e Programas Necessários

Antes de começar, você precisa ter instalados no seu computador (Windows):

1. **[Android SDK Platform-Tools (ADB & Fastboot)](https://developer.android.com/studio/releases/platform-tools)**
2. **[Python (Versão 3.9 ou superior)](https://www.python.org/downloads/)** - *Lembre-se de marcar a caixa "Add Python to PATH" durante a instalação.*
3. **[MTK Client](https://github.com/bkerler/mtkclient)** - Para fazer a leitura/escrita profunda (BROM).
4. **[USBDk / libusb-win32](https://github.com/daynix/UsbDk/releases)** - Drivers para reconhecer o celular em modo BROM.
5. **Drivers da Motorola** ou **[Motorola Rescue and Smart Assistant (RSA)](https://pt-br.support.motorola.com/app/rsa)** (Essencial para recuperação em caso de Hard Brick).

---

## Passo 1: Preparando o Aparelho (Depuração USB e OEM Unlock)

1. Vá em **Configurações > Sobre o telefone**.
2. Toque em **Número da versão** 7 vezes para ativar as *Opções do Desenvolvedor*.
3. Volte, vá em **Sistema > Opções do Desenvolvedor**.
4. Ative a opção **Desbloqueio de OEM** (OEM Unlocking).
5. Ative a opção **Depuração USB** (USB Debugging).
6. Conecte o celular ao PC e permita a conexão na tela do celular.

---

## Passo 2: Como Entrar em Modo BROM e Fazer Seu Próprio Backup (MTK Client)

*Fazer backup das suas partições NVRAM/NVDATA é obrigatório antes de instalar ROMs customizadas, para não perder seu IMEI.*

1. Desconecte o cabo USB e **desligue o seu Moto G22 completamente**.
2. No computador, abra um terminal do PowerShell na pasta do **MTK Client**.
3. Digite o comando abaixo e pressione Enter. O programa ficará com a mensagem "Waiting for device" (Aguardando dispositivo):

```powershell
# Este comando faz um Backup Completo de TODAS as partições do celular
# e as salva dentro de uma pasta chamada "Backup_Completo".
# Isso salvará seu IMEI, NVRAM, partições de boot e o sistema atual inteiro!
# Ignorando a partição userdata para poupar tempo e espaço.
python mtk.py rl Backup_Completo --skip userdata
```

4. Agora, pegue o celular desligado, segure os botões **Volume Mais (+) e Volume Menos (-)** juntos e **conecte o cabo USB no computador**. O programa vai detectar o celular em Modo BROM e iniciar a extração automaticamente!

*Guarde os arquivos gerados na pasta Backup_Completo em um lugar seguro na nuvem ou em um pen-drive.*

---

## Passo 3: Desbloqueando o Bootloader (Via MTK Client / BROM)

O bootloader do Moto G22 é severamente bloqueado pela Motorola e não aceita os comandos tradicionais. Sendo assim, o método definitivo é forçar o desbloqueio ignorando a segurança da Motorola (lembrando que isso **apagará todos os seus dados**):

1. Com o celular desconectado e desligado, prepare o comando no PowerShell da pasta do **MTK Client**:
```powershell
python mtk.py da seccfg unlock
```
2. Segure os botões **Volume Mais (+) e Volume Menos (-)** e conecte o cabo USB. O programa fará o desbloqueio instantaneamente.
3. **⚠️ ATENÇÃO - Correção do dm-verity:** Como o desbloqueio foi forçado, o Android de fábrica acusará corrupção. Assim que o MTK Client der sucesso, desconecte o cabo, **mantenha o celular desligado** e **siga imediatamente para o Passo 4**, onde iremos flashear os arquivos necessários para contornar esse aviso.

---

## Passo 4: Instalação da ROM e do Boot Vacinado (Root)

Nesta pasta de lançamento, você encontrou os seguintes arquivos:
- `super.img` (Sistema Completo Android 16 PIXELOS)
- `boot.img` (Kernel Vacinado / Rooted Magisk)
- `vendor_boot.img`
- `dtbo.img`
- Arquivos `vbmeta.img`, `vbmeta_system.img`, `vbmeta_vendor.img`

### Esqueça o Fastboot: Flasheando via BROM (MTK Client)
Como o Fastboot da Motorola é severamente bloqueado neste aparelho, faremos toda a instalação de forma forçada pelo **Modo BROM** usando o MTK Client.

**⚠️ MUITO IMPORTANTE SOBRE OS CAMINHOS DOS ARQUIVOS:**
Para que o programa encontre os arquivos da ROM, você tem duas opções:
* **Opção 1 (A mais fácil):** Copie todos os arquivos `.img` que você baixou (super, boot, vbmeta, etc.) e cole-os **dentro da pasta do seu MTK Client**.
* **Opção 2 (Arraste e solte):** Em vez de digitar apenas `vbmeta.img`, você arrasta o arquivo para dentro do PowerShell para ele preencher o caminho completo sozinho.

Com o **celular desligado**, abra o PowerShell na pasta do **MTK Client**. Para cada comando abaixo, digite no PowerShell, dê Enter, segure **Volume Mais (+) e Volume Menos (-)** e conecte o cabo USB. Quando terminar, desconecte o cabo e vá para o próximo comando:

```powershell
# 1. Flasheando os VBMETAs modificados para não dar Bootloop de segurança
python mtk.py w vbmeta,vbmeta_system,vbmeta_vendor vbmeta.img,vbmeta_system.img,vbmeta_vendor.img

# 2. Flasheando o kernel vacinado (Root) e o hardware respectivo
python mtk.py w boot,dtbo,vendor_boot boot.img,dtbo.img,vendor_boot.img

# 3. Flasheando o sistema principal
python mtk.py w super super.img

# 4. Apagando (Erase) os dados residuais e criptografia (Wipe Data)
python mtk.py e userdata,metadata

# 5. Reiniciando o aparelho
python mtk.py reset
```

O primeiro boot pode demorar de 5 a 10 minutos. Tenha paciência. Ao iniciar, você terá o Android 16 PIXELOS e o aplicativo do Magisk já indicará que o Root está ativo!

---

## 🛑 Troubleshooting e Recuperação de Desastres

### 1. Estou preso no Bootloop (O sistema não inicia, só reinicia na logo)
Isso geralmente ocorre se o Wipe Data falhou ou os arquivos `vbmeta` não foram gravados corretamente.
- Com o celular desligado, repita os comandos de vbmeta e limpeza de dados no **MTK Client**:
```powershell
python mtk.py w vbmeta,vbmeta_system,vbmeta_vendor vbmeta.img,vbmeta_system.img,vbmeta_vendor.img
python mtk.py e userdata,metadata
```

### 2. Destruí o sistema e não entra nem no Fastboot (Hard Brick / BROM Mode Loop)
Não entre em pânico, é um aparelho MediaTek (MTK), quase tudo pode ser restaurado.
- Você pode usar o **MTK Client** para regravar as partições usando o seu backup, ou regravar a partição boot original.

### 3. Restauração Padrão de Fábrica Oficial (Ferramenta da Motorola)
Se quiser voltar ao Android Original bloqueado e de fábrica, use o **Rescue and Smart Assistant (RSA)**. O Moto G22 é MediaTek, então a recuperação tem um detalhe muito importante para ser reconhecido:
1. Abra o programa RSA no Windows.
2. Vá em **Resgatar (Rescue)** > Clique em "Telefone" ou insira o modelo do Moto G22. Baixe a Firmware oferecida pelo programa e inicie o processo.
3. O programa vai pedir: *Passo 1: Desligue o dispositivo* / *Passo 2: Conecte o dispositivo ao PC*. Se você estiver na tela do Fastboot, ele **NÃO vai avançar**.
4. **O Segredo para reconhecer:** No modo Fastboot do celular, use os botões de volume para selecionar "Power Off" (Desligar) e aperte Power para confirmar. Desconecte o cabo.
5. Com o aparelho **100% desligado e tela preta**, pressione e **segure a tecla de Volume Menos (-)** e, sem soltar, **conecte o cabo USB no PC**. O programa fará o reconhecimento na hora e iniciará o download/flash para o seu aparelho!

### 4. Erro ao rodar ADB: "O executável não é um aplicativo válido"
Se você tentar rodar `.\adb.exe` e o PowerShell disser que não é um aplicativo válido, significa que o download do *Platform-Tools* falhou ou a extração do arquivo ZIP corrompeu o executável (ele fica com 0 bytes).
- **A solução:** Apague a pasta do platform-tools, baixe o ZIP novamente no site oficial do Android e extraia tudo novamente com cuidado.

---
**Desenvolvido para a Comunidade do YouTube! 🚀**
Espero que curtam essa build fluida. Lembrem-se de nunca divulgar seus arquivos de `nvram` para a internet!

