# Configuração Manual — AWS Console

Este diretório contém os vídeos de cada etapa do provisionamento manual
da infraestrutura realizado via console web da AWS.

## Pré-requisitos

- Conta ativa na AWS
- Usuário IAM com as policies:
  - `AmazonEC2FullAccess`
  - `AmazonRDSFullAccess`
- Acesso ao console: https://console.aws.amazon.com

## Infraestrutura provisionada

| Etapa | Componente | Parâmetros principais |
|---|---|---|
| 1 | VPC | CIDR 10.0.0.0/16, DNS habilitado |
| 2 | Subnets | Pública 10.0.1.0/24 (sa-east-1a), Privada 10.0.2.0/24 (sa-east-1a), Auxiliar 10.0.3.0/24 (sa-east-1b) |
| 3 | Internet Gateway | Criado e anexado à VPC |
| 4 | Route Table | Rota 0.0.0.0/0 → IGW, associada à subnet pública |
| 5 | Security Group EC2 | Inbound: portas 22 e 80; Outbound: irrestrito |
| 6 | Instância EC2 | t3.micro, Ubuntu 22.04, subnet pública |
| 7 | Security Group RDS | Inbound: porta 3306, origem SG-EC2 |
| 8 | RDS MySQL | db.t3.micro, MySQL 8.0, subnet privada, acesso público desabilitado |

## Passo a passo

**Etapa 1 — VPC**
- VPC → Your VPCs → Create VPC
- Name: `minha-vpc` | CIDR: `10.0.0.0/16`
- Habilitar DNS support e DNS hostnames

**Etapa 2 — Subnets**
- VPC → Subnets → Create Subnet → selecione `minha-vpc`
- Subnet pública: `subnet-publica` | `sa-east-1a` | `10.0.1.0/24`
- Habilitar Auto-assign public IPv4 na subnet pública
- Subnet privada: `subnet-privada` | `sa-east-1a` | `10.0.2.0/24`
- Subnet auxiliar: `subnet-privada-b` | `sa-east-1b` | `10.0.3.0/24`

**Etapa 3 — Internet Gateway**
- VPC → Internet Gateways → Create → Name: `meu-igw`
- Actions → Attach to VPC → `minha-vpc`

**Etapa 4 — Route Table**
- VPC → Route Tables → Create → Name: `rt-publica` | VPC: `minha-vpc`
- Routes → Add route: `0.0.0.0/0` → `meu-igw`
- Subnet associations → associar `subnet-publica`

**Etapa 5 — Security Group EC2**
- EC2 → Security Groups → Create → Name: `SG-EC2` | VPC: `minha-vpc`
- Inbound: SSH porta 22 e HTTP porta 80 de `0.0.0.0/0`
- Outbound: All traffic (padrão)

**Etapa 6 — Instância EC2**
- EC2 → Launch instances → Name: `minha-ec2`
- AMI: Ubuntu Server 22.04 LTS | Type: `t3.micro`
- VPC: `minha-vpc` | Subnet: `subnet-publica` | IP público: habilitado
- Security group: `SG-EC2`

**Etapa 7 — Security Group RDS**
- EC2 → Security Groups → Create → Name: `SG-RDS` | VPC: `minha-vpc`
- Inbound: MySQL porta 3306 | Source: `SG-EC2` (não CIDR)

**Etapa 8 — RDS MySQL**
- RDS → Subnet groups → Create → Name: `meu-db-subnet-group`
- VPC: `minha-vpc` | Subnets: `subnet-privada` e `subnet-privada-b`
- RDS → Create database → MySQL 8.0 | Template: Dev/Test
- Instance: `db.t3.micro` | VPC: `minha-vpc` | Public access: No
- Security group: `SG-RDS`

## Vídeos por etapa

- [Etapa 1 — VPC](https://youtu.be/VsiVGryorT0)
- [Etapa 2 — Subnets](https://youtu.be/tLd0SfpLiEk)
- [Etapa 3 — Internet Gateway](https://youtu.be/lYP_vRJ1MSM)
- [Etapa 4 — Route Table](https://youtu.be/WsvnNE6eqJ0)
- [Etapa 5.1 — Security Group EC2 (tentativa 1)](https://youtu.be/MLmWwtP9M7w)
- [Etapa 5.2 — Security Group EC2 (tentativa 2)](https://youtu.be/XgdoSKC2Vfs)
- [Etapa 6 — Instância EC2](https://youtu.be/_QhpF1-qvIA)
- [Etapa 7 — Security Group RDS](https://youtu.be/KeIgXTmuCPw)
- [Etapa 8.1 — Criar Subnet Group RDS](https://youtu.be/DmcR6Py9iS0)
- [Etapa 8.2 — Criar Banco RDS MySQL](https://youtu.be/uwAOwj7NTkE)
