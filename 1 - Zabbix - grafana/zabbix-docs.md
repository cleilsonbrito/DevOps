# Zabbix Monitoring Stack com Docker

Este repositório contém a configuração necessária para implantar uma stack completa de monitoramento usando Zabbix 7.0, PostgreSQL e Grafana utilizando Docker Compose, com foco em segurança e manutenibilidade.

## Arquitetura

A stack é composta pelos seguintes serviços:
- PostgreSQL 15 (Banco de dados)
- Zabbix Server 7.0
- Zabbix Web (interface com Nginx)
- Grafana (visualização de dados)

A infraestrutura utiliza redes Docker isoladas para melhor segurança:
- `frontend-net`: Para serviços com interface web
- `backend-net`: Para comunicação entre serviços internos
- `db-net`: Exclusiva para comunicação com o banco de dados

## Pré-requisitos

- Docker Engine 20.10 ou superior
- Docker Compose 2.0 ou superior
- Git (para clonar o repositório)
- Mínimo de 4GB de RAM
- 10GB de espaço em disco disponível

## Estrutura do Projeto

```
zabbix-monitoring/
├── docker-compose.yml
├── .env.example
├── .env
├── .gitignore
└── zbx_env/
    └── usr/lib/zabbix/
        ├── alertscripts/
        ├── externalscripts/
        ├── export/
        ├── modules/
        ├── enc/
        ├── ssh_keys/
        └── mibs/
```

## Instalação e Configuração

### 1. Clone o Repositório

```bash
git clone https://github.com/seu-usuario/zabbix-monitoring.git
cd zabbix-monitoring
```

### 2. Configure as Variáveis de Ambiente

```bash
# Copie o arquivo de exemplo
cp .env.example .env

# Edite o arquivo .env com suas configurações
nano .env
```

### 3. Crie a Estrutura de Diretórios

```bash
mkdir -p zbx_env/usr/lib/zabbix/alertscripts
mkdir -p zbx_env/usr/lib/zabbix/externalscripts
mkdir -p zbx_env/var/lib/zabbix/export
mkdir -p zbx_env/var/lib/zabbix/modules
mkdir -p zbx_env/var/lib/zabbix/enc
mkdir -p zbx_env/var/lib/zabbix/ssh_keys
mkdir -p zbx_env/var/lib/zabbix/mibs
```

### 4. Inicie os Serviços

```bash
# Inicie todos os serviços
docker-compose up -d

# Verifique o status
docker-compose ps
```

## Configuração das Variáveis de Ambiente

O arquivo `.env` suporta as seguintes configurações:

### Configurações do PostgreSQL
```env
POSTGRES_DB=zabbix
POSTGRES_USER=seu_usuario
POSTGRES_PASSWORD=sua_senha
POSTGRES_HOST=postgres
```

### Configurações do Zabbix
```env
ZABBIX_SERVER_HOST=zabbix-server
ZBX_TIMEOUT=30
PHP_TZ=America/Sao_Paulo
```

### Configurações do Grafana
```env
GRAFANA_ADMIN_PASSWORD=sua_senha_grafana
GRAFANA_PLUGINS=alexanderzobnin-zabbix-app
```

### Configurações de Rede
```env
FRONTEND_SUBNET=172.20.0.0/24
BACKEND_SUBNET=172.20.1.0/24
DB_SUBNET=172.20.2.0/24
```

## Portas e Acessos

### Zabbix Web Interface
- URL: http://localhost:8080
- Credenciais padrão:
  - Usuário: Admin
  - Senha: zabbix

### Grafana
- URL: http://localhost:3000
- Credenciais padrão:
  - Usuário: admin
  - Senha: [definida no .env]

### PostgreSQL
- Porta: 5432 (acessível apenas através da rede db-net)

## Manutenção

### Visualização de Logs

```bash
# Todos os serviços
docker-compose logs -f

# Serviço específico
docker-compose logs -f zabbix-server
```

### Backup e Restauração

#### Backup do PostgreSQL
```bash
docker-compose exec postgres pg_dump -U $POSTGRES_USER $POSTGRES_DB > backup_$(date +%Y%m%d).sql
```

#### Restauração do PostgreSQL
```bash
cat backup_YYYYMMDD.sql | docker-compose exec -T postgres psql -U $POSTGRES_USER $POSTGRES_DB
```

### Atualização dos Serviços

```bash
# Pare os serviços
docker-compose down

# Atualize as imagens
docker-compose pull

# Reinicie os serviços
docker-compose up -d
```

## Segurança

Esta configuração implementa várias medidas de segurança:

- Segregação de redes para isolamento de serviços
- Variáveis de ambiente para dados sensíveis
- Healthchecks para garantir disponibilidade dos serviços
- Volumes persistentes para dados importantes
- Controle de acesso entre serviços

## Resolução de Problemas

### Problemas Comuns

1. **Serviços não iniciam:**
   ```bash
   # Verifique os logs
   docker-compose logs
   ```

2. **Erro de permissão nos diretórios:**
   ```bash
   chmod -R 755 zbx_env/
   ```

3. **Conexão com banco de dados falha:**
   - Verifique as variáveis de ambiente no arquivo .env
   - Confirme se o serviço postgres está rodando
   - Verifique os logs do PostgreSQL

### Verificação de Status

```bash
# Status dos containers
docker-compose ps

# Uso de recursos
docker stats
```

## Contribuição

1. Faça um Fork do projeto
2. Crie uma Branch para sua Feature (`git checkout -b feature/AmazingFeature`)
3. Commit suas mudanças (`git commit -m 'Add some AmazingFeature'`)
4. Push para a Branch (`git push origin feature/AmazingFeature`)
5. Abra um Pull Request

## Licença

Este projeto está sob a licença MIT. Veja o arquivo `LICENSE` para mais detalhes.