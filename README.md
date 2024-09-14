# Projeto Compass UOL

Este guia tem o objetivo de descrever como configurar uma instância EC2 no Amazon Web Services (AWS) com Amazon Linux 2 e associar um Elastic IP, além de liberar portas de comunicação para acesso público.

## 1. Gerar uma chave pública para acesso ao ambiente

1. **Gerar um par de chaves (SSH Key Pair):**
    - No terminal (Linux/macOS) ou PowerShell (Windows), execute o comando abaixo para gerar um par de chaves:
      ```bash
      ssh-keygen -t rsa -b 4096 -f aws_key
      ```
    - Isso criará dois arquivos:
      - `aws_key` (chave privada).
      - `aws_key.pub` (chave pública).
    - O arquivo `aws_key.pub` será usado para acessar a instância EC2, e o arquivo `aws_key` deve ser mantido seguro.

## 2. Criar uma instância EC2 com Amazon Linux 2

1. **Acessar o Console AWS:**
    - Faça login na sua conta AWS.

2. **Navegar até o serviço EC2:**
    - No menu de serviços, selecione EC2.

3. **Lançar uma nova instância:**
    - Clique em `Launch Instance`.
    - Escolha `Amazon Linux 2` como o sistema operacional.
    - Na seleção de tipo de instância, escolha `t3.small`.

4. **Configuração de armazenamento:**
    - Altere o armazenamento para `16 GB SSD`.

5. **Configuração da chave SSH:**
    - Na seção `Key Pair`, selecione `Create a new key pair`.
    - Carregue a chave pública gerada anteriormente (`aws_key.pub`).
    - Alternativamente, crie uma nova chave diretamente no AWS e use a chave privada.

6. **Configuração de rede:**
    - Crie um novo grupo de segurança ou selecione um existente.
    - Certifique-se de liberar as portas descritas abaixo (ver passo 4).

7. **Revisão e lançamento:**
    - Revise as configurações e clique em `Launch Instance`.

## 3. Gerar um Elastic IP e anexar à instância EC2

1. **Acessar o serviço EC2:**
    - No console do EC2, vá para `Elastic IPs` no menu de rede e segurança.

2. **Alocar um novo Elastic IP:**
    - Clique em `Allocate Elastic IP Address`.
    - Escolha `Amazon pool of IPv4 addresses` e clique em `Allocate`.

3. **Anexar o Elastic IP à instância:**
    - Após alocar o Elastic IP, selecione-o e clique em `Associate Elastic IP Address`.
    - Escolha a instância EC2 que você acabou de criar e associe o Elastic IP a ela.

## 4. Liberar as portas de comunicação para acesso público

1. **Configurar o Security Group:**
    - No menu de `Security Groups` no console EC2, edite o grupo de segurança associado à instância.
    - Adicione regras de entrada (inbound rules) para liberar as seguintes portas:

    | Porta  | Protocolo | Tipo  | Origem       |
    |--------|-----------|-------|--------------|
    | 22     | TCP       | SSH   | 0.0.0.0/0    |
    | 111    | UDP       | NFS   | 0.0.0.0/0    |
    | 443    | TCP       | HTTPS | 0.0.0.0/0    |
    | 2049   | UDP       | NFS   | 0.0.0.0/0    |
    | 80     | TCP       | HTTP  | 0.0.0.0/0    |

2. **Salvar as configurações:**
    - Após configurar as regras, clique em `Save`.

Agora, sua instância EC2 está pronta, com o Elastic IP associado e as portas de comunicação liberadas para acesso público.
