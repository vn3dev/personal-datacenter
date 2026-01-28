

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