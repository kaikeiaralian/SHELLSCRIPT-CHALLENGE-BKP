#!/bin/bash
#declarando variaveis
ORIGCENTOS="/etc/httpd/"
ORIGUBUNTU="/etc/apache2/"
DIRSITES="/var/www/"
DIRDEST="/backups/"
DATA=`date +%d-%m_%H%M`
BKP_APACHE_CONF=conf-apache_$DATA.tgz
BKP_APACHE_WWW=site-apache_$DATA.tgz
COMANDO="$1"
CRONJOB="0 5 * * * /backups/./backup.sh"

if [[ -z "$1" ]];then
	echo "Digite ./backup.sh backup para fazer backup e ./backup.sh restore para restaurar o ultimo backup. Utilize ./backup.sh crontab para adicionar script no contrab"
fi

#Comando de backup e log
if [[ $COMANDO == backup ]]; then
	if [[ -n "$(uname -a | grep Debian)" ]]; then
		echo "Iniciando backup em $DATA" >> $DIRDEST/backup.txt
		tar -czpf ${DIRDEST}${BKP_APACHE_CONF} ${ORIGUBUNTU}>>$DIRDEST/backup.txt
		tar -czpf ${DIRDEST}${BKP_APACHE_WWW} ${DIRSITES}>>$DIRDEST/backup.txt
	elif [[-n "$(cat /etc/os-release | grep rhell)" ]]; then
		echo "Iniciado backup em $DATA" >> $DIRDEST/backup.txt
		tar -czpf ${DIRDEST}${BKP_APACHE_CONF} ${ORIGCENTOS}>>$DIRDEST/backup.txt
		tar -czpf ${DIRDEST}${BKP_APACHE_WWW} ${DIRSITES}>>$DIRDEST/backup.txt
	else
		exit 1
	fi
fi 

#Agendamento de backup
if [[ $COMANDO == crontab ]]; then
	cp backup.sh /backups/
	chmod +x /backups/backup.sh
	(crontab -u root -l; echo"CRONJOB") | crontab -u root -
fi
