# **Curso GNU/Linux - Kretcheu**

## **Sumário**

### **Aulas**:

> [A_001 - Software Livre](#a001)

> [A_002 - GNU (GNU is not Unix)](#a002)

> [A_003 - Distribuição Debian](#a003)

> [A_004 - Instalar SO](#a004)

> [A_005 - Instalar Debian](#a005)

> [A_008 - Modelo mental do boot](#a008)

> [A_009 - Princípios do UNIX](#a009)

> [A_011 - FHS - Filesystem Hierarchy Standard](#a011)

> [A_012 - Comandos Básicos - BASH](#a012)

> [A_013 - Editores de texto puro](#a013)

> [A_014 - Manual de sobrevivência no vim](#a014)

> [A_015 - Pacotes](#a015)

### **Solução de Problemas**:

> [SP_000 - Redefinir senha do Root](#sp000)

> [SP_001 - Shell do GRUB (interpretador de comando)](#sp001)

### **Tutoriais**:

> [T_000 - Copiar chaves para servidor ssh](#t000)

> [T_001 - Alterar layout/design do prompt do terminal](#t001)

> [T_002 - Instalar Linux-Libre no Debian](#t002)

> [T_003 - Swap em arquivo criptografado](#t003)

### **Outros**:

> [O_001 - Desvendando Wi-Fi](#o001)

---

## A_001 - Software Livre <a name="a001"></a>

**As 4 liberdades** (definem um software livre):
   * 0 → Qualquer uso
   * 1 → Estudar, adaptar
   * 2 → Distribuir cópias
   * 3 → Redistribuir melhorias

**Licenças Livres**: 
   * **Copyleft**: garante as 4 liberdades (licença é herdada)
   * **Permissiva**: garante as 4 liberdades (não necessariamente precisa herdar a licença)

 GPL (GNU Public License) - licença do Linux.

---

## A_002 - GNU (GNU is not Unix) <a name="a002"></a>

Richard Stallman - 1983 (GNU)

Linus Torvalds - 1991 (Kernel Linux)

**Principais componentes de um Sistema Operacional**: 
   * Bootloader
   * Kernel
   * Bibliotecas (gcc, glibc, ...)
   * Serviços/Aplicação

*Blobs* - Componentes de software não-livres.

---

## A_003 - Distribuição Debian <a name="a003"></a>

Ian Murdock - 1993 (Debora + Ian)

2ª distro mais antiga (Distro mais antiga é o Slackware)

Grupo de pessoas dão manutenção ao Debian (2000+)

**Debian Free Software Guidelines** (DFSG):
   1. Redistribuição livre
   2. Código fonte
   3. Trabalhos derivados
   4. Integridade do código fonte do autor
   5. Não há discriminação contra pessoas ou grupos
   6. Não há discriminação contra fins de utilização
   7. Distribuição de licença
   8. A licença não pode ser específica para o Debian
   9. A licença não deve contaminar outros softwares
   10. Licença exemplo -> GPL, BSD, ...

**Seções do repositório oficial do Debian**:
   * **main** - contém apenas software livre (de acordo com a DFSG)
   * **contrib** - contém software livre com a funcionalidade de carregar software não-livre
   * **non-free** - contém apenas software não-livre

**Etapas do desenvolvimento do Debian** (Branches):
   * **Old-stable** - Seção contendo pacotes que já possuem novas versões estáveis
   * **Stable** - Seção estável que já passou pelas seções Unstable e Testing
   * **Testing** - Seção contendo os pacotes que já passaram pela seção Unstable (maturação: ~2 anos)
   * **Unstable** (Sid) - Seção contendo os novos pacotes do Debian que ainda estão em testes iniciais
   * **Experimental** - Seção usada para testes experimentais

**Arquiteturas suportadas oficialmente**: amd64, arm64, armel, armhf, i386, mips64el, mipsel, ppc64el, s390x

Versão atual (2021): Debian 11 - bullseye

---

## A_004 - Instalar SO <a name="a004"></a>

**Firmware do PC**:
   * (Firmware/Sistema de Particionamento)
   * BIOS/MBR
   * UEFI/GPT

**Bootloader**: programa que roda para permitir que o SO possa ser carregado

**Instalação**: processo de cópia dos arquivos do SO para o HD/SSD da máquina

**Pendrive bootável**: rufus, yumi, balena-edge, comando dd (no Linux)
**Dual boot**: Primeiro instalar Windows, depois Linux (Windows pode dar problema no GRUB)

---

## A_005 - Instalar Debian <a name="a005"></a>

**Durante a instalação no modo gráfico, digitar**:
   * CTRL + ALT + F4 para ver a tela de log
   * CTRL + ALT + F2 para acessar um terminal
   * CTRL + ALT + F5 para voltar para o modo gráfico

**Particionamento de disco**:
   * **/**     -> arquivos de administrador (root) - ext4
   * **/home** -> arquivos dos usuários - ext4
   * **swap**  -> memória virtual

Problemas de **Boot**: UEFI ou Legacy

Problemas de **Rede**: baixar firmware não-livre (placa de wifi - usar cabo), mais de uma placa de rede (escolher uma), rede que não atribui IP (reconfigurar, IP estático)

Problemas com **GRUB**: correto -> UEFI-GPT / BIOS-MBR (usar disco primário caso haja mais de 1)

Problemas de **particionamento**: Usar Modo Manual

---

# A_008 - Modelo mental do boot <a name="a008"></a>

**Firmware**:
   * **BIOS** -> (Basic Input Output System) - anos 80 (máquinas mais antigas) - Legacy
   * **UEFI** -> mais inteligente (conhece sistema de particionamento GPT, DOS, sistema de arquivos FAT; permite arquivos executáveis PE (Portable executable); funciona no modo secure-boot - só carrega o que foi assinado por certificados reconhecidos pela máquina)
   * **Outros**: coreboot, libreboot

**Particionamento**:
   * **dos/MBR**: 4 partições, max 2TB cada (mais antigo)
        * MBR (Master Boot Record)-> primeiros 512 bytes (stage 0)
        	* 64 bytes iniciais contém as tabelas de partições
        	* 446 bytes contém parte do bootloader (stage 1)
        * stage 1,5 -> contém o resto do bootloader
        * Resto do espaço contém as partições em si.

<img width="400" alt="mbr" src="https://user-images.githubusercontent.com/66706870/131202089-57f4aa26-895a-4277-afda-8eb08e4ed745.png">

   * **GPT**: GUID (Globally Unique Identifiers) Partition Table -> 128 partições, max 9ZB cada (1ZB = 1 bilhão de TB)
        * Protective MBR (Igual ao MBR anterior)        
        * GPT primária (contém tabela de partições)
        	* Primary GPT Header
        	* Entry 1 | Entry 2 | Entry 3 | Entry 4
        	* Entries 5-128
        * Partições
        	* Partition 1
        	* Partition 2
        	* Remaining Partitions
        * GPT Secundária (cópia da GPT primária - redundância)
        	* Entry 1 | Entry 2 | Entry 3 | Entry 4
        	* Entries 5-128
        * Secundary GPT Header

<img width="420" alt="gpt" src="https://user-images.githubusercontent.com/66706870/131202341-190626e9-8288-4bf8-8071-438fc0d0ff7f.png">

**Bootloader**:
   * GRUB (uefi) - grub4.efi + módulos (/boot)

**Kernel**:
   * /vmlinuz (é um arquivo compactado)
   * /boot/vmlinuz-5.6.13-gnu (5~15MB) -> built-in
   * módulos - ~300MB

**Initrd**:
   * /initrd.img (imagem de disco inicial)
   * /boot/initrd.img-5.6.13-gnu (8~20MB)
   * possui módulos do kernel que não estão built-in

**Modelo Mental:**

<img width="569" alt="modelo_mental" src="https://user-images.githubusercontent.com/66706870/131203145-43facd54-9ca2-42f8-ba93-d6f95f78e522.png">

1. POST: Power On Self Test 
   * Testa se tem memória, se tem placa de vídeo, se barramentos estão ok, ...
   * Verifica a presença e conteúdo da BIOS
   * Geralmente sinaliza problemas por BIPS sonoros

*Com BIOS*:

2. Roda programas da BIOS (roda outros testes) - verifica qual é o primeiro dispositivo de boot (até achar MBR)

3. BIOS acessa conteúdo do MBR (Master Boot Record - 446 bytes localizados no primeiro dispositivo da memória) - Objetivo do MBR é carregar o GRUB

4. GRUB é o bootloader

*Com UEFI*:

2. Roda programas da UEFI (programa executável no formato PE) -> UEFI consegue rodar o GRUB diretamente. Se tiver secure-boot, a UEFI roda o shim (pré-bootloader assinado digitalmente que acessa o GRUB). É possível que a UEFI carregue diretamente o kernel + initrd sem precisar de GRUB

*Ambos*:

5. GRUB procura por um arquivo de configuração (+ módulos GRUB) e carrega o initrd e prepara um sistema de arquivos virtual. Além disso, o GRUB também carrega o kernel. O kernel em funcionamento monta o sistema de arquivos virtual na sua estrutura de diretórios. No FS virtual existe um script chamado "init" que permite com que o kernel carregue alguns módulos necessários para montar o FS real.

6. Com o diretório / montado, o primeiro programa a rodar é o systemd, que coloca todos os serviços configurados em funcionamento.

---

## A_009 - Princípios do UNIX <a name="a009"></a>

**1965** -> Multics - Ken Thompson (criou linguagem B e Go) e Dennis Ritchie - Bell Labs, MIT, AT&T

**1969** -> Unics (Depois renomeado para Unix) - Linguagem Assembly

**1973** -> Unix - Linguagem C

**Ideias**:
   * Programas devem fazer uma única coisa, mas fazer isso muito bem! (keep it simple)
   * Programas devem conversar entre si e e trabalhar juntos! (saída de um programa = entrada de outro programa - pipe)

**Abstração**: 
   * Tudo é representado por arquivos (arquivos, diretórios, hardware, processos, etc).
   * Estrutura em árvore hierárquica (base é o diretório /). FHS - Filesystem Hierarchy Standard.

---

## A_011 - FHS - Filesystem Hierarchy Standard <a name="a011"></a>

References: 
[Filesystem Hierarchy Standard](https://refspecs.linuxfoundation.org/fhs.shtml) 
/
[The Directory Tree](https://www.debian.org/releases/stable/amd64/apcs02.en.html)


*Origem*: 1996, comunidade BSD 

*Objetivo*: Uniformização (sistemas Unix-like)

Mantido hoje pela Linux Foundation

**Principais Diretórios**:

**/**          → diretório barra ou raiz

**/proc**  → processos (info dos processos em execução e do sistema)

**/dev**   → dispositivos (hd, pendrive, mouse, teclado, etc)

**/boot**  → kernel, initrd (arquivos do GRUB)

**/bin**   → binários (E) (essenciais pro sistema)

**/sbin**  → binários adm (essenciais para adm do sistema)

**/lib**   → bibliotécas (libs usadas pelos binários /bin e /sbin)

**/etc**   → configuração (configs dos programas)

**/media** → removíveis (aqui são montados os conteúdos de removíveis)

**/mnt**   → temporários (serve para montagens de dispositivos temporários para uso do administrador)

**/root**  → usuário root

**/home**  → usuários (cada usuário possui sua pasta)

**/tmp**   → temporário (serve para armazenar arquivos temporários)

**/var**   → variável (geralemnte conteúdo é apagado ao reiniciar o sistema) - possui: /log
    
**/usr/bin** → binários (binários não essenciais: instalação, etc)

**/usr/sbin** → binários adm

**/usr/lib** → bibliotecas (libs usadas pelos /usr/bin e /usr/sbin)

**/usr/share** → independente (arquivos independentes de arquitetura - 32 ou 64 bits)

**/usr/share/doc** → documentação

**/usr/share/man** → manuais

---

## A_012 - Comandos Básicos - BASH <a name="a012"></a>

References: 
[Tutorial do Bash](http://www.inf.ufpr.br/cursos/ci055/artigos_linux_veteranos/gbbs14.pdf)
/
[Comandos Básicos no Linux](https://cotidianoti.com.br/Comandos-basicos-linux/)

**clear**  → limpa terminal (CTRL + L)

**pwd**    → mostra caminho absoluto do diretório corrente

**cd**     → change directory

**ls**     → list (opções: -alhrt)

**cp**     → copy

**mv**     → move (move, muda nome ou atualiza informações sobre um arquivo/dict - hora da última atualização)

**rm**     → remove

**mkdir**  → make directory

**rmdir**  → remove directory (only empty directories)

**rm -rf** → remove directory recursive (and all its contents)

**cat**    → mostra conteúdo do stdout de um programa ou conteúdo de um arquivo (opções: -n (numero de linhas))

**less**   → mostra conteúdo de um arquivo de forma paginada
    :pesquisa: /  
    :sair: q

**grep**   → filtro - Ex: grep chave arquivo.txt (permite regex)

**find**   → procura - Ex: find /home -name filename 

**tail**   → mostra últimas linhas de texto de um arquivo (-)

**head**   → mostra primeiras linhas de texto de um arquivo

**wc**     → conta numero de linhas, palavras e caracteres de um arquivo (opções: -lwc)

---

*--help* → mostra principais opções e formas de usar programa

*man*    → mostra manual mais detalhado do programa

---

**|**      → pipe - saída de um programa (stdout) é a entrada de outro (stdin)

**>**      → redireciona stdout (reescreve todo o arquivo que recebe)

**2>**     → redireciona stderr (/dev/null - diretório para ignorar)

**>>**     → append (adiciona no arquivo sem apagar o que já existe)

__*__      → caractere coringa (qualquer coisa)

**.**      → diretório atual

**..**     → diretório pai do atual (anterior)

**-**      → último diretório que estava

---

Permissões: `ls -l`

**d rwx r-x r-x 1 user user 4096 mai 9  01:44 file.txt**

1---2---3--4--5--6---7----8----9--10---11----12

1. Tipo de arquivo (d=directory / -=arquivo / l=symbolic link)
2. Permissão para o dono (criador) do arquivo
3. Permissão para usuários pertencente ao grupo de usuário
4. Permissão para outros usuários
5. Níveis de diretórios
6. Usuário que criou o arquivo (dono)
7. Grupo de usuário dono do arquivo
8. Tamanho do arquivo (bytes)
9. Mês última modificação
10. Dia última modificação
11. Hora última modificação
12. Nome do arquivo

---

## A_013 - Editores de texto puro <a name="a013"></a>

* **Texto Puro** (Plain Text): codificação ASCII (1 byte), unicode (UTF-8 - 1 a 4 bytes)

* **Modo Gráfico**: pluma, gedit, geany, kate, kwrite, ...

* **Modo Texto** (terminal): vi, vim-basic, vim (gtk), nvi, nano, mcedit, ...

* **GNU Emacs** (Richard Stallman) - linguagem Lisp 

---

## A_014 - Manual de sobrevivência no vim <a name="a014"></a>

References: 
[Github - calangohc/apresentacoes](https://github.com/calangohc/apresentacoes/tree/master/vim-magnun)
/
[YouTube](https://www.youtube.com/watch?v=Efh3OTnG74E)

**Modos de Operação**:
   * **Comando** (normal) → serve para navegar no conteúdo do arquivo
   * **Inserção** → serve para inserir/editar o texto do arquivo
   * **Visual** → seleção visual do texto
   * **Linha de Comando** → pode escrever comandos para processar texto

 __________               ___________              ____________
|          |    esc  →   |           |  a, i, o → |            |
|  visual  |             |  comando  |            |  inserção  |
|__________| ← CTRL + v  |___________|    ← esc   |____________|
                ou  V     ↓ :   ↑ esc
                          ___________
                         |           |
                         | linha de  |     
                         |  comando  |
                         |___________|


> Editar arquivo de configuração padrão do vim:
`sudo nano /etc/vim/vimrc`
> Descomendar:
`syntax on`
`set background=dark`

* Chamada do programa: [vi|vim] [file]

> Comandos do modo COMANDO:

y + y : copia linha que o cursor está em cima (yunk)
2 + y + ↑ : copia a linha atual + 2 linha acima dela

p : cola o que foi copiado na linha abaixo de onde o cursor está em cima (paste)
SHIFT + p : cola o que foi copiado na linha acima de onde o cursor está em cima

d + d : deleta linha que o cursor está em cima (delete)
2 + d + ↑ : deleta a linha atual + 2 linha acima dela

u : desfazer último comando (undo)
CTRL + r : refazer último comando (redo)

i: entra no modo de INSERÇÃO (cursor fica exatamente onde está)
a: entra no modo de INSERÇÃO (cursor vai um caractere para a direita)
o: entra no modo de INSERÇÃO e cria uma nova linha a abaixo da linha onde o cursor estava (cursor fica no início desta nova linha)

g + g : vai para a primeira linha do arquivo
SHIFT + g : vai para a última linha do arquivo
50 + % : vai pra metade do arquivo (percentagem que quiser)
/ + <palavra_pesquisada>: pesquisa se existe alguma linha com a palavra buscada
    n : busca linhas encontradar navegando para baixo
    SHIFT + n : busca linhas encontradar navegando para baixo

v (entra no modo visual) + seleciona trecho de texto + y → copiar seleção de texto
v (entra no modo visual) + seleciona trecho de texto + d → deletar seleção de texto
CTRL + v + seleciona pro lado e pra baixo → seleção por bloco

w → avança o cursor palavra por palavra
$ → vai pro final da linha
^^ → vai pro começo da linha

> Comandos do modo LINHA DE COMANDO:

:x  → salvar conteúdo do arquivo e fechar programa (:wq)
:w  → salvar conteúdo do arquivo sem fechar programa
:q  → sair sem salvar (quando o arquivo não foi alterado)
:q! → sair sem salvar mesmo se houver alteração no arquivo

:%s/string1/string2/g → substituir string1 por string2 de forma global (g)

---

## A_015 - Pacotes <a name="a015"></a>

REF: []


---
---
---

## SP_000 - Redefinir senha do Root <a name="sp000"></a>

> Redefinir senha do root pelo GRUB. 

1. Na tela do GRUB, digitar "e" 
2. Na linha contendo 'linux', substituir "ro quiet" por "rw init=/bin/bash"
3. Apertar "F10" para dar boot
4. Feito isso, após o boot o terminal irá abrir já no modo root
   Digite o comando 
   `passwd`
   Digite a nova senha 2 vezes
5. (CTRL + ALT + DELETE) para dar reboot na máquina

OBS: Com acesso físico a maquina, não há segurança, pois é possível mudar a senha de root!

---

## SP_001 - Shell do GRUB (interpretador de comando) <a name="sp001"></a>

> GRUB não encontra o arquivo de configuração e abre o shell do GRUB. O que fazer?

`grub> set pager=1` > Configura paginador no grub
`grub> help`        > Mostra todos os comandos do shell do grub

1. Descobrir quais partições existem:
`ls`
Ex: (disco, partição) -> (proc) (hd0) (hd0, gpt2) (hd0, gpt1) (cd0)
`ls (hd0, gpt1)/`
Ex: efi/ refind_linux.conf
`ls (hd0, gpt2)/`
Ex: lost+found/ boot/ bin lib sbin dev/ etc/ home/ ... (SISTEMA ESTÁ NESTA PARTIÇÃO)
`ls (hd0, gpt2)/boot/`
Ex: vmlinuz* (Arquivo de interesse)

2. Descobrir em qual partição do disco deve ser instalado o diretório root "/":
`cat (hd0, gpt2)/etc/fstab`
3. Comando para carregar o kernel na memória:
`linux (hd0, gpt2)/boot/vmlinuz-5.6.13.gnu root=/dev/sda0`
4. Carregar initrd:
`initrd (hd0, gpt2)/boot/initrd.img-5.6.13.gnu`
5. Dar boot:
`boot`

OBS: Após entrar no sistema, ir no terminal e digitar o comando de atualização do grub:
`update-grub`
`shutdown -r now`

---
---
---

## T_000 - Copiar chaves para servidor ssh <a name="t000"></a>

REF: [https://salsa.debian.org/kretcheu/tutoriais/-/blob/master/copiar.chave.md]

> Copiar chave criptográfica para logar sem senha num servidor ssh:

1. Criar um par de chaves (pública e privada) no PC Cliente:
`ssh-keygen`
   Gera uma chave do tipo RSA (padrão: /home/<user>/.ssh/id_rsa)
   Escolher passphrase para criptografar chaves (SHA256)              ---  Senha : ssh-coreapp
2. Verificar chaves criadas no diretório .ssh:
`ls -l /home/<user>/.ssh`
   id_rsa -> chave privada
   id_rsa.pub -> chave pública
3. Copiar a chave pública para o servidor:
`ssh-copy-id <ip do servidor>`
Em caso de erro:
`ssh-keygen -f "/home/<user>/.ssh/known_hosts" -R "<ip do servidor>"`
Ou copiar usando o comando scp
4. Acessar o servidor:
`ssh <user>@<ip do servidor>`
5. Verificar arquivo criado dentro do diretório .ssh no servidor:
`ls -l /home/<user>/.ssh`
   authorized_keys -> arquivo contendo as chaves públicas dos clientes com autorização para acesso ao servidor ssh

> Copiar a chave pública para o /root para acessar servidor como root (não é uma prática muito boa - pode usar "su -" no servidor)
`cat /home/<user>/.ssh/authorized_keys >> /root/.ssh/authorized_keys`

No PC cliente:
> Acessar o servidor como root
`ssh root@<ip do servidor>`
> Adicionar chave privada no agent para não pedir senha ao logar
`ssh-add ~/.ssh/id_rsa`

---

## T_001 - Alterar layout/design do prompt do terminal <a name="t001"></a>

> Para todos os usuários: /etc/bash.bashrc
> Para um usuário:       ~/.bashrc

Modificar variável PS1

* Script com modelos de terminal: [https://libregit.org/kretcheu/devel/src/branch/master/prompt]'

---

## T_002 - Instalar Linux-Libre no Debian <a name="t002"></a>

> Editar /etc/apt/source.list e adicionar linha abaixo:
`deb http://linux-libre.fsfla.org/pub/linux-libre/freesh/ freesh main`

> Baixar chave pública do repositório: 
`wget https://jxself.org/gpg.inc`

> Mostrar a Fingerprint que está no arquivo gpg.inc:
`gpg --keyid-format long --with-fingerprint gpg.inc`

> Verificar se confere com a fornecida pelo site oficial:
# Key finderprint = F611 A908 FFA1 65C6 9958 4ED4 9D0D B31B 545A 2198

> Incluir chave pública no chaveiro do apt:
`apt-key add < gpg.inc`

> Atualizar lista de pacotes dos repositórios apt:
`apt update`

> Instalar Linux-Libre:
`apt install linux-libre`
`reboot`

---

## T_003 - Swap em arquivo criptografado <a name="t003"></a>

REF: [https://salsa.debian.org/kretcheu/tutoriais/-/blob/master/swap.criptografado.md]
     [https://salsa.debian.org/kretcheu/tutoriais/-/blob/master/swap.on.file.criptografado.md]

> Swap - espaço de troca (Memória Virtual).
> Memória Total = Memória Física (RAM) + Swap
> Geralmente swap é colocado em uma partição do disco /dev/sdaX

> É possível encontrar senhas ou informações críticas a partir do swap em partições ou arquivos não criptografados (o comando strings busca linhas de texto contidas dentro de um dispositivo ou arquivo):
```
strings /dev/sdaX > arquivo_temporario
grep senha arquivo_temporario 
# ou 
less arquivo_temporario
```

* Swap em arquivo (muito parecido para swap em disco):
-------------------------------------------------------

1. Verificar memória disponível
`free`
2. Desligar swaps já configurados no disco se existir
`swapoff -a` 
# ou 
`swapoff /dev/sdaX`
3. Criar arquivo de swap de 3GB (bs = tamanho de bloco) 
`fallocate -l 3G /swapfile`
# ou
`dd if=/dev/zero of=/swapfile bs=1M count=3072`
4. Colocar permissão de leitura e escrita apenas para root no arquivo de swap
`chmod 600 /swapfile`

5. Editar arquivo de criptografia /etc/crypttab
```
#<target name> <source device> <key file>    <options>
cswap         /swapfile       /dev/urandom  swap,cipher=aes-xts-plain64,size=256,hash=sha1
```
6. Editar /etc/fstab (comentar a linha antiga e colocar a nova)
`/dev/mapper/cswap  none     swap     sw     0      0`
7. Instalar ferramenta cryptsetup-run
`apt install cryptsetup-run`
8. Preparar arquivo para ser swap criptografado
`cryptdisks_start cswap`
9. Ativar o swap
`swapon -a`

* Mais informações sobre o swap
`swapon --show`
# ou
`swapon --summary`

10. Configurar para que no próximo reboot o swap continue funcionando:
`echo "RESUME=none" > /etc/initramfs-tools/conf.d/resume`
`update-initramfs -u -k all`

---
---
---

## O_001 - Desvendando Wi-Fi <a name="o001"></a>

REF: [https://www.youtube.com/watch?v=Dyhmj57B_U8]
 ___________________________
|          kernel           |
|---------------------------|
| Módulo PCI     Módulo USB |      > Módulos built-in do kernel
|___________________________|
      ↓               ↓
`lspci -nnk`    `lsusb -tv`        > Lista conteúdo do que o kernel 
* Pacotes: pciutils / usbutils       conseguiu do barramento PCI/USB
                                     (id, módulo do kernel, ...)

`modinfo <módulo>`
* Mostra as inforações de um módulo (no Linux, avisa se precisa de um firmware na placa de Wi-Fi)

> Firmware: código que roda em um processador que não o principal da máquina (não roda pelo SO).


                Driver da placa Wi-Fi no Debian
                    ___________|____________
                   |                        |
                presente                 ausente
          _________|_________            ___|____
         | carregar firmware |          | código |
não, já está na placa       sim        não    compilar
         2               ____|____               |
                        |         |          não-livre
                      livre   não-livre          4
                        1         3

* Exemplo:
`lspci -nnk`
03:00.0 Network controller [0280]: Realtek Semiconductor Co., Ltd. RTL8723BE PCIe Wireless Network Adapter [10ec:b723]
	Subsystem: Hewlett-Packard Company RTL8723BE PCIe Wireless Network Adapter [103c:804c]
	Kernel driver in use: rtl8723be
	Kernel modules: rtl8723be
# ID: 10ec:b723 / Módulo: rtl8723be

# CASO 1 ou 3
Caso haja problemas com o firmware, pesquisar: 
Google: debian wiki 10ec:b723 
ou
`grep firm /var/log/syslog | grep fail`
Encontra o nome do firmware (.bin) que o módulo tentou rodar e falhou.
Buscar este nome de firmware no: packages.debian.org (procurar conteúdo dos pacotes)

Se decidir instalar o pacote:
# Baixar pacote
`apt install ./firmware-FAB.deb`
ou
Incluir seção non-free no repo descrito no /etc/apt/source.list
```
deb http://deb.debian.org/debian buster main non-free
apt update
apt install firmware-FAB
```

Descarrega módulo
`modprobe -r nome_do_modulo`
Carrega o módulo novamente
`modprobe nome_do_modulo`
Verificar se módulo carregou o firmware
`dmesg | grep firm`

VERIFICAR SE WIFI ESTÁ FUNCIONANDO
# ip a / ip l / ifconfig / iw dev / iwconfig

# CASO 4 - compilar firmware
Baixar fonte/firmware (git clone)
dkms -> para cada nova versão de kernel, recompila o módulo
Instalar -> dpkg/apt

---




