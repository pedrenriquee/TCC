# Automação com Terraform

O arquivo `tcc-etapas.zip` contém os arquivos de configuração Terraform para
provisionamento da infraestrutura na AWS, organizados em 8 etapas
independentes para permitir a execução e cronometragem individual de cada componente.

## Download e extração dos arquivos

1. Baixe o arquivo `tcc-etapas.zip` disponível no repositório.
2. Após o download, clique com o botão direito sobre o arquivo e selecione **Extrair Tudo**.
3. Escolha o local de extração, por exemplo, `C:\`.
4. Abra a pasta extraída `tcc-etapas` e verifique se ela contém as oito pastas das etapas de provisionamento.

> Não execute os comandos diretamente dentro do arquivo ZIP. Extraia todos os arquivos antes de iniciar a configuração.

## Pré-requisitos

- [Terraform](https://developer.hashicorp.com/terraform/install) versão 1.3 ou superior
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html)
- Conta AWS com usuário IAM com as policies:
  - `AmazonEC2FullAccess`
  - `AmazonRDSFullAccess`

## Configuração inicial

**1. Abra o CMD**
Aperte a tecla Windows, digite `cmd` e pressione Enter.

**2. Configure suas credenciais AWS:**
```
aws configure
```
Preencha: Access Key ID, Secret Access Key, região `sa-east-1`, formato `json`.

**3. Ajuste a senha do RDS:**
Abra `etapa8-rds-mysql/main.tf` no bloco de notas e troque:
```
password = "SuaSenhaAqui123!"
```
pela sua senha (mínimo 8 caracteres). Salve o arquivo.

## Executando as etapas

No CMD, navegue até a pasta extraída `tcc-etapas`. Caso tenha escolhido outro local de extração, ajuste o caminho abaixo:
```
cd /d C:\tcc-etapas
```

Execute cada etapa em ordem. Para cada uma, entre na pasta, rode os três comandos e volte:

**Etapa 1 — VPC**
```
cd etapa1-vpc
terraform init
terraform plan
terraform apply
cd ..
```

**Etapa 2 — Subnets**
```
cd etapa2-subnets
terraform init
terraform plan
terraform apply
cd ..
```

**Etapa 3 — Internet Gateway**
```
cd etapa3-internet-gateway
terraform init
terraform plan
terraform apply
cd ..
```

**Etapa 4 — Route Table**
```
cd etapa4-route-table
terraform init
terraform plan
terraform apply
cd ..
```

**Etapa 5 — Security Group EC2**
```
cd etapa5-sg-ec2
terraform init
terraform plan
terraform apply
cd ..
```

**Etapa 6 — Instância EC2**
```
cd etapa6-ec2
terraform init
terraform plan
terraform apply
cd ..
```

**Etapa 7 — Security Group RDS**
```
cd etapa7-sg-rds
terraform init
terraform plan
terraform apply
cd ..
```

**Etapa 8 — RDS MySQL**
```
cd etapa8-rds-mysql
terraform init
terraform plan
terraform apply
```

> Digite `yes` quando pedir confirmação em cada etapa. A etapa 8 demora aproximadamente 5 minutos — é o RDS sendo provisionado. Não feche o CMD.

Ao final da etapa 8 o terminal exibe o IP público da EC2 e o endpoint do RDS.

## Destruindo a infraestrutura

Execute na ordem inversa, um por um:

```
cd etapa8-rds-mysql
terraform destroy
cd ..

cd etapa7-sg-rds
terraform destroy
cd ..

cd etapa6-ec2
terraform destroy
cd ..

cd etapa5-sg-ec2
terraform destroy
cd ..

cd etapa4-route-table
terraform destroy
cd ..

cd etapa3-internet-gateway
terraform destroy
cd ..

cd etapa2-subnets
terraform destroy
cd ..

cd etapa1-vpc
terraform destroy
```

> Digite `yes` em cada um. Aguarde o RDS ser completamente removido antes de continuar o destroy.

## Pontos de atenção

- Se aparecer erro de permissão IAM, adicione as policies listadas nos pré-requisitos
- A availability zone deve sempre ser `sa-east-1a` ou `sa-east-1b`, nunca só `sa-east-1`
- O RDS pode não estar disponível no Free tier — use o template Dev/Test se necessário
- A senha do RDS deve ter no mínimo 8 caracteres
