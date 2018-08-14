# Dokumentation File-Comparison
### HOWTO Benutzung Python Tool für Auswertung von größeren Datenmengen

----------------------
[Dokumentation auch in Confluence zu finden](http://jazz.agilion.local:8090/display/System/%5BDokumentation%5D+LOC-Data+comparison+Tool)
#### Benötigte Dateien
* [In diesem Ordner: ](https://svn.agilion.lan/svn/tools/LOCDataCompare/)
  * 2018-07-30_V2.0.exe
    * Kompilierter Code, nutzbar auf Windows
  * 2018-07-30_V2.0.py
    * SourceCode, geschrieben in Python 3.6
  * file_locations.txt
      * File-Locations-Datei
      * Hier werden alle zu analysierenden Dateien eingefügt
          * Formatierung beachten!
      * Beispiel-Daten liegen bei (auch in file_locations.txt zu erkennen)
          * Q:\ble\von the\Test2
* Extern benötigt (nicht nativ in diesem Ordner vorhanden)
  * Daten zum Auswerten
    * Formatierung der Daten laut "**Features / Funktionen**"
  * PowerPivot zum Anzeigen, wo Probleme genau sind und wie stark sie sich auswirken  
    * [Tutorial zu PowerPivot für diesen Kontext](http://jazz.agilion.local:8090/display/System/PowerPivot)
-------------------------
#### Requirements für Standardmässige Nutzung (mit \*.exe) [Empfohlen]
  * Windows Betriebssystem
  * Compilierter Python File, zu finden in:
      * **https://svn.agilion.lan/svn/tools/LOCDataCompare/2018_7_30_V2.0.exe**
      * kann auch an anderen Ort kopiert werden
  * Datei, um Ordner-Pfad anzugeben für zu vergleichende Quelldateien
    * Beispiel in **https://svn.agilion.lan/svn/tools/LOCDataCompare/file_locations.txt**
    * Ort nicht besonders relevant, jedoch muss Pfad bekannt sein  
      * einfach im Explorer den Pfad kopieren und Name der Datei anhängen
      * oder im Ordner mit der Exe haben
    * Format der Datei (Pro Zeile):
-------------------------------------
#### _**Anwendung**_:


0. Quelldateien-Ordner anpassen, wenn nötig
1. benötigte .exe ausführen
2. Anweisungen befolgen

       Tag=File_Location#Kommentar


 **Für 0.**: Beispiel, wie es derzeit in **file_locations.txt** liegt

       master=Q:/ble/von the/Test2/Out Produktivserver Muenchen/BMW_M_TMO3_2018-07-13_071237trackpos.csv
       1=Q:/ble/von the/Test2/Out WIN10G13 (RC single Loc)/2018-07-19_143621trackpos.csv
       1=Q:/ble/von the/Test2/Out WIN10G13 (RC single Loc)/2018-07-19_145823trackpos.csv
       2=Q:/ble/von the/Test2/Out WIN10G14 (RC dual Loc)/Loc1/2018-07-19_145120trackpos.csv
       2=Q:/ble/von the/Test2/Out WIN10G14 (RC dual Loc)/Loc2/2018-07-19_145155trackpos.csv
       3=Q:/ble/von the/Test2/Out WIN10G15 (2.6.5 single Loc)/2018-07-19_143647trackpos.csv
       3=Q:/ble/von the/Test2/Out WIN10G15 (2.6.5 single Loc)/2018-07-19_145833trackpos.csv
 **Alles nach einem "#" wird als Kommentar betrachtet**  

* Bei Anfrage nach der Datei wird nach dem Tag gefragt
  * Einschänkungen für Files/Tags/ETC:
    * Tag **DARF NICHT** "=" beinhalten
    * File **DARF NICHT** "=" beinhalten
    * Tag **DARF NICHT** "#" beinhalten
    * File **DARF NICHT** "#" beinhalten
    * Tag **DARF NICHT** Umlaute beinhalten
    * File **DARF NICHT** Umlaute beinhalten
  * BSP: Master,Slave,1,2,3,hier_einen_beliebigen_tag_egal_wie_lang
  * Die Eingabe wird in der Hinsicht überprüft, ob die Eingabe in einem Tag vorkommt
    * "m" in "master"      ==> true
    * "master" in "master" ==> true
    * "1" in "master"      ==> false
  * Falls es Tags mit Gemeinsamkeiten gibt, Vorsicht!:
    * BSP *Tag1*="RC LOC1"; *Tag2*="RC LOC2"; *Tag3*="Single LOC1"; *Tag4*="RC LOC2.4"
    * bei Eingabe == "RC" werden alle Files mit Tag1, Tag2 und Tag4 betrachtet
    * bei Eingabe == "1" werden alle Files mit Tag1 und Tag3 betrachtet
    * bei Eingabe == "rc loc1" werden nur Files mit Tag1 betrachtet
    * In dieser Konstellation von Tags kann man Tag2 nicht einzeln ansprechen
      * (weil Tag4 den ganzen String von Tag2 beinhält)









-------------------------
#### Features / Funktionen:
* Vergleich von 2 Clustern an Quelldateien
    * minimale Anzahl pro Cluster jeweils 1
    * maximale Anzahl pro Cluster undefiniert (Stand 25.7.18)
* Quelldateien sind [\*.csv]
    * Dateien werden pro Zeile eingelesen
    * Format pro Zeile: (relevante werte sind Fett) "**0**;1;**2**;**3**;4;5;**6**;7;**8**;**9**;**10**;11;12" (ohne Anführungsstriche)
      * **0** = Timestamp
        (BSP: "2018-07-13T05:12:37.667Z")
      * 1 = irrelevant (für dieses Tool)
        (BSP: "640298734")
      * **2** = TAG-ID
        (BSP: "36255")
      * **3** = nur "48" ist relevant
        (BSP: "48")
      * 4 = irrelevant (für dieses Tool)
        (BSP: "20")
      * 5 = irrelevant (für dieses Tool)
        (BSP: "5")
      * **6** = correct? (daten bei "1" als correct erkannt, bei "0" nicht)
        (BSP: "1")
      * 7 = irrelevant (für dieses Tool)
        (BSP: "0")
      * **8** = X-koordinate
        (BSP: "42246")
      * **9** = Y-koordinate
        (BSP: "40933")
      * **10** = Z-koordinate
        (BSP: "51625")
      * 11 = irrelevant (für dieses Tool)
        (BSP: "0")
      * 12 = Ausrichtung (irrelevant)
        (BSP: "Heading=270")
* Vergleich nach mehreren Kriterien
  * Vergleich von Cluster1(Master) mit Cluster2(Slave)
  * Es werden nur Timestamps berücksichtigt, die vom Master vorgegeben werden
    * korrekte Zeilen in Cluster1 geben min-/max-Zeiten vor
  * Vergleich, nur wenn:
    * Wert an der Stelle 3 = "48"
    * Wert an der Stelle 6 = "1"
    * Koordinaten ungleich 0
    * Zeit innerhalb der durch Master vorgegebenen Zeit
  * mehrere Errorcodes
    * _Zeile bedeutet hier eine bestimmte Tag-ID **und** ein bestimmter TimeStamp_
      * Es werden Keine Rundungen berücksichtigt, also zeit T != T+(1/10000)
    * 1 = Zeile nur in Cluster1 vorhanden
    * 2 = Zeile nur in Cluster2 vorhanden
    * 3 = Zeile bei beiden Clustern vorhanden & unterschiedliche Koordinaten
    * 4 = Zeile bei beiden Clustern vorhanden & gleiche Koordinaten <- erwünschtes Ergebnis
* Automatische Erkennung, ob Multi-LOC System
  * nur, wenn in Ordner-Struktur kein "Single" vorhanden ist


***Beispiel-Log aus Python-Shell*** [Stand: 2018-7-30] [veraltet]

    Bitte geben Sie den Pfad zur Quelldatei an:
    (Nichts eingeben fuer Q:\ble\CompareCode-Python\file_locations.txt)
    Q:\ble\CompareCode-Python\file_locations.txt
    master  =       Q:/ble/von the/Test2/Out Produktivserver Muenchen/BMW_M_TMO3_2018-07-13_071237trackpos.csv#hier ist ein comment
    1       =       Q:/ble/von the/Test2/Out WIN10G13 (RC single Loc)/2018-07-19_143621trackpos.csv
    1       =       Q:/ble/von the/Test2/Out WIN10G13 (RC single Loc)/2018-07-19_145823trackpos.csv
    2       =       Q:/ble/von the/Test2/Out WIN10G14 (RC dual Loc)/Loc1/2018-07-19_145120trackpos.csv
    2       =       Q:/ble/von the/Test2/Out WIN10G14 (RC dual Loc)/Loc2/2018-07-19_145155trackpos.csv
    3       =       Q:/ble/von the/Test2/Out WIN10G15 (2.6.5 single Loc)/2018-07-19_143647trackpos.csv
    3       =       Q:/ble/von the/Test2/Out WIN10G15 (2.6.5 single Loc)/2018-07-19_145833trackpos.csv

    possible tags:['1', '2', '3', 'master']

    welche Datei soll als Master betrachted werden? m

    welche Datei soll als Slave betrachted werden? 2
    +-----------------------+
    | Zeitmessung gestartet |
    +-----------------------+
    ------------------------------------------
    +-------------+
    | Init Master |
    +-------------+
    --------master(tag: m) file:1
    --------Q:/ble/von the/Test2/Out Produktivserver Muenchen/BMW_M_TMO3_2018-07-13_071237trackpos.csv
     der Zeitraum der Messungen betraegt 0:07:46.482000(HH:MM:Sec)
         (2018-07-13 05:12:37.667000 bis 2018-07-13 05:20:24.149000)
    ------------------------------------------
    +------------+
    | Init Slave |
    +------------+
    --------slave(tag: 2) file:1
    --------Q:/ble/von the/Test2/Out WIN10G14 (RC dual Loc)/Loc1/2018-07-19_145120trackpos.csv
          67960 sind rausgefallen wegen TimeConstraints durch Master-File

    --------slave(tag: 2) file:2
    --------Q:/ble/von the/Test2/Out WIN10G14 (RC dual Loc)/Loc2/2018-07-19_145155trackpos.csv
          142240 sind rausgefallen wegen TimeConstraints durch Master-File

    ------------------------------------------
    Keine TAGs werden im Multi-LOC System von mehr als einem Server verarbeitet, Erfolg!
    ------------------------------------------
    +-------------------+
    | Analyse Duplikate |
    +-------------------+
        Anzahl gleicher Eintraege: 367485
    <<-- Finished - ->>
    ------------------------------------------
    +----------------------+
    | Analyse Unterschiede |
    +----------------------+
        Eintraege nur in Master: 511
        Eintraege nur in Slave:  19
                Summe von Exklusiven Datensätzen: 530
        Eintraege mit unterschiedlichen Koordinaten:  337
    <<-- Finished - ->>
    ------------------------------------------
    +-------------------------------+
    | Pfade zu untersuchten Dateien |
    +-------------------------------+
    Master-Dateien:
        Q:\ble\von the\Test2\Out Produktivserver Muenchen\BMW_M_TMO3_2018-07-13_071237trackpos.cs
    Slave-Dateien:
        Q:\ble\von the\Test2\Out WIN10G14 (RC dual Loc)\Loc1\2018-07-19_145120trackpos.cs
        Q:\ble\von the\Test2\Out WIN10G14 (RC dual Loc)\Loc2\2018-07-19_145155trackpos.cs
    Vergleichs-Datei gespeichert in:
        Q:\ble\von the\Test2\Out WIN10G14 (RC dual Loc)\Loc1\total_2018-07-30_11-06-20.csv
    ------------------------------------------
    Dauer des tests(ohne Betrachtung der Eingaben): 0:00:19.792142

    [Enter] zum beenden
