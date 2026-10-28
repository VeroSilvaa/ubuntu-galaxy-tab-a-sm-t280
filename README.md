# Ubuntu no Galaxy Tab A 7.0 (2016) — SM-T280

Ubuntu 22.04 LTS (ARMHF) para o **Samsung Galaxy Tab A 7.0 2016, modelo SM-T280
(gtexswifi, Wi-Fi)**. O Ubuntu roda pelo **microSD** ou pode ser instalado na
**memória interna**; o Android continua disponível.

> Projeto em desenvolvimento. Use por sua conta e risco. Siga os passos na ordem
> e tenha o tablet com mais de 50% de bateria.

## O que funciona

- Boot automático, sem menu: **microSD com Ubuntu → Ubuntu na memória interna → Android**.
  Sem o cartão, o Android inicia normalmente.
- Wi-Fi (NetworkManager, `nmtui`), SSH pela rede e pelo cabo USB.
- Console na tela, com indicador de bateria; a tela apaga após 5 minutos sem uso
  e acende pelo botão Power.
- Assistente de primeiro acesso para criar o seu usuário.
- `install-ubuntu`: instalação na memória interna, **backup e restauração do Android**.

Ainda não disponível nesta versão: interface gráfica (Desktop), som, Bluetooth
no Ubuntu, câmera.

## Download

Baixe o arquivo **`ubuntu-minimal`** da página de
[Releases](../../releases) e extraia. Ele contém 3 arquivos:

| Arquivo | Para que serve | Como instalar |
|---|---|---|
| `twrp-gtexswifi-recovery.tar.md5` | Recovery TWRP | Odin, no modo download |
| `ubuntu-gtexswifi-boot-twrp.zip` | Boot que inicia o Ubuntu (e o Android) | Pelo TWRP |
| `ubuntu-gtexswifi-sd.img.xz` | Sistema Ubuntu para o microSD | balenaEtcher ou Rufus |

## Instalação

### 1. TWRP (se ainda não tiver)

1. No PC com Windows, instale os drivers USB da Samsung e abra o **Odin**.
2. Desligue o tablet. Ligue em **modo download**: segure **Volume para baixo + Home + Power**
   e confirme com **Volume para cima**.
3. Conecte o cabo USB. No Odin, clique em **AP** e escolha `twrp-gtexswifi-recovery.tar.md5`.
4. Em *Options*, desmarque **Auto Reboot**. Clique em **Start** e aguarde **PASS**.
5. Desligue o tablet (segure **Volume para baixo + Power**) e entre direto no TWRP:
   segure **Volume para cima + Home + Power**.

### 2. Backup do Android (recomendado)

No TWRP, use **Backup** e marque **Boot**, **System** e **Data**, salvando no microSD
ou num PC. Isso permite voltar exatamente ao Android atual.

### 3. Boot do Ubuntu

1. Copie `ubuntu-gtexswifi-boot-twrp.zip` para o tablet ou para o microSD.
2. No TWRP: **Install** → escolha o zip → deslize para confirmar.
3. O instalador confere o aparelho e o arquivo, grava o boot e confere a gravação.

Depois disso, sem o microSD do Ubuntu o tablet continua iniciando o Android.

### 4. microSD com o Ubuntu

1. No PC, use o **balenaEtcher** (Windows, Linux, macOS) ou o **Rufus** (Windows).
   Cartão de pelo menos 8 GB.
   - balenaEtcher: *Flash from file* → `ubuntu-gtexswifi-sd.img.xz` → escolha o cartão → *Flash*.
   - Rufus: escolha o cartão e a imagem; se perguntar, use o **modo DD**.
2. Coloque o cartão no tablet e ligue. No primeiro boot a partição do cartão é
   expandida para o tamanho total.

### 5. Primeiro acesso

1. Na tela de login, entre com usuário **`ubuntu`** e senha **`ubuntu`**
   (teclado USB com adaptador OTG ou pelo SSH, abaixo).
2. O assistente abre sozinho: informe o **seu usuário**, a **senha** e o nome do aparelho.
3. Saia (`exit`) e entre com o seu usuário. O usuário `ubuntu` é removido no próximo boot.

### 6. Wi-Fi e SSH

- Configurar o Wi-Fi: `sudo nmtui` → *Activate a connection* → escolha a rede.
- Comandos rápidos: `wifi up`, `wifi down`, `wifi status`, `wifi lista`.
- SSH pelo cabo USB: o tablet aparece como rede USB; `ssh SEU_USUARIO@192.168.77.2`.
- SSH pelo Wi-Fi: `ssh SEU_USUARIO@IP_DO_TABLET` (o IP aparece ao entrar no sistema).

### 7. (Opcional) Instalar na memória interna

Com o tablet rodando pelo microSD:

```
sudo install-ubuntu
```

- **Instalar o Ubuntu na memória interna**
  - *Partição existente (tabela preservada)*: só aparecem as partições onde o
    Ubuntu cabe (`SYSTEM` ou `userdata`). As outras partições do Android são
    limpas e a tabela de partições não muda — dá para instalar uma ROM depois
    sem restaurar nada.
  - *Juntar partições vizinhas*: une partições para ter mais espaço (até ~7 GB).
    Para voltar ao layout original, use **Restaurar**.
  - Antes de alterar qualquer coisa, o programa salva no microSD um **backup
    completo do Android** e da tabela de partições.
- **Fazer backup** / **Restaurar** / **Verificar** o Android interno a qualquer momento.

Depois de instalar: desligue, retire o microSD e ligue. Com o cartão presente,
ele sempre tem prioridade.

**Guarde o microSD**: os backups do Android ficam nele, em `/var/backups/gtexswifi/`.

> **Atenção com ferramentas de partição**: a tabela da memória interna segue o
> layout da Samsung, e o `fdisk` avisa *"The backup GPT table is not on the end
> of the device"*. Isso é normal. Use apenas `sudo fdisk -l` (leitura); **não
> grave** a tabela com `fdisk`, `gdisk` ou `parted`, pois isso altera o layout
> que o bootloader e o Odin esperam. Para mudar partições, use o `install-ubuntu`.

## Voltar ao Android

- Com o Android ainda instalado: basta iniciar sem o microSD do Ubuntu.
- Se instalou o Ubuntu na memória interna: inicie pelo microSD e use
  `sudo install-ubuntu` → **Restaurar um backup**.
- Boot original: restaure o backup **Boot** feito no TWRP (passo 2).

## Problemas comuns

| Sintoma | O que fazer |
|---|---|
| Tela preta ou só a logo depois de gravar o boot | Sem o cartão, deve iniciar o Android em ~10 s; confira se o cartão foi gravado com a imagem certa |
| Login recusado no primeiro acesso | Entre primeiro com `ubuntu`/`ubuntu`; o seu usuário só existe depois do assistente |
| Sem Wi-Fi | `wifi status`; o log de inicialização fica em `/run/wifi-init.log` |
| Tela apagada | Aperte o botão Power |

## Créditos

- Kernel base: [underscoremone/android_kernel_samsung_gtexswifi](https://github.com/underscoremone/android_kernel_samsung_gtexswifi)
- Referências: [postmarketOS — samsung-gtexswifi](https://wiki.postmarketos.org/wiki/Samsung_Galaxy_Tab_A_7.0_(2016)_(samsung-gtexswifi))
