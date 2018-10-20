M300 - Prototyping (20)
======

Dieses Repository behandelt die Installation von Apache, MySQL, MongoDB und NodeJS auf einer  Vagrant Box mit dem Ubuntu-Server (Version 14.04) als OS.

#### Einleitung

Die nachstehende Dokumentation wurde von Michael Blickenstorfer im Rahmen des Moduls M300 (Plattformübergreifende Dienste in ein Netzwerk integrieren)
erarbeitet und zeigt alle Schritte auf, die es zur Einrichtung eines vollständig funktionsfähigen Web-Servers mit Apache und einer MySQL-DB benötigt.

#### Revision History
https://raw.githubusercontent.com/TacoNaco47/M300_20_Prototyping/master/images/RESTSample.png
| Datum         | Änderungen                                                                         |  Kürzel  |
| ------------- |:-----------------------------------------------------------------------------------| :------: |
| 22.08.2018    | Erstellung des Repositorys und der README.md-Datei                                 |    MBL   |
| 23.09.2018    | Einleitung, Voraussetzungen, Inhaltsverzeichnis & Kapitel 1 erarbeitet             |    MBL   |
| 25.09.2018    | Restliche Kapitel fertiggestellt                                                   |    MBL   |
| 20.10.2018    | Verknüpfungen angepasst                                                            |    MBL   |
|      ...      | ...                                                                                |    ...   |

#### Voraussetzungen
* [X] macOS High Sierra (Version 10.13.6)
* [X] VirtualBox (Version 5.2.18)
* [X] Vagrant (Version 2.1.4)
* [X] Vagrant Box "ubuntu/trusty64"

