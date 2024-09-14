PROJETO COMPASS UOL
1. Gerar uma chave pública para acesso ao ambiente
1.	Gerar um par de chaves (SSH Key Pair):
o	No terminal (Linux/macOS) ou PowerShell (Windows), execute o seguinte comando para gerar um par de chaves:
o	Escolha um nome para o arquivo da chave (neste exemplo, "aws_key"). Isso criará dois arquivos:
	aws_key (chave privada).
	aws_key.pub (chave pública).
o	O arquivo aws_key.pub será usado para acessar a instância EC2, e aws_key deve ser mantido seguro.

2. Criar uma instância EC2 com Amazon Linux 2
1.	Acessar o Console AWS:
o	Faça login na sua conta AWS.
2.	Navegar até o serviço EC2:
o	No menu de serviços, selecione EC2.
3.	Lançar uma nova instância:
o	Clique em Launch Instance.
o	Escolha Amazon Linux 2 como o sistema operacional.
o	Na seleção de tipo de instância, escolha t3.small.
4.	Configuração de armazenamento:
o	Altere o armazenamento para 16 GB SSD.
5.	Configuração da chave SSH:
o	Na seção Key Pair, selecione Create a new key pair.
o	Carregue a chave pública gerada anteriormente (aws_key.pub).
o	Alternativamente, crie uma nova chave diretamente no AWS, baixe e use a chave privada.
6.	Configuração de rede:
o	Crie um novo grupo de segurança ou selecione um existente.
o	Certifique-se de liberar as portas que você mencionou (ver passo 5 abaixo).
7.	Revisão e lançamento:
o	Revise as configurações e clique em Launch Instance.
3. Gerar um Elastic IP e anexar à instância EC2
1.	Acessar o serviço EC2:
o	No console do EC2, vá para Elastic IPs no menu de rede e segurança.
2.	Alocar um novo Elastic IP:
o	Clique em Allocate Elastic IP Address.
o	Escolha Amazon pool of IPv4 addresses e clique em Allocate.
3.	Anexar o Elastic IP à instância:
o	Após alocar o Elastic IP, selecione-o e clique em Associate Elastic IP Address.
o	Escolha a instância EC2 que você acabou de criar e associe o Elastic IP a ela.
4. Liberar as portas de comunicação para acesso público
1.	Configurar o Security Group:
o	No menu de Security Groups no console EC2, edite o grupo de segurança associado à instância.
o	Adicione regras de entrada (inbound rules) para liberar as seguintes portas:
	Porta 22 (SSH):
	Protocolo: TCP
	Porta: 22
	Origem: 0.0.0.0/0 (para permitir acesso de qualquer IP)
	Porta 111 (UDP):
	Protocolo: UDP
	Porta: 111
	Origem: 0.0.0.0/0
	Porta 443 (HTTPS):
	Protocolo: TCP
	Porta: 443
	Origem: 0.0.0.0/0
	Porta 2049 (UDP):
	Protocolo: UDP
	Porta: 2049
	Origem: 0.0.0.0/0
	Porta 2049 (NFS):
	Protocolo: TCP
	Porta: 443
	Origem: 0.0.0.0/0
	Porta 80 (NFS):
	Protocolo: TCP
	Porta: 443
	Origem: 0.0.0.0/0

2.	Salvar as configurações:
o	Após configurar as regras, clique em Save.
Agora, a instância EC2 está pronta com o Elastic IP associado e as portas de comunicação liberadas para acesso público. 
