M300 - 20 - Prototyping
======

Dieses Repository behandelt die Installation von Apache, MySQL, MongoDB und NodeJS auf einer  Vagrant Box mit dem Ubuntu-Server (Version 14.04) als OS.

#### Einleitung

Die nachstehende Dokumentation wurde von Michael Blickenstorfer im Rahmen des Moduls M300 (Plattformübergreifende Dienste in ein Netzwerk integrieren)
erarbeitet und zeigt alle Schritte auf, die es zur Einrichtung eines vollständig funktionsfähigen Web-Servers mit Apache und einer MySQL-DB benötigt.

#### Revision History

| Datum         | Änderungen                                                                         |  Kürzel  |
| ------------- |:-----------------------------------------------------------------------------------| :------: |
| 22.08.2018    | Erstellung des Repositorys und der README.md-Datei                                 |    MBL   |
| 23.09.2018    | Einleitung, Voraussetzungen, Inhaltsverzeichnis & Kapitel 1 erarbeitet             |    MBL   |
| 09.09.2018    | ---                                                                                |    MBL   |
| 10.09.2018    | ---                                                                                |    MBL   |
| 10.09.2018    | ---                                                                                |    MBL   |
|      ...      | ...                                                                                |    ...   |

#### Voraussetzungen
* [X] macOS High Sierra (Version 10.13.6)
* [X] VirtualBox (Version 5.2.18)
* [X] Vagrant (Version 2.1.4)
* [X] Vagrant Box "ubuntu/trusty64"

