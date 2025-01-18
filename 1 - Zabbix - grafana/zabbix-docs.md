# Instalação do Zabbix 7.0 com PostgreSQL e Grafana usando Docker Compose

Este guia fornece instruções detalhadas para configurar um ambiente de monitoramento completo usando Zabbix 7.0, PostgreSQL e Grafana utilizando Docker Compose.

## Pré-requisitos

- Docker instalado (versão 20.10 ou superior)
- Docker Compose instalado (versão 2.0 ou superior)
- Git (opcional, para clonar o repositório)
- 4GB de RAM mínimo recomendado
- 10GB de espaço em disco disponível

## Estrutura do Projeto

```
zabbix-docker/
├── docker-compose.yml
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

## Passos para Instalação

### 1. Preparação do Ambiente

Primeiro, crie um novo diretório para o projeto e navegue até ele:

```bash
mkdir zabbix-docker
cd zabbix-docker
```

### 2. Criação da Estrutura de Diretórios

Execute os seguintes comandos para criar a estrutura necessária:

```bash
mkdir -p zbx_env/usr/lib/zabbix/alertscripts
mkdir -p zbx_env/usr/lib/zabbix/externalscripts
mkdir -p zbx_env/var/lib/zabbix/export
mkdir -p zbx_env/var/lib/zabbix/modules
mkdir -p zbx_env/var/lib/zabbix/enc
mkdir -p zbx_env/var/lib/zabbix/ssh_keys
mkdir -p zbx_env/var/lib/zabbix/mibs
```

### 3. Configuração do Docker Compose

Crie um arquivo `docker-compose.yml` com o seguinte conteúdo:

```yaml
[Conteúdo do docker-compose.yml fornecido anteriormente]
```

### 4. Inicialização dos Serviços

Execute o seguinte comando para iniciar todos os serviços:

```bash
docker-compose up -d
```

### 5. Verificação dos Serviços

Verifique se todos os containers estão rodando corretamente:

```bash
docker-compose ps
```

## Acesso aos Serviços

### Zabbix Web Interface
- URL: http://localhost:8080
- Credenciais padrão:
  - Usuário: Admin
  - Senha: zabbix

### Grafana
- URL: http://localhost:3000
- Credenciais padrão:
  - Usuário: admin
  - Senha: admin (será solicitada alteração no primeiro acesso)

## Configuração do Grafana com Zabbix

1. Acesse o Grafana (http://localhost:3000)
2. Faça login com as credenciais padrão
3. Navegue até Configuration > Plugins
4. Localize o plugin "Zabbix" (já instalado automaticamente)
5. Configure uma nova fonte de dados:
   - Tipo: Zabbix
   - URL: http://zabbix-web:8080/api_jsonrpc.php
   - Username: Admin
   - Password: zabbix
   - Direct DB Connection: Desabilitado

## Comandos Úteis

### Visualizar logs dos serviços
```bash
# Todos os serviços
docker-compose logs -f

# Serviço específico
docker-compose logs -f zabbix-server
```

### Reiniciar serviços
```bash
docker-compose restart
```

### Parar todos os serviços
```bash
docker-compose down
```

### Parar serviços e remover volumes
```bash
docker-compose down -v
```

## Backup e Restauração

### Backup do PostgreSQL
```bash
docker-compose exec postgres pg_dump -U zabbix zabbix > backup_$(date +%Y%m%d).sql
```

### Restauração do PostgreSQL
```bash
cat backup_YYYYMMDD.sql | docker-compose exec -T postgres psql -U zabbix zabbix
```

## Resolução de Problemas

1. Se o Zabbix Server não iniciar, verifique os logs:
```bash
docker-compose logs zabbix-server
```

2. Para problemas de permissão nos diretórios:
```bash
chmod -R 755 zbx_env/
```

3. Se o Grafana não conseguir conectar ao Zabbix:
   - Verifique se o plugin do Zabbix está instalado corretamente
   - Confirme se a URL da API está correta
   - Verifique as credenciais de acesso

## Atualização

Para atualizar as imagens para novas versões:

```bash
docker-compose down
docker-compose pull
docker-compose up -d
```

## Contribuição

Sinta-se à vontade para contribuir com este projeto através de Pull Requests ou reportando issues.

## Licença

Este projeto está sob a licença MIT.