#### Inhaltsverzeichnis
* 01 - [Linux](https://github.com/TacoNaco47/M300_20_Prototyping#-01---linux)
* 02 - [Versionsverwaltung](https://github.com/TacoNaco47/M300_20_Prototyping#-02---versionsverwaltung)
* 03 - [Apache & MySQL](https://github.com/TacoNaco47/M300_20_Prototyping#-03---apache--mysql)
* 04 - [NodeJS & MogoDB](https://github.com/TacoNaco47/M300_20_Prototyping#-04---nodejs--mongodb)
* 05 - [Software-Konfiguration](https://github.com/TacoNaco47/M300_20_Prototyping#-05---software-konfiguration)
* 06 - [Service-Konfiguration](https://github.com/TacoNaco47/M300_20_Prototyping#-06---service-konfiguration)
* 07 - [Reflexion](https://github.com/TacoNaco47/M300_20_Prototyping#zusammenfassung-5)

___

![](https://raw.githubusercontent.com/TacoNaco47/M300_20_Prototyping/master/images/Linux_36x36.png "Linux") 01 - Linux
======

> [⇧ **Nach oben**](https://github.com/TacoNaco47/M300_20_Prototyping)

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
| curl          | `curl http://<server>`            | Webseite oder HTTP REST Service aufrufen & Inhalt ausgeben                            |
| wget          | `wget http://<server>/<datei>`    | Datei von einem Webserver kopieren                                                    |
| dos2unix      | `dos2unix <Datei>`                | Datei von DOS ins UNIX/Linux Format umwandeln (z.B. nach Kopie von Windows-System)    |

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



![](https://raw.githubusercontent.com/TacoNaco47/M300_20_Prototyping/master/images/Versionsverwaltung_36x36.png "Versionsverwaltung") 02 - Versionsverwaltung
======

> [⇧ **Nach oben**](https://github.com/TacoNaco47/M300_20_Prototyping)

Eine Versionsverwaltung ist ein System, das zur Erfassung von Änderungen an Dokumenten oder Dateien verwendet wird. Alle Versionen werden in einem Archiv mit Zeitstempel und Benutzerkennung gesichert und können später wiederhergestellt bzw. abgerufen werden. Versionsverwaltungssysteme werden typischerweise in der Softwareentwicklung eingesetzt, um Quelltexte zu verwalten. Allerdings werden solche Systeme auch bei Büroanwendungen oder Content-Management-Systeme (CMS) eingesetzt. Bekannte Vertreter sind Git (verteiltes System) oder der Team Foundation Server (zentrales System). 

#### Hauptaufgaben
* Protokollierung der Änderungen:
  > Es kann jederzeit nachvollzogen werden, wer wann was geändert hat.
* Wiederherstelltung von alten Ständen einzelner Dateien:
  > Somit können versehentliche Änderungen jederzeit wieder rückgängig gemacht werden.
* Archivierung der einzelnen Stände eines Projektes:
  > Dadurch ist ges jederzeit möglich, auf alle Versionen zuzugreifen
* Koordinierung des gemeinsamen Zugriffs von mehreren Entwicklern auf die Dateien.
* Gleichzeitige Entwicklung mehrerer Entwicklungszweige (engl. Branches) eines Projektes.


### Repository
***
Ein Repository (engl. Lager, Depot oder auch Quelle), ist ein verwaltetes Verzeichnis zur Speicherung und Beschreibung digitaler Objekte für ein digitales Archiv.
Bei den verwalteten Objekten kann es sich beispielsweise um Programme (Software-Repository), Publikationen (Dokumentserver), Datenmodelle (Metadaten-Repository) oder betriebswirtschaftliche Verfahren handeln. Häufig beinhaltet ein Repository auch Funktionen zur Versionsverwaltung der digitalen Objekte.

**Git** <br>
Git ist eine freie Software zur verteilten Versionsverwaltung von Dateien, die durch Linus Trovalds initiiert wurde.

**GitHub** <br>
GitHub ist ein webbasierter Online-Diens, der Software-Entwicklungsprojekte auf seinen Servern bereitstellt. Namensgebend war das Versionsverwaltungssystem Git. Zusätzlich bietet der Dienst diverse Management und Bug-Tracking-Funktionalitäten.

**GitLab** <br>
GitLab ist ebenfalls eine Webanwendung zur Versionsverwaltung für Softwareprojekte auf Basis von Git. Sie bietet diverse Management und Bug-Tracking-Funktionalitäten, sowie mit GitLab CI ein System zur kontinuierlichen Integration.


### Markdown
***
Markdown ist eine vereinfachte Auszeichnungssprache.

Ein Ziel von Markdown ist, dass schon die Ausgangsform ohne weitere Konvertierung leich lesbar ist. Als Auszeichnungselemente wurden daher vor allem Auszeichnungsarten verwendet, die in Klartext und E-Mails üblich sind.

Genutzt wird Markdown vorwiegend auf Plattformen mit eher technikaffinem Publikum wie GitHub, Stack Overflow oder der Blogging-Plattform Ghost. Markdown wird ausserdem häufig bei Readme-Dateien verwendet.

Markdown-Dateien (.md) können über die Weboberfläche von GitLab oder GitHub editiert werden. Alternativen sind die Wiki-Erweiterungen von Eclipse, der GitBook Editor oder Visual Studio Code.


### Zusammenfassung
***
Ein **Repository** ist ein verwaltetes Verzeichnis zur Speicherung und Beschreibung von digitalen Objekten für ein digitales Archiv. Bei den verwalteten Objekten kann es sich beispielsweise um Programme (Software-Repository), Publikationen (Dokumentenserver), Datenmodelle (Metadaten-Repository) oder betriebswirtschaftliche Verfahren handeln. Häufig beinhaltet ein Repository auch Funktionen zur Versionsverwaltung der verwalteten Objekte.

**Git** ist eine freie Software zur verteilten Versionsverwaltung von Dateien, die durch Linus Torvalds initiiert wurde.

**GitHub** ist ein webbasierter Online-Dienst, der Software-Entwicklungsprojekte auf seinen Servern bereitstellt. Namensgebend war das Versionsverwaltungssystem Git. Zusätzlich bietet er diverse Management und Bug-Tracking-Funktionalitäten.

**GitLab** ist eine Webanwendung zur Versionsverwaltung für Softwareprojekte auf Basis von git. Sie bietet diverse Management und Bug-Tracking-Funktionalitäten, sowie mit GitLab CI ein System zur kontinuierlichen Integration.

**Markdown** ist eine vereinfachte Auszeichnungssprache deren Ziel es ist, dass schon die Ausgangsform ohne weitere Konvertierung leicht lesbar ist.






![](https://raw.githubusercontent.com/TacoNaco47/M300_20_Prototyping/master/images/Apache_36x36.png "Apache") 03 - Apache + MySQL
======

> [⇧ **Nach oben**](https://github.com/TacoNaco47/M300_20_Prototyping)

Nach den vorhergehenden Theorie-Inputs geht es nun mehr und mehr in die Praxis. In diesem Abschnitt wird der Apache Webserver installiert und die MySQL-Datenbank auf der Vagrant-VM eingerichtet.

### LAM
***
LAM steht für Linux-, Apache- & MySQL-Server. Mit dieser Kombination wird eine Datenablageplattform geschaffen, die beispielsweise für Sensordaten verwendet werden kann. 

Der LAM-Server wird "REST-fähig" gemacht, damit Daten in eine Serverdatei und in eine MySQL-Datenbank geschrieben werden können.


### Apache
***
Der Apache HTTP Server ist ein quelloffenes und freies Produkt der Apache Software Foundation und der meistbenutzte Webserver im Internet.

Apache kann sehr gut mit sogenannten Modulen erweitert werden, die bestimmte Zusatzfunktionen mitbringen und immer mit 'mod_' beginnen (z.B. mod_rewrite, mod_php5, mod_ssl).

Es gibt u.a. Module für:
* SSL:
    * `mod_ssl` für OpenSSL
    * `mod_gnutls` für GnuTLS
* Dynamische Skript- & Programiersprachen:  
    * `mod_php5`
    * `mod_perl`
    * `mod_python`
* Protokolle (z.B. WebDAV):
    * `mod_dav`
    * `mod_dav_fs`
    * `mod_dav_lock`
    * `mod_dav_repos`
* Authentifizierung:
    * `mod_auth`
* Proxy:
    * `mod_proxy`


Installiert wird der Webserver (Apache) inkl. dem CGI-Modul wie folgt:

1. Terminal öffnen
2. Vagrant-VM starten:
    ```Shell
      $ vagrant up
    ```
3. SSH-Verbindung aufbauen (zur VM):
    ```Shell
      $ vagrant ssh
    ```
4. Apache-Webserver inkl. Dokumentation installieren: 
    ```Shell
      $ sudo apt-get install apache2 apach2-doc
    ```
5. CGI-Modul hinzufügen:
    ```Shell
      $ sudo a2enmod gi
    ```
    > Hier kann es zu folgender "Fehlermeldung" kommen: ![Fehlermeldung](https://raw.githubusercontent.com/TacoNaco47/M300_20_Prototyping/master/images/Fehlermeldung_CGI-Modul_Apache.png "Bild")
    Die Meldung kann aber gemäss [diesem Forum-Post](https://askubuntu.com/questions/683126/1404-your-mpm-seems-to-be-threaded-selecting-cgid-instead-of-cgi-module-cgid) ignoriert werden.
    
6. Service-Neustart durchführen:
    ```Shell
      $ sudo service apache2 restart
    ```
7. Mit Dokumentation fortfahren (...)

#### Struktur Apache-Server
Die Inhalte für den Server werden in einem als "document root" bezeichneten Verzeichnis abgespeichert. Dies bedeutet, dass der Apache Webserver dort nach den auszuliefernden (HTML-)Dateien Ausschau hält.

In einem Linux-System befindet sich der Document-Root unter `/var/www/html`

Von der Client-Seite werden die dort abgelegten Dateien, im Browser, mittels http://Host-IP-Adresse/ abgerufen. In unserer lokalen Vagrant VM lässt sich dies über den Localhost, also http://localhost/ bewerkstelligen.


### HTTP-Protokoll
***
Weiter geht es mit dem HTTP-Protokoll. Das **Hypertext Transfer Protcol** (HTTP) ist ein Protkoll zur Übertragung von Nachrichten und Daten. Es wird hauptsächlich eingesetzt, um Webseiten (Hypertext-Dokumente) aus dem WWW (World Wide Web) in einen Webbrowser zu laden. Es ist jedoch nicht prinzipiell darauf beschränkt und auch als allgemeines Übertragungsprotkoll weitverbreitet.

Jede Nachricht besteht dabei aus zwei Teilen, dem Nachrichtenkopf (Message Header) und dem Nachrichtentrumpf (Message Body). Der Nachrichtenkopf enthält die Anfragemethode und Informationen über den Nachrichtentrumpf, wie etwa verwendete Kodierungen oder den Inhaltstyp. Der Nachrichtentrumpf enhält entsprechend die Nutzdaten.

Von den Nachrichten gibt es zwei unterschiedliche Arten: die Anfrage (engl. Request) vom Client an den Server und die Antwort (engl. Response) als Reaktion darauf, vom Server zum Client.

#### Anwendung
HTTP dient zum Anfordern, Schreiben und Löschen von Daten bzw. Dateien auf einem Webserver, der das HTTP-Protokoll unterstützt.

#### HTTP-Abfragemethoden (Top 4)
1. **GET**
    > Ist die gebräuchlichste Methode. Mit ihr wird eine Ressource (z.B. HTML-Datei) vom Server angefordert.
2. **POST**
    > Schickt die Daten zur weiteren Verarbeitung zum Server.
3. **PUT**
    > Dient dazu eine Ressource (z.B. eine Bilddatei) auf einen Webserver hochzuladen.
4. **DELETE**
    > Löscht die angegebene Ressource auf dem Server.

Heute ist **PUT**, ebenso wie **DELETE**, kaum implementiert. Beides erlangt mit dem REST Programmierparadigma eine neue Bedeutung.

#### HTML (Body-Teil)
Die Hypertext Markup Language (HTML) ist eine textbasierte und maschinenlesbare Sprache, die zur Strukturierung digitaler Dokumente - wie etwa Texte mit Hyperlinks, Bildern und anderen Inhalten - eingesetzt wird. 

HTML-Dokumente sind die Grundlage des World Wide Webs (WWW) und werden zur graphischen Darstellung durch die Webbrowser benötigt.

### REST
***
REST steht für **Representational State Transfer** und bezeichnet ein Programmierparadigma (grundsätzliche Denkweise) für verteilte Systeme.

Mit REST versteht man eine eine Abstraktion der Struktur und des Verhaltens des World Wide Web (HTTP GET, PUT, POST, DELETE).

Das Programmierparadigma hat das Ziel, einen Architekturstil zu schaffen, der die Anforderungen des modernen Web besser darstellt. Dabei unterscheidet sich REST vor allem in der Forderung nach einer einheitlichen Schnittstelle von anderen Architekturstilen.

> Wichtig: Wird über das HTTP-Protokoll zugegriffen, entsprechen die REST-Operationen den HTTP-Abfragemethoden.

#### Hintergrund
Alle bauen APIs (Programmierschnittstellen). Grob geschätzt bestehen 80% Prozent der heute entwickelten Anwendungen im Kern aus einer oder mehreren serverseitigen Komponenten, die Geschäftslogik kapseln und diese ihren Clients über eine RESTful API zur Verfügung stellen. Ist das REST-Paradigma einmal verstanden, dann sind REST-APIs klar und einfach zu benutzen.

#### Anwendungsbeispiel
Den fachlichen Hintergrund des Beispiel-API bildet ein einfaches Shop-System. Das Domainmodell des Shops besteht aus den Entitäten Customer, Order und Product. Die untenstehende Abbildung zeigt deren Beziehungen, URIs sowie JSON-Repräsentationen:

[Beispiel](https://raw.githubusercontent.com/TacoNaco47/M300_20_Prototyping/master/images/RESTSample.png "Beispiel REST")

Eine Ressource im REST-Kontext ist eine Abstraktion, die ein "Ding" referenziert. Dieses Ding kann z.B. ein einzelner Kunde, eine Liste aller Kunden oder auch eine Liste von Kunden mit dem Namen 'Meier' sein. Ressourcen werden über URIs identifiziert. So identifiziert der URI `/customers` die Liste aller Kunden und die Ressource `/customer/1` den Kunden mit der ID 1. Neben einer Identität besitzen Ressourcen eine oder mehrere Repräsentationen. In diesem Anwendungsbeispiel werden Ressourcen in JSON repräsentiert und in diesem Format auch zwischen Client und Server übertragen.

Beziehungen zwischen Ressourcen werden über Links beschrieben. Für HTML-repräsentierte Ressourcen wird häufig das HTML-Element verwendet, während in JSON-Repräsentationen verlinkte Resourcen als Listen von IDs beschrieben werden.

Das Anfragen, Anlegen Verändern und Löschen von Ressourcen erfolgt über die bekannten HTTP-Methoden, die im REST-Kontext auch als HTTP-Verben bezeichnet werden. So liefert beispielsweise `GET /customers/1/orders/9` die Bestellung mit der ID 9, während `DELETE /customers/1/orders/9` diese Bestellung löscht.


### CGI-Script
***
Das **Common Gateway Interface** (CGI) ist ein Standard für den Datenaustausch zwischen einem Webserver und dritter Software, die Anfragen bearbeitet. CGI ist eine schon länger bestehende Variante, Webseiten dynamisch bzw. interaktiv zu machen.

#### Installation
Da wir auf dem Webserver das CGI-Modul bereits geladen haben, können wir nun ein entsprechendes Skript auf dem Webserver ablegen. Dieses ist in diesem Repository bereits unter `cgi-rest-script.txt` abgelegt. Nach dem Download, kann mit folgenden Schritten fortgefahren werden:

1. Terminal öffnen
2. Vagrant-VM starten:
    ```Shell
      $ vagrant up
    ```
3. SSH-Verbindung aufbauen (zur VM):
    ```Shell
      $ vagrant ssh
    ```
4. Unterordner im Verzeichnis `/usr/lib/cgi-bin` erstellen:
    ```Shell
      $ sudo mkdir `/usr/lib/cgi-bin/rest`
    ```  
5. In das Verzeichnis `/usr/lib/cgi-bin/rest` wechseln und das Skript einfügen bzw. erstellen:
    ```Shell
      $ cd /usr/lib/cgi-bin/rest
      $ sudo nano cgi-rest-script.txt   # Nun den Inhalt einfügen & das Script speichern!
    ```
6. Ordner `data` im Verzeichnis `/var/www/html` erstellen:
    ```Shell
      $ sudo mkdir /var/www/html/data                       # Ordner erstellen
      $ sudo chown www-data:www-data /var/www/html/data     # Bereichtigungen für Apache-Servicebenutzer anpassen
    ```
7. Script ausführbar machen:
    ```Shell
      $ sudo chmod +x /usr/lib/cgi-bin/rest/cgi-rest-script.txt 
    ```
8. Script-Datei mit `dos2unix` 'formatieren':
    ```Shell
      $ sudo dos2unix /usr/lib/cgi-bin/rest/cgi-rest-script.txt 
    ```

#### Praxisbeispiele
Nun kann das Script zusammen mit cURL ausgetestet werden. Dazu müssen alle nachfolgenden Beispiele durchgeführt werden.
Anschliessend können die geschriebenen Daten unter http://localhost:8080/data/ abgerufen werden.

**GET** - Daten vom Server lesen
```Shell
    curl http://localhost/cgi-bin/rest/cgi-rest-script.txt 
    curl http://localhost/cgi-bin/rest//cgi-rest-script.txt/timestamp
```

**POST** - Daten auf Server schreiben
```Shell
    curl -v -X POST http://localhost/cgi-bin/rest/cgi-rest-script.txt  -d "poti=0.5&hall=0.1&light=0.8&temp=26"
```

**PUT** - Datei inkl. Inhalt auf Server erstellen
```Shell
    curl -v -X PUT http://localhost/cgi-bin/rest/cgi-rest-script.txt?test.txt -d "Das sind Daten fuer den Server"
```

**DELETE** - Datei vom Server löschen
```Shell
    curl -v -X DELETE http://localhost/cgi-bin/rest/cgi-rest-script.txt?test.txt
``` 

### MySQL
***
Eine Datenbank, auch Datenbanksystem (DBS) genannt, ist ein System zur elektronischen Datenverwaltung. Die wesentliche Aufgabe eines DBS ist es, grosse Datenmengen effizient, wiederspruchsfrei und dauerhaft zu speichern und benötigte Teilmengen in unterschiedlichen, bedarfsgerechten Darstellungsformen für Benutzer und Anwendungsprogramme bereitszustellen.

MySQL ist eines der weltweit verbreitesten relationalen Datenbankverwaltungssysteme . Es ist als Open-Source-Software sowie als komerzielle Enterprise Version für verschiedene Betriebssysteme verfügbar und bildet die Grundlage für dynamische Webauftritte.

#### Installation
1. Terminal öffnen
2. Vagrant-VM starten:
    ```Shell
      $ vagrant up
    ```
3. SSH-Verbindung aufbauen (zur VM):
    ```Shell
      $ vagrant ssh
    ```
4. MySQL-Anwendungen installieren & dabei Zugangspasswort festelegen:
    ```Shell
      $ sudo apt-get install mysql-server mysql-client
    ```
5. Mit der Konfiguration fortfahren (...)

#### Konfiguration
1. Beim Datenbankserver anmelden:
    ```Shell
      $ mysql -u root -p [mein Passwort]
    ```
2. Datenbank `sensoren` anlegen:
    ```SQL
      > CREATE DATABASE IF NOT EXISTS sensoren;
    ```
3. DB-Benutzer `www-data` mit dem Passwort `mbed` erstellen:
    ```SQL
      > create user 'www-data'@'localhost' identified by 'mbed'; 
      > grant usage on *.* to 'www-data'@'localhost' identified by 'mbed';
    ```
4. Zugriff auf die Datenbank `sensoren` erteilen:
    ```SQL
      > grant all privileges on sensoren.* to 'www-data'@'localhost';
      > flush privileges;
    ```
5. Datenbank wechseln:
    ```SQL
      > use sensoren;
    ```
6. Tabelle `data` für die Sensordaten erstellen:
    ```SQL
      > create table data ( seq INT PRIMARY KEY AUTO_INCREMENT, poti FLOAT, light FLOAT, hall FLOAT, temp FLOAT, created TIMESTAMP DEFAULT CURRENT_TIMESTAMP );
    ```
7. Testdaten einlesen:
    ```SQL
      > insert into data(poti, light, hall, temp) values ( 0.9, 0.8, 0.49, 25.2 );
      > insert into data(poti, light, hall, temp) values ( 0.8, 0.7, 0.48, 25.1 );
    ```
9. Weitere Abfragen durchführen:
    ```SQL
      # lesen (alle Daten)
      > select * from data;

      # Mittelwert vom Temperaturwert ausgeben
      > select avg(temp) from data;

      # Kleinster Wert vom Temperaturwert ausgeben
      > select min(temp) from data;

      # Grösster Wert vom  Temperaturwert ausgeben
      > select max(temp) from data;
    ```
10. Abmelden:
    ```SQL
      > quit;
    ```

#### CGI-Script erstellen
Das nachfolgende CGI-Script empfängt die Daten via HTTP POST, bereitet diese als SQL-Statement auf und schreibt die Daten in die Datenbank. Wie auch das vorherige Script, kann dieses vom Repository heruntergeladen und eingefügt werden.

1. Terminal öffnen
2. Vagrant-VM starten:
    ```Shell
      $ vagrant up
    ```
3. SSH-Verbindung aufbauen (zur VM):
    ```Shell
      $ vagrant ssh
    ```
2. Unterordner im Verzeichnis `/usr/lib/cgi-bin` erstellen:
    ```Shell
      $ sudo mkdir `/usr/lib/cgi-bin/restsql`
    ```
3. Ins Verzeichnis wechseln und das Skript einfügen bzw. erstellen:
    ```Shell
      $ cd /usr/lib/cgi-bin/restsql
      $ sudo nano cgi-rest-sql-script.txt   # Nun den Inhalt einfügen & das Script speichern!
    ```
4. Script ausführbar machen:
    ```Shell
      $ sudo chmod +x /usr/lib/cgi-bin/restsql/cgi-rest-sql-script.txt 
    ```
5. Script-Datei mit `dos2unix` 'formatieren':
    ```Shell
      $ sudo dos2unix /usr/lib/cgi-bin/restsql/cgi-rest-sql-script.txt 
    ```

#### Praxisbeispiele
Nun kann dieses Script ebenfalls zusammen mit cURL ausgetestet werden. Dazu muss das nachfolgende Beispiel durchgeführt werden.
Anschliessend können die geschriebenen Daten unter http://localhost/cgi-bin/restsql abgerufen werden.

**POST** - Daten auf Server schreiben
```Shell
    curl -v -X POST http://localhost/cgi-bin/restsql -d "poti=0.5&hall=0.1&light=0.8&temp=26"
```


### cURL
***
cURL (Client for URLs / Curl URL Request Library) ist ein Kommandozeilen-Programm zum Übertragen von Dateien in Rechnernetzen. Das Programm cURL steht unter der offenen MIT-Lizenz und ist auf verschiedene Betriebssysteme portiert worden. Es ist Bestandteil der meisten Linux-Distributionen und auch von Mac OS X. Die zugehörige Programmbibliothek libcurl wird von zahlreichen Programmen und Programmiersprachen verwendet.


### Zusammenfassung
***
Mittels einer Kombination von **Linux** (Ubuntu basiert auf Linux bzw. Debian), dem Web-Server **Apache** und der relationalen Datenbank **MySQL** bauten wir eine Datenablageplattform für Sensordaten.

Dazu haben wir verwendet:

 **HTTP** <br>
 Das Hypertext Transfer Protocol (HTTP) ist ein Protokoll zur Übertragung von Nachrichten und Daten. Es wird hauptsächlich eingesetzt, um Webseiten (Hypertext-Dokumente) aus dem World Wide Web (WWW) in einen Webbrowser zu laden. Es ist jedoch nicht prinzipiell darauf beschränkt und auch als allgemeines Dateiübertragungsprotokoll sehr verbreitet.

 **REST** <br>
 Representational State Transfer (REST) bezeichnet ein Programmierparadigma für verteilte Systeme. REST ist eine Abstraktion der Struktur und des Verhaltens des World Wide Web. REST fordert, dass eine Web-Adresse genau einen Seiteninhalt repräsentiert, und dass ein Web-/REST-Server auf mehrfache Anfragen mit demselben URI auch mit demselben Webseiteninhalt antwortet.

 **CGI** <br>
 Das Common Gateway Interface (CGI) ist ein Standard für den Datenaustausch zwischen einem Webserver und dritter Software, die Anfragen bearbeitet. CGI ist eine schon länger bestehende Variante, Webseiten dynamisch bzw. interaktiv zu machen, deren erste Überlegungen auf das Jahr 1993 zurückgehen.
 
 **MySQL** <br>
 MySQL ist eines der weltweit verbreitetsten relationalen Datenbankverwaltungssysteme. Es ist als Open-Source-Software sowie als kommerzielle Enterprise Version für verschiedene Betriebssysteme verfügbar und bildet die Grundlage für viele dynamische Webauftritte.

Durch das Zusammenspiel von REST und CGI kann ein HTTP Server dynamisch (HTTP GET) Inhalte anzeigen und Daten mittels HTTP POST, PUT oder DELETE empfangen. Die Methoden GET, POST, PUT und DELETE entsprechen dem REST-Paradigma.



![](https://raw.githubusercontent.com/TacoNaco47/M300_20_Prototyping/master/images/NodeJS_36x36.png "NodeJS") 04 - NodeJS + MongoDB
======

> [⇧ **Nach oben**](https://github.com/TacoNaco47/M300_20_Prototyping)

Neben MySQL kann auch die MongoDB-Anwendung eingesetzt werden. Dieser Variante widmen wir uns nun in diesem Kapitel.

### NodeJS
***
Node.js ist eine serverseitige Plattform in der Softwareentwicklung zum Betrieb von Netzwerkanwendungen. Insbesondere Webserver lassen sich damit realisieren. 

Node.js wird in der JavaScript-Laufzeitumgebung "V8" ausgeführt, die ursprünglich für Google Chrome entwickelt wurde, und bietet daher eine ressourcensparende Architektur, die eine besonders große Anzahl gleichzeitig bestehender Netzwerkverbindungen ermöglicht.

#### Installation & Betrieb
1. Terminal öffnen
2. Vagrant-VM starten:
    ```Shell
      $ vagrant up
    ```
3. SSH-Verbindung aufbauen (zur VM):
    ```Shell
      $ vagrant ssh
    ```
4. Node.js installieren:
    ```Shell
      $ sudo apt-get install nodejs
    ```  
5. Datei `main.js` erstellen (sudo nano) & mit folgenden Inhalt füllen:
    ```Javascript
      $ 
        var http = require("http");

        http.createServer(function (request, response) {

            response.writeHead(200, {'Content-Type': 'text/plain'});

            // Send the response body as "Hello World"
            response.end('Hello World\n');

        }).listen(8081);

        // Console will print the message
        console.log('Server running at http://127.0.0.1:8081/');
    ```  
6. Script mit NodeJS starten:
    ```Shell
      $ nodejs main.js &
    ```  
7. In neuem Terminal-Fenster eine cURL-Abfrage starten:
    ```Shell
      $ curl localhost:8081
    ```
8. Wenn Test erfolgreich, kann das Terminal wieder geschlossen werden.


### MongoDB
***
Die MongoDB ist eine der marktführenden NoSQL-Datenbanken. MongoDB wurde für die heutige moderne IT-Landschaft konzipiert und erlaubt eine agilere Entwicklung und höhere Skalierbarkeit von Anwendungen.

Bei einer MongoDB spricht man von einem sogenannter Document Store.

Document Stores weisen folgende Eigenschaften auf:
* Weisen eine Schlüssel Werte-Paar Speicherstruktur auf, ihre Werte sind aber komplexe Datenstrukturen, die als Dokumente bezeichnet werden.
* Der Schlüssel ist ein eindeutiger Wert über die ganze DB hinweg.
* Dokumente sind unabhängig voneinander. Das heisst, dass die Struktur der jeweiligen Dokumente sich massiv unterscheiden können.
* Es besteht die Möglichkeit, Dokumente basierend auf ihren Attributen zu indexieren. Um Sichten auf ähnliche Dokumente, sogenannte Kollektionen von Dokumente, zu erstellen.
* Dokumente können anhand der Attribute gefiltert werden und Relationen zwischen Dokumenten mit Verweise auf die Schlüsselwerte in einem Dokument erstellt werden.

#### Anwendung
Der Einsatz von MongoDBs ist in folgenden Szenarien relevant:
* Realtime Analytics
* Content Manangement Systems (CMS)
* Caching
* Single Document, partial updates
* Speichern von Dokumenten in JSON

#### Installation
1. Terminal öffnen
2. Vagrant-VM starten:
    ```Shell
      $ vagrant up
    ```
3. SSH-Verbindung aufbauen (zur VM):
    ```Shell
      $ vagrant ssh
    ```
4. MongoDB installieren:
    ```Shell
      $ sudo apt-get install mongodb
    ```
5. Mit der Konfiguration fortfahren (...)

#### Konfiguration
1. MongoDB-CLI starten:
    ```Shell
      $ mongo
    ```
2. Daten im JSON Format einfügen:
    ```js
      db.adressbuch.insert({
            vorname: "Daniel",
            nachname: "Fasel",
            email: "df@scigility.com",
            adresse: 
            {
                strasse: "Rte du soleil 22",
                ort: "Fribourg",
                plz: 1700,
                land: "Switzerland"
                },
            korrespondenzsprache: "französisch"
      })
      
      db.adressbuch.insert({
            vorname: "Andreas",
            nachname: "Meier",
            email: "andreas.meier@unifr.ch",
            adresse: 
            {
                strasse: "Bd de Pérolles 90",
                ort: "Fribourg",
                plz: 1700
            }
      })
    ```
3. Datenbank-Abfragen durchführen:
    * Dokumente mit Ort 'Fribourg'
        ```js
        db.adressbuch.find({"adresse.ort" : "Fribourg"})
        ```
    * Dokumente mit Tag: 'korrespondenzsprache'
          ```js
        db.adressbuch.find( { korrespondenzsprache: { $exists: true }} )
        ```
    * Anzahl Dokumente mit PLZ 1700 (Fribourg)
        ```js
        db.adressbuch.find( { "adresse.plz": 1700 } ).count()
        ```
    * Reguläre Ausdrücke
        ```js
        db.adressbuch.find( { "adresse.ort": /^Fr/i } )
        db.adressbuch.find( { "adresse.ort": /bourg/i } )
        ```

#### Praxisbeispiel
1. Aus MongoDB-CLI mit `CTRL + C` herausgehen
2. Temporäres Verzeichnis erstellen:
    ```Shell
        $ mkdir temp
    ```
3. In das entsprechende Verzeichnis wechseln:
    ```Shell
        $ cd /temp
    ```
4. Testdaten mit `wget` herunterladen:
    ```Shell
        $ wget https://raw.githubusercontent.com/mongodb/docs-assets/primer-dataset/primer-dataset.json
    ```
5. Testdaten in MongoDB importieren:
    ```Shell
        $ mongoimport --db test --collection restaurants --drop --file primer-dataset.json
    ```
6.  Wieder in MongoDB-CLI wechseln:
    ```Shell
      $ mongo
    ```
7. In Datenbank `test` wechseln:
    ```SQL
      > use test
    ```
8. Tabelle `restaurants` abfragen:
    ```js
      > db.restaurants.find()
    ```
9.  MongoDB-CLI mit `CTRL + C` schliessen
10. Mit Abschnitt "Zusammenfassung" fortfahren (...)

### Zusammenfassung
***
**Node.js** ist eine serverseitige Plattform in der Softwareentwicklung zum Betrieb von Netzwerkanwendungen.

**MongoDB** ist eine der marktführenden NoSQL-Datenbanken und ein sogenannter Document Store. MongoDB wurde für die heutige moderne IT-Landschaft konzipiert und erlaubt eine agilere Entwicklung und höhere Skalierbarkeit von Anwendungen.


![](https://raw.githubusercontent.com/TacoNaco47/M300_20_Prototyping/master/images/Software-Konfiguration_36x36.png "Software-Konfiguration") 05 - Software-Konfiguration
======

> [⇧ **Nach oben**](https://github.com/TacoNaco47/M300_20_Prototyping)

Das war es grundsätzlich schon mit der Praxis! Wir wollen jetzt in diesem Abschnitt noch ein wenig vertiefter in die Strukturen von Linux- bzw. Unix-Systemen einsteigen.

### /etc
***
Der Begriff **etc** kommt von `et cetera` ("alles übrige"). Oftmals ist dabei auch **editable text configuration** gemeint.

`/etc` enthält Konfigurations- und Informationsdateien des Basissystems. Zum Beispiel fstab, hosts, lsb-release oder blkid.tab. Die hier liegenden Konfigurationsdateien können dabei durch gleichnamige Konfigurationsdateien im Homeverzeichnis überlagert werden (z.B. bash -> .bashrc).

#### Die wichtigsten Unterverzeichnisse
* /etc/default:
    * Konfigurationsdateien für den Start von Diensten
* /etc/profile.d:
    * Scripte welche beim Einloggen eines User durchlaufen werden
* /etc/apache2:
    * Konfigurationsdateien für den Apache Webserver 
* /etc/mysql:
    * Konfigurationsdateien für die MySQL-Datenbank
* /etc/network:
    * Verzeichnisse und Konfigurationsdateien des Netzwerkes (z.B. interfaces)
* /etc/init.d (alt):
    * Start- und Stopskripte für Services 
* /etc/systemd (neu): 
    * Start- und Stopskripte für Services (dito)

> **Wichtig:** <br> Verzeichnisse mit der Endung **.d** beinhalten in der Regel mehrere Dateien welche durch eine vorgelagerte Datei inkludiert werden.

<br>
Um Konfigurationsdateien mit dem "Infrastruktur as a Code"-Ansatz (IaC) zu bearbeiten, eigen sich u.a. die nachfolgenden Tools:

### sed
***
sed (stream editor) ist ein nicht-interaktiver Texteditor für die Verwendung auf der Kommandozeile oder in Skripten. sed zählt zu den "Urgesteinen" in der Unix- / Linux-Welt und ist quasi in jeder Linux-Installation (auch Minimalinstallationen) enthalten.

#### Praxisbeispiel
1. Ersetzen von Text in einer Datei:
    ```Shell
        $ sed 's/Anton/Berta/g' Textdatei 
    ```
2. Entfernen von Zeilen, die mit # beginnen:
    ```Shell
        $ sed '/^#/d' Textdatei 
    ```
3. MySQL-Server Port öffnen:
    ```Shell
        $ sudo sed -i -e"s/^bind-address\s*=\s*127.0.0.1/bind-address = 0.0.0.0/" /etc/mysql/mysql.conf.d/mysqld.cnf

    ```

### cat
***
cat ist ursprünglich zum Zusammenfügen von Dateien gedacht (von concatenate = verketten, verknüpfen). Häufig wird cat aber als "Pager" zum Anzeigen von Dateiinhalten im Terminal eingesetzt (was letztendlich dem "Verketten" von Dateiinhalten mit dem Bildschirm entspricht).

#### Praxisbeispiel
* Schreiben einer Zwischendatei und Übergabe dieser an ein nachfolgendes Programm ($$ ist die Prozess-Nummer):  
    ```Shell
        $ cat 
        
        <<%EOF% >/tmp/$$ C
        
        REATE USER 'root'@'192.168.55.101' IDENTIFIED BY 'admin'; 
        GRANT ALL PRIVILEGES ON *.* TO 'root'@'192.168.55.101';
        FLUSH PRIVILEGES;
        
        %EOF% 
          
        mysql -uroot -pS3cr3tp4ssw0rd </tmp/$$
    ```

### cut
***
Der Befehl cut extrahiert spaltenweise Ausschnitte aus Textzeilen. So können beispielsweise aus einer Logdatei irrelevante Informationen entfernt oder CSV-Dateien bearbeitet werden.

#### Praxisbeispiel
1. Ausgabe nur der Benutzernamen aus der passwd-Datei:
    ```Shell
        $ cut -d: -f1 /etc/passwd
    ```
2. Ausgabe aller Prozess-Nummern:
    ```Shell
        $ ps huax | cut -c 9-14 
    ```
3. Durch Kombination von Befehlen, kann cut auf jedes Zeichen einer Datei zugreifen. Mit folgendem Befehl wird das 3. Wort der 6. Zeile (der Datei datei.txt)extrahiert. Es werden zunächst mit head die ersten 6 Zeilen ausgeschnitten, dann mit tail die letzte dieser verbleibenden Zeilen und schließlich mit cut deren drittes Wort:
    ```Shell
        $ cat datei.txt | head -6l | tail -1l | cut -d" " -f3

    ```

### in
***
ln steht für **link** und erzeugt eine Verknüpfung zu einer Datei oder einem Verzeichnis. Man kann danach auf eine Datei nicht nur über ihren ursprünglichen Namen bzw. Pfad, sondern auch über den Namen des Links zugreifen. 

Man unterscheidet zwischen Hardlinks und symbolischen (oder Soft-)links:
* **Hardlink**
<br> Ist einfach ein Verzeichniseintrag mit einem Namen, der auf eine Datei zeigt. Jede Datei hat immer mindestens einen Hardlink, denn ansonsten könnte man sie im Dateisystem nicht finden.
* **Symbolischer Link**
<br> Symbolische Verknüpfungen (oder "Softlinks") wurden geschaffen, um diese Unzulänglichkeit der Hardlinks zu umgehen. Sie funktionieren ähnlich wie die HTML-Links auf Webseiten. Ein symbolischer Link ist einfach eine kleine Datei bei der das 1-Bit gesetzt ist und die den Pfad des Zieles enthält.

#### Praxisbeispiel
1. Datei erstellen und dann mit einem Hardlink verknüpfen:
    ```Shell
        $ cat <<%EOF% >zahlen
        eins
        zwei
        drei
        %EOF%
        ln zahlen numbers

        $ ls -li
        total 8
        280065 -rw-rw-r-- 2 ubuntu ubuntu 15 Feb 11 12:45 numbers
        280065 -rw-rw-r-- 2 ubuntu ubuntu 15 Feb 11 12:45 zahlen

        $ cat numbers
        eins
        zwei
        drei

        $ cat zahlen
        eins
        zwei
        drei
    ```
2. Apache Modul mittels eines symbolischen Links freischalten:
    ```Shell
        $ cd /etc/apache2/conf-enabled/
        $ ln -s ../conf-available/adminer.conf . 
    ```


### Zusammenfassung
***
`/etc` enthält Konfigurations- und Informationsdateien des Basissystems. Beispiele: fstab, hosts, lsb-release, blkid.tab. Die hier liegenden Konfigurationsdateien können durch gleichnamige Konfigurationsdateien im Homeverzeichnis überlagert werden (Z.B. bash -> .bashrc).

sed, cat etc. sind Tools zur Bearbeitung von Konfigurationsdateien.


![](https://raw.githubusercontent.com/TacoNaco47/M300_20_Prototyping/master/images/Service-Konfiguration_36x36.png "Service-Konfiguration") 06 - Service-Konfiguration
======

> [⇧ **Nach oben**](https://github.com/TacoNaco47/M300_20_Prototyping)

Nach den vorhergehenden Theorie-Inputs geht es nun mehr und mehr in die Praxis. In diesem Abschnitt wird der Apache Webserver installiert und die MySQL-Datenbank eingerichtet.

### Dienst
***
Ein Dienst ist ein Programm, das beim Start des Rechners automatisch ausgeführt wird und im Hintergrund darauf wartet, seine Aufgabe zu erledigen.

Ein Dienst besitzt meist keine grafische Oberfläche und arbeitet ohne Interaktion des Benutzers.

Die bekanntesten Dienste sind sicherlich Web-, Mail- oder Datenbank-Server. Aber auch die Hardwareerkennung oder das automatische Einbinden (Mounten) von z.B. USB-Sticks wird durch Dienste erledigt.

Prinzipiell gibt es also zwei Arten von Diensten:
* **Interne** für beim Systemstart relevante bzw. hardwarenahe Aufgaben.
* Vom Benutzer **nachinstallierte** Dienste, zu denen in der Regel alle Serverdienste gehören.

In der Fachsprache werden generell Dienste traditionell auch als "Daemon" (im Sinne eines guten Geistes) bezeichnet. Der Buchstabe d findet sich daher häufig auch als letzter Buchstabe in der Programmbezeichnung wieder, so z.B. bei der Serverkomponente sshd von SSH.

#### Init-Systeme
Um das geordnete Starten und Beenden der Dienste kümmert sich ein Init-System. Dies ist normalerweise der erste gestartete Prozess und hat damit die Prozess-ID 1.

Ursprünglich übernahm diese Aufgabe auf Linux-Systemen **SysVinit**. Dieses startet die Dienste nacheinander in einer festgelegten Reihenfolge. Das macht den Startvorgang zuverlässig und leicht kontrollierbar, aber auch langsam. Mit dem parallelen Start von Diensten lässt sich der Startvorgang entscheidend beschleunigen, was insbesondere auf Desktop-Rechnern und Notebooks praktische Vorteile bietet.

Stoppen und Starten des Apache Web Servers, alte und neue Variante:
```Shell
    $ sudo service apache2 stop
    $ sudo service apache2 start

    $ sudo systemctl stop apache2
    $ sudo systemctl start apache2
```

Status aller Services abfragen:
```Shell
    $ sudo service --status-all

    $ sudo systemctl status
```

#### Start-/Stopp-Skripte
Je nach verwendetem System, findet man Start-/Stopp-Skripte an folgenden Orten:
* **Systemd**
    * /etc/systemd/system/ (benutzerdefinierte) oder /lib/systemd/system/ (systemeigene)
* **Upstart**
    * .conf-Dateien im Ordner /etc/init/
* **SysVinit**x
    * alle Dateien im Ordner /etc/init.d/


### systemd
***
systemd ist ein **System- und Sitzungs-Manager** (Init-System), der für die Verwaltung aller auf dem System laufenden Dienste über die gesamte Betriebszeit des Rechners, vom Startvorgang bis zum Herunterfahren, zuständig ist.

Prozesse werden dabei immer (soweit möglich) parallel gestartet, um den Bootvorgang möglichst kurz zu halten.

Systemd wird über Dateien mit einem INI-Datei-ähnlichen Format konfiguriert. In der Terminologie von systemd sind dies "Units".

Bei Ubuntu vorinstallierte Units sind im Ordner `/lib/systemd/system/` gespeichert. Falls sich jedoch eine Unit mit gleichem Namen im Verzeichnis `/etc/systemd/system/` befindet, so wird diese bevorzugt und jene unterhalb von `/lib` ignoriert. Damit hat man die Möglichkeit, eine Unit an eigene Gegebenheiten anzupassen, ohne dass man befürchten muss, dass sie bei einer Systemaktualisierung überschrieben wird.

### cron
***
Der **Cron-Daemon** ist ein Dienst, der automatisch Skripte und Programme zu vorgegebenen Zeiten starten kann.

Der auszuführende Befehl wird in einer Tabelle, der "crontab", gespeichert.

Es gibt eine systemweite Datei `/etc/crontab`, die nur mit Root-Rechten bearbeitet werden kann. Zusätzlich kann jeder Benutzer eine eigene Crontab erstellen, die man dann im Verzeichnis `/var/spool/cron/crontabs/` findet.

Diese Tabelle besteht aus sieben bzw. sechs Spalten. Die ersten fünf dienen der Zeitangabe (Minute, Stunde, Tag, Monat, Wochentage) für einen Cronjob, dann folgt (nur in der systemweiten Crontab) der Benutzername, unter dem der Befehl ausgeführt werden soll, und die letzte enthält den Befehl. Die einzelnen Spalten werden durch Leerzeichen oder Tabulatoren getrennt.

### anacron
***
Cron startet einzelne Cronjobs immer nur zu den eingetragenen Zeiten. Legt man also einen Cronjob an, der täglich um 18.00 Uhr ausgeführt wird, so wird der Job an diesem Tag nur ausgeführt, wenn der Rechner um Punkt 18 Uhr angeschaltet war. Für Jobs, die regelmäßig ausgeführt werden müssen, wobei der Rechner nicht durchgehend läuft, ist Cron daher nicht ideal. Wenn man Pech hat, wird z.B. eine wichtige Datensicherung ("backup") verpasst.

Als Ergänzung zu Cron bietet sich daher Anacron an. Anacron ist ein cron-ähnlicher Taskplaner, allerdings erfordert er nicht das kontinuierliche Laufen des Systems. Er kann zum Ausführen von täglich, wöchentlich oder monatlich anstehenden Aufträgen verwendet werden, die normalerweise von cron ausgeführt werden.

Anstatt einer Crontab muss nur das auszuführende Skript oder Programm in das entsprechende Verzeichnis des Anacron-Dienstes kopiert oder verlinkt werden.

* `/etc/cron.hourly/` - stündlich auszuführen
* `/etc/cron.daily/` - täglich auszuführen
* `/etc/cron.weekly/` - wöchentlich auszuführen
* `/etc/cron.monthly/` - monatlich auszuführen

Die Namen der Verzeichnisse sprechen für sich. Skripte, die dort abgelegt sind, werden auch durch den Anacron-Dienst ausgeführt. Am besten schaut man sich kurz die Skripte in diesen Verzeichnissen an, bevor man selber dort etwas ablegt.

### Zusammenfassung
***
Ein Dienst ist ein Programm, das beim Start des Rechners automatisch ausgeführt wird und im Hintergrund darauf wartet, seine Aufgabe zu erledigen.

Prinzipiell gibt es zwei Arten von Diensten:
* **Interne** für beim Systemstart relevante bzw. hardwarenahe Aufgaben.
* Vom Benutzer **nachinstallierte** Dienste, zu denen in der Regel alle Serverdienste gehören.

systemd ist ein System- und Sitzungs-Manager (Init-System), der für die Verwaltung aller auf dem System laufenden Dienste über die gesamte Betriebszeit des Rechners, vom Startvorgang bis zum Herunterfahren, zuständig ist.

Der Cron-Daemon ist ein Dienst, der automatisch Skripte und Programme zu vorgegebenen Zeiten starten kann.

Anacron ist ein cron-ähnlicher Taskplaner, allerdings erfordert er nicht das kontinuierliche Laufen des Systems. Er kann zum Ausführen von täglich, wöchentlich oder monatlich (und ab Ubuntu 14.04 auch stündlich) anstehenden Aufträgen verwendet werden, die normalerweise von cron ausgeführt werden.

![](https://raw.githubusercontent.com/TacoNaco47/M300_20_Prototyping/master/images/Reflexion_36x36.png "Reflexion") 07 - Reflexion
======

> [⇧ **Nach oben**](https://github.com/TacoNaco47/M300_20_Prototyping)

Die Erarbeitung dieses Abschnittes hat mir sehr viel Freude bereitet und ich konnte einiges lernen. Jedoch waren die Aufgabenstellungen teilweise etwas unkalr oder sogar falsch formuliert, wodurch ich viel Geduld und Zeit aufwenden musste. Mit meinem Ergebnis bin ich jedoch sehr zufrieden und ich hoffe, dass durch diese Dokumentation bzw. durch dieses Repository die Aufgaben verständlicher wirken.