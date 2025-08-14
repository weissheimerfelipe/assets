Sincronizando um Servidor Samba com o Google Drive
Este guia mostra como sincronizar uma pasta de um servidor de arquivos Samba com o Google Drive usando a ferramenta rclone no Linux. O método funciona tanto para backups quanto para espelhamento completo.
1. Instalar o Rclone
No terminal do servidor, execute:
sudo apt update
sudo apt install rclone -y
2. Configurar o Rclone
Inicie a configuração com:
rclone config

Passos:
1. Digite 'n' para criar uma nova configuração.
2. Dê um nome para o remote, por exemplo: gdrive
3. Escolha o tipo 'Google Drive'.
4. Autentique no navegador conforme instruções.
5. Caso estiver via ssh e nao funcionar a autenticação:
6. - crie um tunnel ssh no putty com a porta 53682
7. - abra o putty>vá para ssh>expanda>va para tunnel> source port=53682>destination= localhost:53682
8. - logue no dispositivo>re-faça a configuração e quando pedir se voce esta localmente na maquina, clique em sim, ai copie a url disponibilizada [ http://127.0.0.1:53682 url ]
5. Salve e saia.
3. Testar Conexão
Para verificar se está funcionando:
rclone ls gdrive:
Se listar arquivos, a conexão está correta.
4. Sincronizar a pasta Samba com o Google Drive
Supondo que a pasta Samba esteja em /srv/samba/compartilhado:

Para espelhar (apaga no Drive o que foi apagado localmente):
rclone sync /srv/samba/compartilhado gdrive:backup_samba --progress

Para apenas enviar (sem apagar no Drive):
rclone copy /srv/samba/compartilhado gdrive:backup_samba --progress
5. Automatizar com Cron
Edite o crontab com:
crontab -e

Para rodar todo dia às 2h da manhã:
0 2 * * * rclone sync /srv/samba/compartilhado gdrive:backup_samba --log-file=/var/log/rclone.log --log-level INFO
6. Sincronização em tempo real (opcional)
Para manter a sincronização ativa:
rclone sync /srv/samba/compartilhado gdrive:backup_samba --progress --daemon --transfers=8 --checkers=16

Ou configure um script com inotifywait para detectar alterações e sincronizar.
Dicas de Segurança
* Se o espaço do Google Drive for limitado, use parâmetros como '--max-size' ou '--exclude' para evitar enviar arquivos temporários.
* Sempre teste a sincronização com uma pasta de teste antes de aplicar na pasta principal.
