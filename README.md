# FULLSTACK NANODEGREE - Configuração do Servidor Linux
## Descrição do Projeto
Você pegará uma instalação de referência de um servidor Linux e a preparará para 
hospedar suas aplicações web. Também irá oferecer segurança a seu servidor contra um 
número de vetores de ataque, instalar e configurar um servidor de banco de dados e 
implantar uma de suas aplicações web existentes a ele.

## Desenvolvimento do Projeto

### Processo de criação do usuário "grader"
- Com o usuário root foi executado o seguinte comando:
    - sudo adduser grader
-  Foi também adicionado as permissões de sudo para o usuário "grader" 
realizando os seguintes passos:
    - Foi executado o comando abaixo para acessar a pasta referente as permissões de usuários:
        > sudo ls /etc/sudoers.d
    - Criado o arquivo com nome do usuário
        > sudo nano /etc/sudoers.d/grader
    - Editado o arquivo adicionando as seguintes informações:
        > grader ALL=(ALL) NOPASSWD:ALL

### Processo de autenticação utilizando RSA
- Realizado a criação da par de chaves para acesso ao servidor:
    > Executado o comando ssh-keygen para gerar a chave publica e privada
- Realizada a instalação da chave publica no servidor 
    - Criado pasta .ssh
        > mkdir .ssh
    - Criado arquivo que armazena as chaves publicas
        > touch .ssh/authorized_keys
    - Inserido chave publica dentro do arquivo authorized_keys
    - Executado comando de permissionamento do diretório .ssh
        > chmod 700 .ssh
    - Executado comando de permissionamento do arquivo authorized_keys
        > chmod 644 .ssh/authorized_keys    
        
### Instalação de programas necessários para rodar aplicação Flask

- Instalado apache2
	> sudo apt-get install apache2
- Instalado mod_wsgi
	> sudo apt-get install libapache2-mod-wsgi python-dev
	- Habilitado mod_wsgi
    	> sudo a2enmod wsgi
- Instalado PostgreSQL
	> sudo apt-get install postgresql
	
- Instalado o pip
    > sudo apt-get install python-pip 

### Clonagem de Aplicação FLASK

- Dentro da pasta /var/www foi criada outra pasta utilizando o seguinte comando
    > sudo mkdir FlaskApp

- Dentro da pasta /var/www/FlaskApp foi utilizado  o seguinte comando
    > git clone https://github.com/fxsobr/catalog

#### Instalação das dependencias do projeto catalogo que foi clonado do github

- Dentro da pasta /var/www/FlaskApp/catalog foi instalado o virtual env
    > sudo pip install virtualenv
    - Foi criado um ambiente virtual com o nome venv
        > sudo virtualenv venv
    - Ativado virtual env utilizando o seguinte comando
        > source venv/bin/activate
    - Dentro do virtual env foi instalado as dependencias do projeto de catalogo
        > sudo pip install Flask e outras depedencias necessárias para rodar o projeto

#### Criação de um novo VirtualHost
- Executado o comando abaixo, para a criação de um novo arquivo .conf
	> sudo nano /etc/apache2/sites-available/FlaskApp.conf
- Adicionado arquivo de configuração do arquivo .conf

    ![ScreenShot](https://github.com/fxsobr/linux-fullstack/blob/master/imagens/wsgi.png?raw=true)

- Habilitado o novo virtual host criado
    > sudo a2ensite FlaskApp

#### Criação do arquivo .wsgi
- Acessado a pasta /var/www/FlaskApp e criado o arquivo flaskapp.wsgi
	> sudo nano flaskapp.wsgi 
- Editado o arquivo flaskapp.wsgi

    ![ScreenShot](https://github.com/fxsobr/linux-fullstack/blob/master/imagens/flaskapp.png?raw=true)

- Reiniciado apache

### Criação do banco catalogo no postgres
- Adicionado usuário catalogo
    > sudo adduser catalog
- Acessando usuário postgres
    > sudo su - postgress
- Conectado ao sistema do postgres
    > psql
- Criado usuário
    > CREATE USER catalog WITH PASSWORD 'catalog';
- Adicionado permissão ao usuário catalog para criar tabelas no banco
    > ALTER USER catalog CREATEDB;
- Criado Banco de dados
    > CREATE DATABASE catalog WITH OWNER catalog;
- Revogado todos os acessos
    > REVOKE ALL ON SCHEMA public FROM public;
- Adicionado acesso somente a role catalog
    > GRANT ALL ON SCHEMA public TO catalog;

- Reiniciado PostgreSQL
    > sudo service postgresql restart

### Ajustes no Projeto Flask
- Alterado arquivos que continham conexão com o banco de dados utilizando o sqlite para o postgres
> engine = create_engine('postgresql://catalog:catalog@localhost/catalog')

- Rodado o arquivo database_setup.py para criação das classes dentro do postgres
- Rodado o arquivo database_insert.py para inserção de dados dentro do postgres
   
### Configuração SSH
- Acessado as configurações do ssh
    > sudo nano /etc/ssh/sshd_config
- Alterado porta 22 para 2200 no ssh
- Desabilitado login via usuário root
- Reiniciado serviço ssh
    > sudo service ssh restart

### Configurado Portas do UFW

- Bloqueado Portas de entrada
    > sudo ufw default deny incoming
- Liberado portas de saida
    > sudo ufw default allow outgoing
- Liberado porta 2200
    > sudo ufw allow 2200/tcp
- Liberado porta 80
    > sudo ufw allow www
- Liberado porta 173
    > sudo ufw allow ntp
- Habilitado o ufw
    > sudo ufw enable
- Verificado o status do ufw
    > sudo ufw status

### Conectando no servidor via SSH

- Utilizar o seguinte comando
    > ssh grader@104.248.232.241 -p 2200 -i caminho_da_chave_privada
- Informar senha contidada dentro da chave rsa

### Acessando banco de dados
- Para acessar o banco utilizar o seguinte comando
    > sudo su - catalog
- Depois informar o comando
    > psql
- Para listar as tabelas utilizar o comando
    > \dt
    
    ![ScreenShot](https://github.com/fxsobr/linux-fullstack/blob/master/imagens/tabelas.png?raw=true)
- Verificar conteúdo das tabelas
    > SELECT * FROM categoria;
    
    > SELECT * FROM produto;
    
    > SELECT * FROM usuario;
    
  ![ScreenShot](https://github.com/fxsobr/linux-fullstack/blob/master/imagens/conteudo_tabelas.png?raw=true)  
    
### Acessando o site da aplicação
- Para acessar o site clique
    [Aqui](http://104.248.232.241/)