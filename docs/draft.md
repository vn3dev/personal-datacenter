[27/01/2026]

sudo nano /etc/netplan/00-installer-config.yaml
* abre o arquivo para configurar as redes do ubuntu com editor de texto como super user. O arquivo e o diretorio variam dependendo da distro linux. Debian por exemplo é: /etc/network/interfaces

* ja que arquivos sao lidos em ordem numerica, 00 tem prioridade maxima

* fluxo netplan:
* le yaml -> valida sintaxe -> gera as configs em systemd-networkd -> aplica as configs no boot ou no netplan apply

network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true

    enp0s9:
      dhcp4: true

    enp0s8:
      dhcp4: false
      addresses:
        - 10.10.10.10/24

sudo netplan apply


* HABILITAR ROTEAMENTO LINUX - descomentar linha net.ipv4.ip_forward=1 para aplicar no boot
* srv-core vai ser nosso gateway por enqt
sudo nano /etc/sysctl.conf

* regra de NAT para mascarar o ip do trafego que sai pela WAN
* insere regra na table nat
* -A de append. adiciona uma regra na chain POSTROUTING
* -o de out interface. quer dizer que essa regra so vai valer para os pacotes que sairem da rede enp0s3.
* -j de jump. pular para uma ação, no caso MASQUERADE, que vai substituir o ip da VM na LAN pelo ip WAN do srv-core
sudo iptables -t nat -A POSTROUTING -o enp0s3 -j MASQUERADE

* permitir forwarding. permite pacotes q chegam da LAN sejam encaminhados para a WAN
* -A FORWARD = da append da regra na chain FORWARD
* -i de in-interface para pacotes que vem da enp0s8 (LAN)
* -o de output para interface de saida enp0s3 (WAN)
* -j ACCEPT = comando de permitir
sudo iptables -A FORWARD -i enp0s8 -o enp0s3 -j ACCEPT

* bloqueia conexões da WAN para dentro da LAN
* append forward denovo
* input agora é aWAN e output é a LAN
* -m de mach module. filtra por estado de conexão "state"
* --state RELATED,ESTABLISHED define que só aceita pacotes se eles ja tiverem sido estabelecidos por dentro da LAN, RELATED para pacotes relacionados a uma conexão existente.
* -j ACCEPT para permitir
sudo iptables -A FORWARD -i enp0s3 -o enp0s8 -m state --state RELATED,ESTABLISHED -j ACCEPT

sudo apt update
sudo apt install iptables-persistent
* comando de save para depois >>>> sudo iptables-save > /etc/iptables/rules.v4
ls -l /etc/iptables/rules.v4
sudo cat /etc/iptables/rules.v4

### enfrentei um problema tentando deixar a config dos ip tables permanentes. Quando fiz o reboot, meu sistema congelou quando estava inicializando os drivers. O sistema estava usando recursos e parecia estar ativo mas não tive resposta até apertar enter. Fui jogado para um log com o crash e estou analisando possiveis soluções. No pior dos casos vou dar reboot no modo recovery e tentar desabilitar o carregamento automatico do netfilter-persistent e reverter regras de iptables/forwarding

[28/01/2026]

### consegui inicializar o srv-core desfazendo as regras. Ainda estou trabalhando na solução definitiva, talvez tenha que buscar conhecimentos em pfSense mais cedo que esperado. Decidi fazer o SSH por enquanto, aplicando as regras manualmente no srv-core para testar.

* no srv-core, atualizar a lista de pacotes e instalar ssh
sudo apt update
* porta 22
sudo apt install openssh-server

* continua apos o reboot
sudo systemctl enable ssh
sudo systemctl start ssh
sudo systemctl status ssh
* ver o ip MGMT, provavelmente 192.168.56.102
ip a

* usei gitbash para acessar no windows, acho que por powershell funciona também. talvez o comando seja diferente.
ssh labadmin@IP

* ssh de senha para chave:
* se ja nn tiver uma ssh key, gere uma no gitpash ou pshell
ssh-keygen
* de enter para manter o diretorio padrao
* escolha uma passphrase e pronto

* para ver os arquivos criados, confirme que os arquivos foram criados
ls ~/.ssh

* comando para copiar o id.pub e inserir a chave no authorized_keys direto no servidor
ssh-copy-id labadmin@ip
* colocar a senha de acesso pela ultima vez, agora teste se consegue conectar usando a passphrase em vez de senha:
ssh labadmin@ip
* depois de ter acesso sem utilizar a senha, vamos desabilita-la para evitar ataques de brute force e aumentar a segurança do nosso servidor

* NO SERVIDOR:
sudo nano /etc/ssh/sshd_config
* procure pelas linhas uma de cada vez: CTRL + W para barra de busca
#PasswordAuthentication yes
#PermitRootLogin prohibit-password
#PubkeyAuthentication yes
* descomente e altere os valores, deve ficar assim: n esqueça de descomentar!!!
PasswordAuthentication no
PermitRootLogin no
PubkeyAuthentication yes
* pronto, agora tente acessar novamente no gitbash ou pshell. Se entrar direto sem pedir login do servidor, deu certo

### pesquisar sobre bastion host e ssh agent forwarding