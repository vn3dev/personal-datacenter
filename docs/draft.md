

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