# Ubuntu/Docker mit Apache Webserver

## Inhaltsverzeichnis

- Übersicht
- Beschreibung
- Installation
- Dockerfile
- Testfälle
- Quellenverzeichnis
## Übersicht
```
    +---------------------------------------------------------------+
    !                                                               !	
    !    +-------------------------+                                !
    !    ! Apache Web-Server       !                                !       
    !    ! Port: 80                !                                !       
    !    ! Volume: /var/www/html   !                                !       
    !    +-------------------------+                                !
    !                                                               !	
    ! Container                                                     !	
    +---------------------------------------------------------------+
    ! Container-Engine: Docker                                      !	
    +---------------------------------------------------------------+
    ! Gast OS: Ubuntu 16.04                                         !	
    +---------------------------------------------------------------+
    ! Hypervisor: VirtualBox                                        !	
    +---------------------------------------------------------------+
    ! Host-OS: Windows (Mac und Linux sollte auch funktionieren)    !	
    +---------------------------------------------------------------+
```

## Beschreibung
Ein Apache Webserver Container für Docker auf Ubuntu. Es wird die Ubuntu Version 14.04 verwendet. Der Apache Webserver startet automatisch, sobald der Container gestartet wird. Auf dem Container ist der Port 80 geöffnet. 
Das `index.html` File unter `root/web` kann ersetzt werden. 
## Installation

Wenn die Ubuntu Installation mit Docker vorhanden ist, kann das Projektverzeichnis auf diese Instanz kopiert werden. Dazu kann FTP, SCP, oder z.B. auch RSYNC verwendet werden. 
Mit folgendem Befehl in das Verzeichnis wechseln 
```
$ cd /apachecontainer
```
> **Hinweis:** Der Pfad muss eventuell noch angepasst werden

Mit folgendem Befehl den Container aus dem Dockerfile erstellen
```
$ docker build -t apache . 
```
Dann mit folgendem Befehl den soeben erstellten Container starten
```
$ docker run --rm -d -p 8080:80 -v `pwd`/web:/var/www/html --name apache apache
```
Nun sollte die Webseite mit der IP der VM unter Port 8080 aufrufbar sein. Falls dies nicht der Fall ist, liegt ein Fehler in der Netzwerkkonfiguration der VM vor. 
## Dockerfile
Hier werden die einzelnen Befehle im Dockerfile erklärt
```
FROM ubuntu:14.04
```
Beschreibt das Basisimage, welches die Grundlage für das neu erstellte Image bildet.
#
```
MAINTAINER Marcel mc-b Bernet <marcel.bernet@ch-open.ch>
```
Mit diesem Befehl wird der Autor des Images gesetzt. Da ich persönlich keine Änderungen an diesem File vorgenommen habe, habe ich den Autor nicht geändert. 
#
```
RUN apt-get update
```
Der Befehl `RUN` führt den gegebenen Parameter einfach als Shell Befehl aus. 
Diese Zeile kontrolliert, ob ein Update zur Verfügung steht und falls dies der Fall ist, wird es auch gleich ausgeführt.
#
```
RUN apt-get -q -y install apache2
```
Hier wird der Apache Webserver, genauer gesagt das Paket `apache2` installiert. 
Die Option `-q` steht für quiet, welcher dafür sorgt, dass der Output während der Installation für Logging geeignet ist. 
Die Option `-y` bestätigt automatisch alle Aufforderungen, welche eine weiter Benutzerhandlung erfordern.
#
```
ENV APACHE_RUN_USER www-data
```
Der Befehl `ENV` setzt eine Environment Variabel auf einen gewissen Wert. Die Variabel und der Wert werden nach dem Befehl angegeben. 
In diesem Fall wird der Apache User auf `www-data` gesetzt. 
#
```
ENV APACHE_RUN_GROUP www-data
```
In dieser Zeile wird die Apache Gruppe auf `www-data` gesetzt. 
#
```
ENV APACHE_LOG_DIR /var/log/apache2
```
Hier wird das Log Verzeichnis vom Apache Webserver auf `/var/log/apache2` gesetzt. 
#
```
RUN mkdir -p /var/lock/apache2 /var/run/apache2
```
Auf dieser Zeile werden mit dem `mkdir` Befehl zwei Verzeichnisse erstellt. 
Die Option `-p` besagt, dass fehlende höhere Verzeichnisse ebenfalls erstellt werden sollen. 
```
EXPOSE 80
```
Mit dem Befehl `EXPOSE` kann ein Port definiert werden, welcher auf dem Container geöffnet werden soll. 
In diesem Fall wurde der Port 80 geöffnet. 
```
VOLUME /var/www/html
```
Mit dem Befehl `VOLUME` könne Mountpoints für externe Volumes erstellt werden. 
Hier wurde ein Mountpoint und `/var/www/html` erstellt. 
```
CMD /bin/bash -c "source /etc/apache2/envvars && exec /usr/sbin/apache2 -DFOREGROUND"
```
Der Befehl `CMD` definiert Standards für einen Container. Diese Standards können Scripts enthalten, welche ausgeführt werden. 
In diesem Fall wurde ein Script definiert, welches den Webserver automatisch startet, sobald der Container gestartet wird. 
## Testfälle
Ich habe alle folgenden Tests auf meinem System durchgeführt. Diese können auch als Kontrolle verwendet werden, ob der Container wie gewollt funktioniert. 
|Nr.|   Test        |Erwartetes Verhalten           |Tatsächliches Verhalten      |Test erfolgreich?|
|---|---------------|-------------------------------|-----------------------------|-----------------|
|1  |Zugriff auf Webseite von Docker Host mit Curl  |Der Inhalt der Webseite wird angezeigt|Der Inhalt der Webseite wird angezeigt| Ja                           
|2  |Zugriff auf Webseite von Windows Host mit Curl |Der Inhalt der Webseite wird angezeigt|Der Inhalt der Webseite wird angezeigt| Ja
|3  |Zugriff auf Webseite von Windows Host im Browser|Die Webseite wird angezeigt|Die Webseite wird angezeigt|Ja
## Quellenverzeichnis
**Quelle 1**
Halprin: Dockerfile - kapeli.com 
[https://kapeli.com/cheat_sheets/Dockerfile.docset/Contents/Resources/Documents/index](https://kapeli.com/cheat_sheets/Dockerfile.docset/Contents/Resources/Documents/index)
[Abrufdatum: 07.05.2020]
**Quelle 2**
Autor: Linux man pages - linux.die.net 
[https://linux.die.net/man/](https://linux.die.net/man/)
[Abrufdatum: 07.05.2020]

```
Titel: Ubuntu/Docker mit Apache Webserver
Autor: Nando Rigonalli 
Datum: 07.05.2020
```
