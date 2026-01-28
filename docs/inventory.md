# inventario (v1)

## host
* SO: Windows 10 Pro
* CPU: i7-7700K
* RAM: 32 GB
* disco: SSD SATA
* tooling: WSL2
* hypervisor: virtualbox

## redes
* NAT: virtualbox NAT
* LAN: LAB_LAN-10.10.10.0/24

## planejamento VMs
| VM       | SO            | Função     | vCPU | RAM | Disco | NICs           | IP (LAN)      | Serviços (v1)             |
|----------|---------------|------------|-----:|----:|------:|----------------|---------------|---------------------------|
| srv-core | ubuntu server | Admin      | 2    | 2GB | 40GB  | NAT + LAB_LAN  | 10.10.10.10   | SSH                       |
| srv-web  | ubuntu server | Web        | 2    | 2GB | 40GB  | LAB_LAN        | 10.10.10.20   | SSH, Nginx (80/tcp)       |
| srv-data | ubuntu server | Database   | 2    | 4GB | 60GB  | LAB_LAN        | 10.10.10.30   | SSH, PostgreSQL (5432/tcp)|

## backups
- pasta no host: C:\homelab\backups\
- origem: srv-data (prioridade), srv-web (configs), srv-core (scripts/docs)
