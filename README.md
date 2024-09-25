# Projeto Compass UOL - Gabriel Marques Santos

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

     Para Amazon Linux 2 ou RHEL:
      ```bash
      sudo yum update -y
      ```
2. **Instale o Apache:**

      ```bash
      sudo yum install httpd -y
      ```
      id para ver o Apache Ativo(test): 98.82.249.130
3. ** Iniciar o Apache:**

      ```bash
      sudo systemctl start httpd
      sudo systemctl enable httpd
      ```
    - Verificar Status do Apache:
      ```bash
      sudo service status httpd
      ```
 4. ** Criar diretório para armazenar logs no NFS **
      ```bash
       nfs_client_share
       nano checkservice
      /mnt/nfs_client_share *(rw,sync,no_subtree_check,no_root_squash)
      ```
4. **Criar um script para verificar o status do Apache**
    ```bash
      #!/bin/bash

   Definir variáveis
      SERVICO="httpd"
      DATA_HORA=$(date "+%Y-%m-%d %H:%M:%S")
      NOME_SERVICO="Apache"
      NFS_DIR="/mnt/nfs_client_share/<gabriel>"
      ARQUIVO_ONLINE="$NFS_DIR/apache_online.log"
      ARQUIVO_OFFLINE="$NFS_DIR/apache_offline.log"

    Verificar se o serviço está online
      if systemctl is-active --quiet $SERVICO; then
      MENSAGEM="$DATA_HORA - $NOME_SERVICO - Status: ONLINE - Tudo está funcionando corretamente."
      echo $MENSAGEM >> $ARQUIVO_ONLINE
   else
      MENSAGEM="$DATA_HORA - $NOME_SERVICO - Status: OFFLINE - O serviço está inativo!"
      echo $MENSAGEM >> $ARQUIVO_OFFLINE
   fi
    ```
**Torne o script executável e configure o cron:
```bash
   chmod +x checkserveice
   sudo nano /etc/crontab
 ```
Adicione a linha:
```bash
*/5 * * * * root /home/gabriel/checkservice
```

  #Documentação Final
Para finalizar, assegure-se de que toda a configuração e os scripts estejam devidamente documentados e armazenados no repositório Git. Isso facilita a manutenção futura e permite que outros membros da equipe entendam o processo.

Recomendações para a Documentação:
README.md:

Descrição do Projeto: Breve introdução sobre o objetivo e funcionalidades.
Pré-requisitos: Listagem de softwares e ferramentas necessárias.
Passo a Passo de Configuração: Resumo das etapas descritas acima.
Como Executar os Scripts: Instruções para rodar o script de monitoramento manualmente e via cron.
Estrutura de Diretórios: Descrição dos principais diretórios e arquivos.
Contribuição: Diretrizes para colaboradores.
Licença: Informações sobre a licença do projeto.

Versionamento:

Utilize commits claros e descritivos para cada alteração significativa no projeto.

Com isso, conclui a configuração da instância EC2 com Amazon Linux 2, associei a Elastic IP, liberei as portas de comunicação necessárias, instalei e configurei o NFS e o Apache, criei um script de monitoramento, automatizei sua execução com crontab, versionou o projeto com Git e documentou todo o processo. Este ambiente está pronto para uso e manutenção contínua.

    

