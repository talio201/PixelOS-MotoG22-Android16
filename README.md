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

## Passo 3: Desbloqueando o Bootloader

Existem três formas de desbloquear o bootloader do Moto G22. Escolha a que preferir (lembrando que **todas apagarão seus dados**):

### Método 1: Direto pelo Fastboot (Mais Rápido)
1. Com o celular ligado normalmente e conectado via cabo, abra o PowerShell na pasta do **Platform-Tools**:
```powershell
.\adb.exe reboot bootloader
```
2. No modo Fastboot, digite:
```powershell
.\fastboot.exe flashing unlock
```
3. Na tela do celular, use os botões de volume para selecionar "Unlock the bootloader" e o botão Power para confirmar.

### Método 2: Via BROM com MTK Client (Forçado / Sem código da Motorola)
Se o método acima falhar ou estiver bloqueado, o MTK Client pode forçar o desbloqueio alterando a partição de segurança.
1. Com o celular desconectado e desligado, prepare o comando no PowerShell da pasta do **MTK Client**:
```powershell
python mtk.py da seccfg unlock
```
2. Segure os botões **Volume Mais (+) e Volume Menos (-)** e conecte o cabo USB. O programa fará o desbloqueio instantaneamente.

### Método 3: Método Oficial da Motorola (Pelo Site)
Caso prefira fazer pelo método oficial, recebendo a chave da fabricante:
1. No modo Fastboot, digite:
```powershell
.\fastboot.exe oem get_unlock_data
```
2. Copie o código gerado, junte todas as linhas em uma só (removendo os espaços) e acesse o [Site de Desbloqueio da Motorola](https://motorola-global-portal-pt.custhelp.com/app/standalone/bootloader/unlock-your-device-a).
3. Insira o código no site e solicite a chave de desbloqueio. Você receberá uma chave exclusiva de 20 caracteres no seu e-mail.
4. Volte ao Fastboot e digite o comando usando a chave que você recebeu:
```powershell
.\fastboot.exe oem unlock SUA_CHAVE_AQUI
```
5. Confirme na tela do celular usando os botões de volume e o botão Power.

---

## Passo 4: Instalação da ROM e do Boot Vacinado (Root)

Nesta pasta de lançamento, você encontrou os seguintes arquivos:
- `super.img` (Sistema Completo Android 16 PIXELOS)
- `boot.img` (Kernel Vacinado / Rooted Magisk)
- `vendor_boot.img`
- `dtbo.img`
- Arquivos `vbmeta.img`, `vbmeta_system.img`, `vbmeta_vendor.img`

### Entrando no FastbootD
O Moto G22 usa partições dinâmicas. O `super.img` deve ser instalado no modo `fastbootd`.

1. Reinicie no bootloader:
```powershell
.\adb.exe reboot bootloader
```
2. Entre no FastbootD:
```powershell
.\fastboot.exe reboot fastboot
```
*A tela do celular mudará e indicará "fastbootd".*

### Flasheando os Arquivos
No PowerShell, dentro da pasta onde estão os arquivos baixados, execute:

```powershell
# Desativando a verificação de integridade (AVB) para não dar Bootloop
.\fastboot.exe --disable-verity --disable-verification flash vbmeta vbmeta.img
.\fastboot.exe --disable-verity --disable-verification flash vbmeta_system vbmeta_system.img
.\fastboot.exe --disable-verity --disable-verification flash vbmeta_vendor vbmeta_vendor.img

# Flasheando o kernel vacinado (Root) e o hardware respectivo
.\fastboot.exe flash boot boot.img
.\fastboot.exe flash dtbo dtbo.img
.\fastboot.exe flash vendor_boot vendor_boot.img

# Flasheando o sistema principal
.\fastboot.exe flash super super.img

# Formatando dados para remover criptografia residual (Wipe Data)
.\fastboot.exe -w

# Reiniciando o aparelho
.\fastboot.exe reboot
```

O primeiro boot pode demorar de 5 a 10 minutos. Tenha paciência. Ao iniciar, você terá o Android 16 PIXELOS e o aplicativo do Magisk já indicará que o Root está ativo!

---

## 🛑 Troubleshooting e Recuperação de Desastres

### 1. Estou preso no Bootloop (O sistema não inicia, só reinicia na logo)
Isso geralmente ocorre se o `Wipe Data` falhou ou o `vbmeta` não foi desativado corretamente.
- No Fastboot normal (não no Fastbootd), execute:
```powershell
.\fastboot.exe -w
.\fastboot.exe --disable-verity --disable-verification flash vbmeta vbmeta.img
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

