# Projeto Compass UOL

Este guia tem o objetivo de descrever como configurar uma instância EC2 no Amazon Web Services (AWS) com Amazon Linux 2 e associar um Elastic IP, além de liberar portas de comunicação para acesso público.

## 1. Gerar uma chave pública para acesso ao ambiente

1. **Gerar um par de chaves (SSH Key Pair):**
    - No terminal (Linux/macOS) ou PowerShell (Windows), execute o comando abaixo para gerar um par de chaves:
      
    - ssh-keygen -t rsa -b 4096 -f Chave_02
      
    - Isso criará dois arquivos:
      - `Chave 01` (chave privada).
      - `Chave 02` (chave pública).
    - O arquivo `Chave 02` será usado para acessar a instância EC2, e o arquivo `Chave 01` deve ser mantido seguro.

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
    - Carregue a chave pública gerada anteriormente (`Chave 02`).
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
   | 111    | TCP       | NFS   | 0.0.0.0/0    |
   | 443    | TCP       | HTTPS | 0.0.0.0/0    |
   | 2049   | UDP       | NFS   | 0.0.0.0/0    |
   | 2049   | TCP       | NFS   | 0.0.0.0/0    |
   | 80     | TCP       | HTTP  | 0.0.0.0/0    |
     
2. **Salvar as configurações:**
    - Após configurar as regras, clique em `Save`.

Agora, sua instância EC2 está pronta, com o Elastic IP associado e as portas de comunicação liberadas para acesso público.

## INSTALAÇÃO DO NFS E DO APACHE NA AWS

1. **Conecte-se à Instância EC2 via SSH** e atualize o sistema:

    - Para Amazon Linux 2 ou RHEL:
      ```bash
      sudo yum update -y
      ```

2. **Instale o NFS (Network File System):**

    - Para Amazon Linux 2 ou RHEL:
      ```bash
      sudo yum install nfs-utils -y
      ```

3. **Configure o NFS:**

    - Crie o diretório a ser compartilhado:
      ```bash
      sudo mkdir -p /home/Gabriel/server-nfs
      ```

    - Configure as permissões do diretório:
      ```bash
      sudo chown -R ec2-user:ec2-user /home/jefferson/server-nfs/
      sudo chmod 755 /home/Gabriel/server-nfs/
      ```

    - Edite o arquivo de configuração `/etc/exports`:
      ```bash
      sudo nano /etc/exports
      ```

      - Para permitir o acesso a todos os clientes:
        ```
        /home/Gabriel/server-nfs/ *(rw,sync,no_subtree_check)
        ```

      - Para permitir o acesso apenas a IPs específicos:
        ```
        /home/Gabriel/server-nfs/ 192.168.1.0/24(rw,sync,no_subtree_check)
        ```

    - Aplique as mudanças:
      ```bash
      sudo exportfs -a
      ```

    - Inicie e habilite o serviço NFS:

      - Para Amazon Linux 2 ou RHEL:
        ```bash
        sudo systemctl start nfs-server
        sudo systemctl enable nfs-server
        ```


4. **Verifique o status do NFS:**

    - Para Amazon Linux 2 ou RHEL:
      ```bash
      sudo systemctl status nfs-server
      ```

5. **Verifique as regras de segurança da AWS para a porta 2049 (NFS).**

6. **Instale o Apache:**

    - Para Amazon Linux 2 ou RHEL:
      ```bash
      sudo yum install httpd -y
      sudo systemctl start httpd
      sudo systemctl enable httpd
      ```

7. **Verifique o status do Apache:**

    - Para Amazon Linux 2 ou RHEL:
      ```bash
      sudo systemctl status httpd
      ```

    - Para Ubuntu ou Debian:
      ```bash
      sudo systemctl status apache2
      ```

8. **Verifique as regras de segurança da AWS para as portas 80 (HTTP) e 443 (HTTPS).**

9. **Abra um navegador e digite o IP público da instância EC2 para ver a página padrão do Apache.**

## MONITORAMENTO DOS SERVIÇOS
