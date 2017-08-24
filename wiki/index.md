DentaDesk File Watcher
======================

Installation
------------

Laden Sie die aktuelleste Version des DentaDesk File Watcher auf Ihren PC: [dentadesk-filewatcher-2.3.0.msi](https://www.dentadesk.com/downloads/dentadesk-filewatcher-2.3.0.msi)

Führen Sie den *.msi-Installer aus und installieren Sie das Programm in einen beliebigen Ordner.


Konfiguration
-------------

Die "config.ini" finden Sie im Pfad "%APPDATA%\DentaDesk\FileWatcher". Sie sieht in etwa so aus:

	[store]
	projects=Z:\download\projects
	temp=Z:\download\temp
	salt=aj3459kjvbnblaz3hgdgx83dsec6341
	project=%transaction.transactionnum%_%account.customernum%_%account.shortname%_%materialscolors%_%project.name%\%file.material%-%file.color%
	file=%transaction.transactionnum%_%transaction.ID%_%file.name%
	lastmodified=1502453811

	[cam]
	projects=Z:\cam\projects\
	cutoff=21
	file=%transaction.transactionnum%_%transaction.ID%_%file.name%

	[log]
	logfile=Z:\filewatcher\log.txt
	loglevel=ALL

	[app]
	autostart=0
	interval=90
	projects=1
	charges=1

	[urls]
	api=https://app.dentadesk.com/PLATFORM/api/
	zeyos=https://cloud.zeyos.com/PLATFORM/remotecall/net.zeyon.dentadesk-1000.api/


Zur Erläuterung:

* Bei "store.projects" tragen Sie den Ordner ein, wo die ganzen Projekte landen sollen, die heruntergeladen werden.
* "store.temp" wird im Moment nicht genutzt und dient zur Ablage von temporären Dateien, die nur während des Downloads oder des Verarbeitens genutzt und dann gelöscht werden.
* "store.salt" ist ein einzigartiger Schlüssel, der für jede Plattform eindeutig ist. Sie erhalten das Salt von uns und tragen es hier ein.
* "store.project" ist der Name des Projektordners samt Platzhaltern. Mehr zu den Platzhaltern im Anschluss.
* "store.file" ist der Name der STL-Datei im Projektordner, ebenso mit der Möglichkeit für Platzhalter. Mehr zu den Platzhaltern im Anschluss.
* In "store.lastmodified" wird der UNIX-Zeitstempel (Sekunden seit 1.1.1970) gespeichert. Es werden nur Projekte heruntergeladen, die nach dem gespeicherten Zeitstempel geändert wurden. Der DentaDesk FileWatcher aktualisiert den Stempel auf den aktuellen Zeitpunkt, wenn bei einem Durchlauf alle Projekte fehlerfrei heruntergeladen wurden.

* Bei "cam.projects" tragen Sie den Ordner ein, in dem alle CAM-Projekte liegen.
* "cam.cutoff" schränkt das Durchsuchen aller CAM-Projektordner auf diejenigen ein, die in den letzten x Tagen verändert wurden.
* "cam.file" funktioniert gleich wie "store.file" und sollte gleich lauten.

* "log.logfile" ist der Pfad der Log-Datei. Im Normalfall muss er nicht angepasst werden.
* "log.loglevel" beschreibt, welche Log-Nachrichten in die Log-Datei geschrieben werden. Mögliche Werte sind: ALL, INFO, WARNING, ERROR, NONE

* Wenn "app.autostart" auf 1 gestellt wird, beginnen die periodischen Durchläufe sofort nach dem Start der Anwendung. Auf 0 muss manuell gestartet werden.
* "app.interval" ist die Zeit zwischen den Durchläufen in Sekunden.
* "app.projects" ist ein Schalter, der bestimmt, ob CAD-Projekte abgerufen werden (1=ja, 0=nein)
* "app.charges" ist ein Schalter, der bestimmt, ob Chargen ermittelt werden sollen (1=ja, 0=nein)

* "urls.api" ist die DentaDesk-API-URL. Hier muss nur "PLATFORM" durch den Namen der eigenen DentaDesk-Instanz ersetzt werden.
* "urls.api" ist die ZeyOS-API-URL. Hier muss nur "PLATFORM" durch den Namen der eigenen ZeyOS-Plattform ersetzt werden.


Chargennummern-Erfassung
------------------------

Voraussetzung, dass die Chargennummern-Weitergabe funktioniert, ist die obige Namenskonvention für die STL-Datei, d.h. der Dateiname muss im CAM-Verzeichnis genauso lauten wie im Projektordner. Über den Dateinamen in der Project.Info werden dann die STL-Dateien mit der Charge und dem Projekt verknüpft. Diejenigen Dateien, die verknüpft werden konnten und noch wegen fehlender Chargennummer periodisch gesucht werden, übermittelt der FileWatcher dann an die ZeyOS-Plattform. Dort werden sie (im Moment unsichtbar) in der Transaktion hinterlegt.


Platzhalter für Projekt- und Dateinamen
---------------------------------------

Der Pfad, in dem Projekte und deren Dateien gespeichert werden, kann komplett angepasst und mit projekt- und datei-bezogenen Platzhaltern konfiguriert werden. Platzhalter werden in der Form "%kategorie.eigenschaft%" in den Pfad geschrieben, also das Prozentzeichen als Begrenzung genutzt. Es stehen folgende Möglichkeiten (Auszug der sinnvollen Platzhalter) zur Verfügung:

* "project"
	- "ID": Die systeminterne ID des Projekts (einzigartig, dient zur Referenz)
	- "creationdate": UNIX-Zeitstempel des Erstelldatums des Projekts
	- "lastmodified": UNIX-Zeitstempel des letzten Änderungsdatums des Projekts
	- "name": Name des Projekts
	- "description": Beschreibungstext
	- "type": Typ des Projekts (z.B. "stl")
	
* "account"
	- "ID": Die systeminterne ID des Kunden (einzigartig, dient zur Referenz)
	- "customernum": Kundennummer
	- "lastname": Nachname bzw. Firmenname des Kunden
	- "firstname": Vorname des Kunden (bei Firmen leer)
	- "email": Mail-Adresse
	- "shortname": Kurzname des Kunden (nützlich bei überlangen Firmennamen)

* "extdata": Alle fürs Projekt konfigurierten Erweiterungsfelder
	
* "duedate"
	- "name": Liefer-Modus (z.B. "Standard")
	- "days": Tage ab Bestelldatum, die zur Bearbeitung benötigt werden
	- "threshold": Uhrzeit, bis zu der geliefert werden muss (z.B. "13:00")
	- "saturday": Ob Samstage beachtet werden (1=ja, 0=nein)
	- "sunday": Ob Sonntage beachtet werden (1=ja, 0=nein)
	- "holidays": Ob Feiertage beachtet werden (1=ja, 0=nein)
	- "duedate": Vorberechnetes Fälligkeitsdatum
	
* "materialscolors": Kombination aus Material und Farbe der Projektdateien (z.B. Z-A3,5_PMMA-D2)

* "transaction":
    - "ID": Die systeminterne ID der Bestellung
    - "transactionnum": Die Transaktionsnummer (Bestellnr./Auftragsnr.)
    - "date": UNIX-Zeitstempel des Leistungsdatums der Bestellung

* "file"
	- "name": Dateiname
	- "fileType": Datei-Typ/Erweiterung (im Moment nicht nutzbar, da immer *.stl)
	- "fileHash": Hash-Wert bzw. Prüfsumme der Datei
	- "material": Material des STLs
	- "color": Farbe des STLs

Bei den obigen Config-Beispielwerten...

	[store]
	projects=Z:\download\projects
	project=%transaction.transactionnum%_%account.customernum%_%account.shortname%_%materialscolors%_%project.name%\%file.material%-%file.color%
	file=%transaction.transactionnum%_%transaction.ID%_%file.name%

... und folgenden Daten...

	* Transaktionsnummer: "A.1707.0131"
	* Transaktions-ID: "280"
	* Kundennummer: "1896"
	* Kurzname: "ZeyOS"
	* Materiale & Farben:
		- Datei 1: "Z" (Zirkon) mit Farbe "A3,5"
		- Datei 2: "PMMA" mit Farbe "D2"
	* Projektname: "Happe, Heiner"
	* Dateiname 1: "Zahn_45__Offset_Inlay__-_Stift 1.stl"

... ergibt sich also folgender Pfad für Datei 1:

	"Z:\download\projects\A.1707.0131_1896_ZeyOS_Z-A3,5_PMMA-D2_Happe, Heiner\Z-A3,5\A.1707.0131_280_Zahn_45__Offset_Inlay__-_Stift 1.stl" 
