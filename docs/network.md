documentar comms de rede do lab

[27/01/2026]

## topologia

interface dupla com NAT para updates e downloads e LAN interna para comunicação entre as VMS

LAN:
rede: 10.10.10.0/24
IPs fixos:
- srv-core: 10.10.10.10
- srv-web:  10.10.10.20
- srv-data: 10.10.10.30

fluxo:
host/wsl2 -> SSH -> VM (10.10.10.x)
srv-web -> postreSQL 5432 -> srv-data
VMs -> internet via NAT

portas v1:
22/tcp SSH: host -> VMs pela LAN
80/tcp SSH: host -> srv-web
5432/tcp PostreSQL: srv-web/srv-core -> srv-data APENAS LAN

### sem roteador/firewall dedicado na LAN nessa versão

implementações futuras:
pfSense
talvez DHCP + reserva na LAN
bastion host