## Teste Técnico Cloud Engineer - Atividade 1

Criar a infraestrutura necessária para uma aplicação simples composta por frontend e backend. As instruções para build da aplicação utilizando Docker seguem no arquivo README.md enviado junto com o código no arquivo cloud-engineer-test-app.zip.

<!--ts-->
  * [Instruções](#instructions)
  * [Código Terraform](#build)
  * [Recursos Utilizados](#recursos)
  * [Destruindo a infraestrutura](#destruir)
<!--te-->

### Instruções<a name="instructions"></a>

- Cloud escolhida, AWS
- Código de como provisionar a infraestrutura usando o Terraform
- Explicação das seções usadas para essa configuração
- Como destruir a infraestrutura usando o Terraform

### Código Terraform<a name="build"></a>

A seguir o código Terraform para provisionar uma instância EC2 na AWS - região us.west.2

```bash
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.67.0"
    }
  }

    required_version = ">= 1.2.0"
}

provider "aws" {
  region = "us-west-2"
  access_key = "********"
  secret_key = "*********************"
}

resource "tls_private_key" "rsa_4096" {
    algorithm = "RSA"
    rsa_bits = 4096
}

variable "key_name" {}

resource "aws_key_pair" "key_pair" {
  key_name   = var.key_name
  public_key = tls_private_key.rsa_4096.public_key_openssh
}

resource "local_file" "private_key" {
    content = tls_private_key.rsa_4096.private_key_pem
    filename = var.key_name
}

resource "aws_instance" "atividade1" {
  ami               = "ami-02f9041628cc2f753"
  instance_type     = "t2.micro"
  availability_zone = "us-west-2a"
  key_name = aws_key_pair.key_pair.key_name

  tags = {
    Name = "Atividade1_Instance"
  }
}
```

########################################

### Recursos utilizados<a name="recursos"></a>

- Configuração do provedor
Utilizei o meu usuário da AWS - IAM, autenticado para fazer a configuração, provisionamento e destruição da instância na AWS. E para isso utilizei uma Access_key & Secret_Key.

- tls_private_key
Utilizei a tls_private_key para gerar uma chave privada segura e codificada no formato PEM. Este é um recurso destinado a inicializar facilmente ambientes de desenvolvimentos.

RSA_4096: É o tamanho da chave RSA gerada em bits.

- Variable
Para deixar mais dinâmico o exercício foi utilizado uma variável Key_name.

- Key_pair
A key_pair é utilizada para controlar o acesso na instância EC2.
A chave pública utilizada é o arquivo PEM criado.

- Private_key
Criar uma chave privada na máquina local.

- aws_instance
Provisiona uma instância EC2. AMI ID, copiei da minha conta AWS para selecionar o sistema operacional a ser criado, nesse case o SO escolhido foi Windows.

### Destruindo a infraestrutura<a name="destruir"></a>

Depois de ter executado o terraform plan, e terraform apply para provisionar a instância, na hora de deletar ela é necessário se utilizar do comando `terraform destroy`.
Executado o comando é só aguardar a destruição da instância EC2.