#### Inhaltsverzeichnis
* 01 - [Linux](https://github.com/TacoTaco47/M300#-01---github-account)
* 02 - [Versionsverwaltung](https://github.com/TacoTaco47/M300#--02---git-client)
* 03 - [Apache & MySQL](https://github.com/TacoTaco47/M300#--03---virtualbox)
* 04 - [NodeJS & MogoDB](https://github.com/TacoTaco47/M300#--04---vagrant)
* 05 - [Software-Konfiguration](https://github.com/TacoTaco47/M300#-05---visual-studio-code)
* 06 - [Service-Konfiguration](https://github.com/TacoTaco47/M300#-05---visual-studio-code)
* 07 - [Fazit / Reflexion](https://github.com/TacoTaco47/M300#-06---fazit--reflexion)
* 08 - [Quellenverzeichnis](https://github.com/TacoTaco47/M300#-07---quellenverzeichnis)

___

![](XXX "Bild") 01 - Linux
======

> [⇧ **Nach oben**](XXX)

Ziel dieses Abschnitts ist die Einrichtung der Vagrant Box mit dem Betriebssystem "Ubuntu-Server 14.04"

Folgende Arbeiten müssen gemacht werden:

### Vagrant Box herunterladen
***
1. Webbrowser öffnen
2. Auf www.app.vagrantup.com/boxes/search können alle öffentlich verfügbaren Vagrant-Boxen eingesehen werden. In diesem Fall suchen wir nach "ubuntu/trusty64".
3. Nach einem Klick auf die Box findet man unter "How to use this box with Vagrant:" eine Anleitung zur Einrichtung einer VM
4. Im Tab "New" wird gezeigt, wie eine komplett neue VM mit dieser Box eingerichtet wird: 
    ```Shell
      $ vagrant init ubuntu/trusty64
      $ vagrant up
    ```    
5. Da wir jetzt wissen, wie die Box heisst, können wir mit dem Abschnitt "VM erstellen" fortfahren

### VM erstellen
***
1. Terminal öffnen
2. In gewünschtes Verzeichnis wechseln, wo die VM anschliessend erzeugt werden soll (mit cd Pfad/zu/meiner/VM)
3. Ordner für die VM erstellen (mkdir ubuntu-server)
4. In Ordner wechseln (cd ubuntu-server) und nachfolgende Befehle ausführen:
    ```Shell
      $ vagrant init ubuntu/trusty64
      $ vagrant up
    ```    
5. Die VM ist soweit betriebsbereit und kann per SSH-Verbindung gesteuert werden:
    ```Shell
      $ vagrant ssh

      Welcome to Ubuntu 14.04.5 LTS (GNU/Linux 3.13.0-158-generic x86_64)

      (...)
    ```    
6. System-Updates durchführen: 
     ```Shell
      $ sudo apt-get update
      $ sudo apt-get upgrade
    ```  
7. Maschine herunterfahren:
    ```Shell
      $ sudo shutdown -h now
      
      The system is going down for halt NOW!

      Connection to 127.0.0.1 closed by remote host.
      Connection to 127.0.0.1 closed.
    ```  
8. Terminal schliessen & mit nachfolgendem Abschnitt weiterfahren

### Port-Weiterleitung einrichten
***
1. Virtual Box öffnen
2. Erstellte Vagrant-VM (Virtuelle Maschine) anwählen und im oberen Menü auf "Ändern" klicken
3. Zum Abschnitt "Netzwerk" wechseln und "> Erweitert" aufklappen
4. Den Button "Port-Weiterleitung" anwählen
5. Im neuen Fenster in der rechten Leiste auf das (+) Plus-Symbol klicken
6. Folgende Einträge machen:

    | Name            | Protokoll    | Host-IP      | Host-Port    | Gast-IP   | Gast-Port   |
    | --------------- | ------------ | ------------ | ------------ | --------- | ----------- |
    | `Apache`        | `TCP`        | `127.0.0.1`  | `8080`       | *NULL*    | `80`        |


7. Änderungen übernehmen und Fenster schliessen
8. Im Terminal mit folgendem Befehl die VM wieder starten:
    ```Shell
      $ vagrant up
    ```
9.  SSH-Verbindung aufbauen:
    ```Shell
      $ vagrant ssh
    ```    
10. Mit Abschnitt "Bash" fortfahren


### Bash
***
Bash steht für **Bourne-again shell** und ist eine quelloffene Unix-Shell und Teil des GNU-Projekts. Sie ist heute auf vielen unixoiden Systemen die Standard-Shell.

**Administration**
| Befehl                                    | Beschreibung                                         |
| ----------------------------------------- | ---------------------------------------------------- | 
| `sudo [-i]`                               | Superuser werden (-i = permanent für aktive Session) |
| `sudo service <service name> start|stop`  | Dienst starten oder stopen (z.B. apach2 oder mysql)  |
| `sudo shutdown -r now`                    | OS-Restart (Neustart)                                |
| `sudo shutdown -h now`                    | OS-Shutdown (Herunterfahren)                         |

**Filesystem**
| Befehl        | Beschreibung                                      |
| ------------- | ------------------------------------------------- | 
| `ls`          | Verzeichnis-Inhalt anzeigen (list)                |
| `rm`          | Datei oder Verzeichnis entfernen (remove)         |
| `mv`          | Datei oder Verzeichnis verschieben (move)         |
| `cd`          | In Verzeichnis wechsel (change directory)         |
| `nano`        | Texteditor (z.B. für das Anzeigen von index.html) |
| `df  -h`      | Diskbelegung                                      |
| `free -m`     | Arbeitsspeicher-Belegung (RAM)                    |
| `w`           | Auslastung CPU                                    |

**Netzwerk**
| Befehl                    | Beschreibung                                                      |
| ------------------------- | ----------------------------------------------------------------- | 
| `ifconfig`                | Ausgabe der eigenen konfigurierten IP-Parameter (z.B. IP-Adresse) |
| `netstat -a`              | Ausgabe verwendeter Netzwerk-Ports                                |
| `netstat -tulpen`         | " dito                                                            |
| `net lookup <hostname>`   | Namensauflösung eines Hosts (z.B. google.com)                     |

**Prozesse**
| Befehl        | Beschreibung                      |
| ------------- | --------------------------------- | 
| `ps -ef`      | Anzeige aktiver Prozesse          |
| `top`         | " dito (top = table of processes) |
| `kill <pid>`  | Prozess gem. Prozess-ID beenden   |

**Hilfsprogramme**
| Programm      | Befehl                            | Beschreibung                                                                          |
| ------------- | --------------------------------- | ------------------------------------------------------------------------------------- |
| `curl`        | `curl http://<server>`            | Webseite oder HTTP REST Service aufrufen & Inhalt ausgeben                            |
| `wget`        | `wget http://<server>/<datei>`    | Datei von einem Webserver kopieren                                                    |
| `dos2unix`    | `dos2unix <Datei>`                | Datei von DOS ins UNIX/Linux Format umwandeln (z.B. nach Kopie von Windows-System)    |

Weiter Informationen und Befehle unter: www.linuxcommand.org/lc3_lts0060.php


### APT
***
Das **Advanced Packaging Tool (APT)** ist ein **Paketverwaltungssystem**, das im Bereich des Betriebssystems Debian GNU/Linux entstanden ist und **dpkg** 
(Debian Package Manager) zur eigentlichen Paketverwaltung nutzt. Ziel ist es, eine einfache Möglichkeit zur Suche, Installation und Aktualisierung von
Programmpaketen zur Verfügung zu stellen. 

APT besteht aus einer Programmbibliothek und mehrere dieser Bibliothek nutzenden Komandozeilen-Programmen, von denen **apt-get** und **apt-cache** zentral sind.
Seit Debian 3.1 wird die Benutzung von aptitude als konsolenbasierendes Paketverwaltungssystem empfohlen. 

In der Datei /etc/apt/**sources.list** stehen die sogenannte **Repositories**, also Quellen für Pakete. Dies können entweder CDs oder DVDs, Verzeichnisse auf der Festplatte
oder hauptsächlich Verzeichnis auf HTTP- und FTP-Servern sein. Befindet sich das gesuchte Paket auf einem Server (oder einem lokalen Datenträger), so wird dieses automatisch
heruntergeladen und installiert (bei apt-get install <Paket-Name>).

Die Pakete liegen im **Debian-Paketformat** (.deb) vor, in dem auch die jeweiligen Abhängigkeiten der Programmpakete untereinander abgelegt sind. So werden automatisch
für ein Programm auch eventuell erforderliche Programmbibliotheken mit heruntergeladen und intalliert.

**APT-Befehle**

| Befehl                            | Beschreibung                                                |
| --------------------------------- | ----------------------------------------------------------- | 
| `sudo apt-get update`             | Repositories aktualisieren                                  |
| `sudo apt-get -y upgrade`         | Installierte Software aktualisieren                         |
| `sudo apt-get -y autoremove`      | Aufräumen -> Nicht mehr benötigte Software entfernen        |
| `sudo apt-get -y install apache2` | Programmpaket installieren (hier Apache2 Webserver)         |
| `sudo apt-cache search [keyword]` | Nach bestimmten Programmpaket suchen                        |
| `sudo dpkg -i [Programmpaket]`    | Programmpaket installieren, was zuvor heruntergeladen wurde |


### cURL
***
Das **Kommandozeilen-Programm** cURL (Client for URLs / Curl URL Request Library) dient zur Übertrgung von Dateien in Rechnernetzen. Es ist Bestandteil der meisten
Linux-Distributionen und auch von Mac OS X. Die zugehörige Programmbibliothek libcurl wird von zahlreichen Programmen und Programmiersprachen verwendet.

**Aktive Ports/Services mit cURL testen**
cURL eignet sich auch zum Testen von aktiven Ports oder Services auf entsprechenden Servern. Dazu eignet sich am besten die `-f` (HTTP -> Fail silently | no output at all) 
Option. 

Hier eine kleine Befehlszeile, die testet, ob ein Webserver auf Port 8080 läuft (Ausgabe "running") und wenn nicht, "stopped" ausgibt:
```Shell
      $ curl -f http://localhost:8080 >/dev/null 2>&1 && { echo "running"; } || { echo "stopped"; } }
``` 

Langform (in Skript):
```Shell
      curl -f http://localhost >/dev/null 2>&1
      if    [ $? -eq 0 ]
        then
            echo "running"
      else
            echo "stopped"
      fi
``` 

**Skript erstellen**
1. Terminal öffnen
2. Mit SSH auf VM verbinden
3. Shellscript mit nano erstellen:
```Shell
      $ sudo nano myscript
```    
4. Folgenden Inhalt einfügen:
```Shell
      #!/bin/bash
      # 
      # Endlosscript wo Testet ob der Apache Webserver läuft

      while    [ 1 ]
      do
        curl -f http://localhost >/dev/null 2>&1
        if    [ $? -eq 0 ]
        then
            echo "running"
        else
            echo "stopped"
        fi    
      done
``` 
5. Datei mit `CTRL + X + y + [Enter]` speichern & schliessen
6. Script starten:
```Shell
      $ bash myscript
```  
7. Script mit `CTRL + C` wieder beenden
  

### Zusammenfassung
***

**Linux**
> Als Linux bezeichnet man in der Regel freie, **unix-ähnliche Betriebssysteme**, die auf dem **Linux-Kernel** und wesentlich auf GNU-Software basieren.
> Beim Gebrauch auf Computern kommmen meist sogenannte **Linux-Distributionen** zum Einsatz. Eine Distribution fasst den Linux-Kernel mit verschiedener Software
zu einem Betriebssystem zusammen, das für die Endnutzung geeignet ist. Dabei passen viele Distributionen und versierte Benutzer den Kernel an ihre eigenen
Bedürfnisse an.

**Bash**
> Bash (Bounre-again shell) ist eine **freie Unix-Shell** und Teil des GNU-Projekts. Sie ist heute auf vielen unixoiden Systemen die Standard-Shell.

**APT**
> Das Advanced Packaging Tool (APT) ist ein **Paketverwaltungssystem**, das im Bereich des Betriebssystems Debian GNU/Linux entstanden ist und **dpkg** zur eigentlichen
> Paketverwaltung nutzt. **Ziel** ist es, eine einfache Möglichkeit zur Suche, Installation und Aktualisierung von Programmpaketen zur Verfügung zu stellen.

**cURL**
> cURL (Client for URLS / Curl URL Request Library) ist ein **Kommandozeilen-Programm** zum **Übertragen** von Dateien in Rechnernetzen.



![](XXX "Bild") 02 - Versionsverwaltung
======

> [⇧ **Nach oben**](XXX)

Ziel dieses Abschnitts ist die Einrichtung der Vagrant Box mit dem Betriebssystem "Ubuntu-Server 14.04"

Folgende Arbeiten müssen gemacht werden:

### Vagrant Box herunterladen
***