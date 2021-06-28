Top-Level-Projekt für die Weiterentwicklung von [Jameica](https://github.com/willuhn/jameica) und dessen Plugins

Dieses Projekt bietet einen zentralen Einstiegspunkt für die Programmierung.
Somit kann direkt gestartet werden, ohne jedes Projekt einzeln suchen, herunterladen und in der IDE konfigurieren zu müssen.

Schnellstart
============
Dieses Projekt nutzt _Git Submodule_ zum Einbinden der unterschiedlichen Quellen.
So können die einzelnen Teilprojekte individuell gepflegt werden.
Updates werden so automatisch bei allen Nutzern sichtbar.
Hier konzentrieren wir uns auf die Grundlagen im Umgang mit diesem Repository.
Mehr Details, Hintergründe und Möglichkeiten von _Git Submodule_ sind im [Git-Buch](https://git-scm.com/book/de/v2/Git-Tools-Submodule) beschrieben.

Code inital besorgen
--------------------
1. Repository herunterladen

   Zuerst benötigen wir eine lokale Kopie von diesem Projekt.
   Nutze dazu regulär `git clone <URL>`, um nur dieses Repository herunterzuladen.
   Nun sollten die Unterverzeichnisse existieren, die auf andere Repositories verweisen, jedoch sind diese leer.

   > Man könnte auch `git clone --recursive-submodules <URL>` nutzen, aber dann sind Schritt 2 und 3 etwas komplizierter.

2. eigenen Fork einbinden

   Sicher möchtest Du dieses Repository nicht nur nutzen, um den anderen Entwicklern bei der Arbeit zuzuschauen... :wink:

   Da Du aber wahrscheinlich keinen Schreibzugriff auf die originalen Quellen hast, kannst Du Deine Änderungen nicht einfach so wieder hochlanden.
   Deswegen, falls noch nicht geschehen, solltest Du spätestes jetzt einen _Fork_ von dem Teilprojekt erstellen, wo Du Änderungen einbringen möchtest.
   Um nun Dein Fork-Repository zu verwenden, muss die URL (und ggf. der Branch) für das spezifische Teilprojekt bei Dir lokal angepasst werden.

   Öffne dazu die Datei `.gitmodules` und ändere die Werte entsprechend.

3. Dateien holen

   Um den Inhalt der referenzierten Projekte zu holen, führe `git submodule update --init --recursive --remote` aus.
   Jetzt wird automatisch jedes Subprojekt heruntergeladen und der letzte Stand aus dem hinterlegtem Branch ausgecheckt.
   
   :warning: Manchmal scheint das nicht sofort zu klappen.
   Ich habe die Ursache noch nicht herausgefunden.
   Mein "Trick" ist es, das Folgende mehrfach zu wiederholen und dabei stets den Erfolg zu überprüfen:
   ```shell
   # Schritt 1: alle Submodule aushängen
   git submodule deinit --all
   # sollte nichts anzeigen
   git config --list | grep submodule
   # Submodule neu initialisieren und neuesten Stand abrufen
   git submodule update --init --recursive --remote --force --checkout
   # hier sollten nun die angepassten URLs stehen
   git config --list | grep submodule
   # Konfiguration synchronisieren
   git submodule sync --recursive
   # spätestens jetzt sollte alles passen, sonst nochmal von vorn
   git config --list | grep submodule
   #
   # Schritt 2: Falls OK, dann nun den korrekten Branch auschecken (wegen "detached head") und neuesten Stand holen
   git submodule foreach "git checkout master; git pull"
   ```


Auf dem neuesten Stand bleiben
------------------------------
Nutze nicht `git pull` sondern `git pull --recurse-submodules`, um die neuesten Updates zu holen.
Der Befehl holt nun die Änderungen aus dem Projekt und aus allen Submodulen, er aktualisiert jedoch die Submodule nicht!
Dazu muss ein zusätzliches `git submodule update --recursive --remote` ausgeführt werden.

:warning: Mit `--remote` wird der letzte Commit im Branch verwendet, ohne stattdessen der Commit, der explizit in diesem Reporsitory hinterlegt wurde:
```shell
$ git pull --recurse-submodules
From https://github.com/octocat/MainProject
   fb9093c..0a24cfc  master     -> origin/master
Fetching submodule MySubModule
From https://github.com/octocat/MySubModule
   c3f01dc..c87d55d  stable     -> origin/stable
Updating fb9093c..0a24cfc
$ git submodule update --recursive
Submodule path 'sub/MySubModule': checked out '71045ebb1a2144a562bda72b871fc7d58c3f01dc'
$ git submodule update --recursive --remote
Submodule path 'sub/MySubModule': checked out '91cbe3b93cfb2980b784ad3a00e622f7ac87d55d'
```
