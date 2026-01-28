[27/01/2026]

## prototipo arquitetura inicial
host: Windows 10
hypervisor: VirtualBox
tooling: WSL2
3 vms iniciais
- srv-core: administração e acesso
- srv-web: serviço web
- srv-data: banco de dados

root via ssh desabilitado
ips fixos na LAN

srv-core (ou bastion / base)
acesso administrativo
ponto inicial de gestão
SSH, documentação, automação futura

srv-app
servidor de aplicação
nginx
serviços que podem escalar
IP pode ser dinâmico

srv-db
banco de dados
PostgreSQL
IP fixo
acesso restrito