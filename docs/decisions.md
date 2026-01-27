[27/01/2026]

## escopo da v1 
* rede interna + NAT
para isolamento dos servidores sem proibir o acesso a internet. apenas 2 interfaces por enquanto, mas pretendo expandir no futuro.
* SSH
método padrão de acesso remoto, escolhi pela segurança e facilidade de automatizar processos
* nginx e postresql
optei nginx ao apache por preferência pessoal, ja tive contato com nginx então me sinto mais confortável usando. PostreSQL pelo mesmo motivo. Os 2 são estáveis, confiáveis e altamente documentados
* backups
já implementado no inicio do projeto. Como ainda não tenho dominio do ambiente e pretendo fazer testes em cada etapa, decidi tratar
backups com alta prioridade
* documentação e runbooks no github
para registrar meus aprendizados e minha evolução. Pretendo fazer etapas detalhadas no runbooks mostrando o passo a passo do projeto, registrando erros que eu encontrar e como eu consegui resolver