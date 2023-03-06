---
layout: page
title: Kapitel 5 - Installation, Verteilung- und Updatestrategien_
permalink: /kapitel5/
nav_order: 5
---

Autoren: Michael Biber, Michael Keller, Paul Peitz

**Kapitelstatus:** "Fertig zum Review" (initial von Sebastian gesetzt, gerne ändern)

*Michael Biber (16.09.): Erster Stand*

*Sebastian F (19.09.): Ein paar Kommentare ergänzt, sprachlich kleinere Anpassungen*

Review von Sebastian am 11/12.01.23 auf alle Kapitel

*(zum Beispiel "Nicht gestartet" \| "In Arbeit" \| "Fertig zum Review" \| "Review durchgeführt von x am y" \| "Review eingearbeitet am y" \| "Finaler Zustand" \|*

*\<was aus eurer Sicht sinnvoll erscheint, evtl. Unterkapitel nennen, falls ihr die Arbeit verteilt\> )*

# Kapitel 5 - Installation, Verteilungs- und Updatestrategien

Die ADT basieren auf dem quelloffenen und vor allem in anderen Programmiersprachen sehr bekannten Eclipse-Framework. Für die Installation und Verteilung hat das den Vorteil, dass das Rad nicht neu erfunden werden muss. Je nach Unternehmensgröße und Heterogenität der IT-Landschaft eines Unternehmens ist es sogar möglich, dass Eclipse-basierte Entwicklungsumgebungen schon im Unternehmen eingesetzt und verteilt werden. In diesem Fall empfiehlt es sich, die bestehende Infrastruktur weiter zu verwenden. Auch kann dies etwaige Aufwände der ein oder anderen Lösung reduzieren.

## Abgrenzungen

### Installation Guide der SAP

Die SAP veröffentlicht einen eigenen Installationsleitfaden ([[Link]{.underline}](https://help.sap.com/doc/2e9cf4a457d84c7a81f33d8c3fdd9694/Cloud/en-US/inst_guide_abap_development_tools.pdf)). Dieser führt neben dem hier ebenfalls aufgeführten manuellen Installationsweg auch weitere Varianten für abgeriegelte Umgebungen mit sehr begrenztem Internetzugriff auf. Da es sich hierbei um Lösungen für spezielle Situationen geht, wird auf diese in den folgenden Abschnitten nicht mehr eingegangen.

#### Andere Werkzeuge mit identischem Installationsweg

SAP veröffentlicht auf Basis von Eclipse auch noch weitere Werkzeuge, wie beispielsweise die BW Tools (verpflichtend ab BW/4HANA 1.0) und HANA Development Tools. Da sich dieser Leitfaden aber auf ABAP-Entwicklung mit ADT konzentriert, wird nicht weiter auf die genannten anderen Werkzeuge eingegangen. Die Erkenntnisse sind jedoch größtenteils übertragbar.

## Vorbereitungen

Für jede ADT-Installation (teilweise auch Eclipse ohne ADT) gibt es gewisse Voraussetzungen. Diese sind unabhängig von der Installation-/Verteilungsstrategie und werden detailliert im o. g. ADT Installation Guide beschrieben.

### Java Development Kit und Java Runtime Environment

Wird mit der Installation lediglich ABAP entwickelt (und insbesondere kein Java, d. h. auch keine Plugin-Entwicklung zur Ergänzung der ADT, siehe Abschnitt [ToDo]{.mark} ), so ist auch kein Java Development Kit (JDK) notwendig. Ein Java Runtime Environment (JRE) reicht aus. Wird der offizielle Installer von Eclipse als Basis verwendet (siehe Abschnitt [ToDo]{.mark}), kommt dies automatisch mit.

Zu beachten ist dabei, dass Eclipse mittlerweile standardmäßig als 64 Bit-Anwendung installiert wird (oft auch als *x64* oder *x86_64* bezeichnet). Da das JRE dieselbe Architektur verwenden muss wie die Eclipse-Installation, wird auch eine 64 Bit JRE installiert.

### Backend

Neben der lokalen ADT-Installation muss im Falle der Entwicklung auf einem on Premise System auch selbiges für die Verbindung mit ADT vorbereitet werden. Diese Schritte werden ebenfalls im offiziellen [[SAP ADT Configuration Guide der SAP]{.underline}](http://help.sap.com/download/netweaver/adt/SAP_ADT_Configuration_Guide_Backend_en.pdf) beschrieben. Zwei essentielle Schritte werden darin jedoch gerne übersehen, weshalb hier noch einmal explizit darauf hingewiesen werden soll.

#### Web Services

Für die Kommunikation nutzen die ADT spezielle Web Services auf dem Backend, die mit den unterstützten Basis-Releases ausgeliefert werden. Die Definition der Web Services und die darin eingetragenen Handler-Klassen sind ein Grund, warum der Funktionsumfang der ADT zwischen den Basis-Releases unterschiedlich ist. Diese Web Services sind standardmäßig inaktiv und müssen zunächst aktiviert werden. Die aktuelle Liste kann dem oben verlinkten Configuration Guide der SAP entnommen werden.

Zum Zeitpunkt dieses Dokumentes sind dies die folgenden Services:

-   ABAP Docu (notwendig)

    -   *default_host → sap → public → bc → abap → docu*

    -   *default_host → sap → bc → abap → docu*

-   Fehlertexte und Element Info (notwendig)

    -   *default_host → sap → public → bc → abap → toolsdocu*

    -   *default_host → sap → bc → abap → toolsdocu*

-   Teilen von HTTP-Links (optional)

    -   *default_host → sap → bc → adt*

-   Web Dynpro (nur notwendig für WD-Entwickler)

    -   *default_host → sap → bc → webdynpro → sap → wdy_aie_vd_preview*

#### Berechtigungen

Um die genannten Web Services nutzen zu dürfen, müssen die User berechtigt werden. Zusätzlich werden noch RFC-Bausteine und Transaktionscodes benötigt.

SAP liefert hierzu zwei Rollen als Vorlage aus:

1.  *SAP_BC_DWB_ABAPDEVELOPER* → Entwicklerrolle mit allen Features

2.  *SAP_BC_DWB_WBDISPLAY* → Anzeigeberechtigungen für alle Features

Details bezüglich der darin verbauten Berechtigungen und deren Zweck können dem [[SAP ADT Configuration Guide der SAP]{.underline}](http://help.sap.com/download/netweaver/adt/SAP_ADT_Configuration_Guide_Backend_en.pdf) entnommen werden.

### SAP GUI Installation

Soll auf einem Backend System ein SAP GUI Transaktionsstart möglich sein (nicht verfügbar in SAP Public Cloud und SAP BTP ABAP Environment / Steampunk), so wird hierfür eine lokale SAP GUI Installation benötigt. Die ADT liefern diese nicht mit.

### Visual Studio Redistributable

Unter Windows wird zudem das Visual Studio 2013 (VC++ 12.0) Redistributable Paket in exakt dieser Version benötigt. Oftmals ist diese Abhängigkeit schon aufgrund anderer bereits auf dem Zielgerät installierter Software vorhanden.

## Technischer Aufbau einer Eclipse-Installation

Wie bereits in Abschnitt [ToDo]{.mark} detailliert erläutert, besteht eine Eclipse-Installation aus den folgenden Komponenten:

-   Installationsordner (reine Softwarepakete und eclipse.exe)

-   User Settings oder Configuration Area (Plugins, Teile der Konfiguration)

-   Workspace (benutzerspezifischer Teil: Einstellungen, Ansichten, Systemverbindungen, ...)

Die Installation von Eclipse legt den Installationsordner und User Settings an. Mittels fortgeschrittenerer Techniken können auch schon Teile des Workspaces vorbelegt werden.

Aufgrund diverser Probleme empfiehlt es sich, alle Komponenten in einen mit normalen Benutzerrechten beschreibbaren Ordner abzulegen (also **nicht** C:\\Program Files\\).

Beispielsweise könnte eine Verzeichnisstruktur für die Eclipse Installation wie folgt aussehen:

C:\\ADT\\ - Gesamtverzeichnis für ADT

C:\\ADT\\IDE Verzeichnis für Ablage der verschiedenen Eclipse Versionen

C:\\ADT\\IDE\\2022-12 entpackte Dateien der Eclipse Version 2022-12

C:\\ADT\\IDE\\2023-03 entpackte Dateien der Eclipse Version 2023-03

C:\\ADT\\WS Verzeichnis zur Ablage der verschiedenen Workspaces

C:\\ADT\\WS\\2022-12 Verzeichnis für Workspaces der Version 2022-12

...

Dieses Beispiel liegt die Annahme zu Grunde, dass die Eclipse Installation über das Entpacken der zip-files erfolgt und die Versionen parallel verwendet werden sollen.

Da die Workspaces mit aufsteigenden Versionen in die jeweils neue Version konvertiert werden, sollten die Workspaces pro Version kopiert werden, um bei Bedarf die älteren Versionen weiterhin verwenden zu können.

Inwiefern Workspaces sinnvoll eingesetzt werden, wird in kapitel xy detaillierter erläutert.

## Plug-ins

Plug-ins wie die ADT können über die Angabe der Update Site im Dialog Help → Install New Software zu einer bestehenden Eclipse Plattform hinzugefügt werden. Einfacher ist jedoch die Verwendung des Eclipse Marketplace, sofern das Plugin dort gelistet ist. Die ADT sind hier Stand 2022 leider nicht gelistet, so dass für die ADT der Weg über die Update Site notwendig ist.

### Eclipse Marketplace

Der Eclipse Marketplace ist im Help-Menü versteckt.

![](./Kapitel 5 - Installation, Verteilung- und Updatestrategien_/media/image5.png){width="4.375in" height="5.010416666666667in"}

Hier kann dann nach Plug-ins gesucht werden. Das Suchwort *ABAP* lieferte Stand 2022 in diesem Beispiel 11 Treffer.

![](./Kapitel 5 - Installation, Verteilung- und Updatestrategien_/media/image4.png){width="6.5in" height="6.361111111111111in"}

Jedes Plug-in besitzt auf der rechten Seite einen eigenen *Install*-Button. Gegebenenfalls müssen noch eine Lizenz bestätigt und Zertifikaten vertraut werden. Abschließend ist ein Neustart von Eclipse notwendig.

### Update Site

Kennt man die Update Site eines Plug-ins oder ist es nicht auf dem Eclipse Marketplace gelistet, kann auch der klassische Installationsweg verwendet werden.

![](./Kapitel 5 - Installation, Verteilung- und Updatestrategien_/media/image14.png){width="4.375in" height="4.989583333333333in"}

Im Feld *Work with* wird die Update Site eingetragen. Ist diese gültig, werden unten die dort verfügbaren Plug-ins angezeigt. Neben Webseiten kann auch eine heruntergeladene Version des Plug-ins in einer .zip-Datei eine Update Site sein. In letzterem Fall müssen Updates jedoch händisch mit einem weiteren Download durchgeführt werden.

![](./Kapitel 5 - Installation, Verteilung- und Updatestrategien_/media/image9.png){width="6.5in" height="5.291666666666667in"}

Auch hier müssen gegebenenfalls Lizenzen akzeptiert und Zertifikaten vertraut werden. Nach einem Neustart steht das Plugin zur Verfügung.

## Installations- und Verteilungsstrategien

### Übersicht und Vergleich

Da die ADT erst vergleichsweise spät dem Eclipse-Ökosystem hinzugefügt wurden, haben sich außerhalb des SAP-Umfeldes bereits diverse Installations-Strategien für Eclipse-basierte Entwicklungsumgebungen etabliert. Die verbreitetsten sind:

1.  Komplett manuelle Einrichtung

2.  Vorkonfigurierte Erstinstallation

3.  Eclipse-Installer (Oomph)

In den folgenden Kapiteln wird auf diese Mechanismen im Detail eingegangen. Neben diesen existieren noch viele weitere Möglichkeiten, die entweder für ungewöhnlichere Situationen (z.B. fehlender Internetzugriff) konzipiert wurden oder mittlerweile durch komfortable Varianten verdrängt wurden.

Zunächst sollen die näher betrachteten Varianten jedoch einmal in einer Übersicht verglichen werden. Die dabei verwendeten Symbole *+ (gut)*, *∘ (mittel)* und *- (schlecht)* sind als Rangfolge zu verstehen und nicht als absolute Werte am Anfang, Mitte und Ende des jeweiligen Spektrums. Grund hierfür ist, dass beispielsweise ein Aufwand sehr individuell wahrgenommen wird. So kann ein mit *-* gekennzeichneter hoher Aufwand für eine Person trotzdem akzeptabel sein. Wohl jedoch stellen diese Symbole eine Reihenfolge dar, da messbare Unterschiede existieren.

  ---------------------------------------------------------------------------------------------------------------------------------------------------------
  **Kriterium**                                          **Manuelle Installation**               **Vorkonfigurierte\                **Eclipse-Installer**
                                                                                                 Erstinstallation**                 
  ------------------------------------------------------ --------------------------------------- ---------------------------------- -----------------------
  **Aufwand Anwender**                                   *-*                                     *∘*                                *+*

  **Notwendiges Wissen Anwender**                        *-*                                     *∘*                                *+*

  **Aufwand zentrale Verwaltung**                        *+\                                     *∘*                                *-*
                                                         (keiner)*                                                                  

  **Benötige zentrale Infrastruktur**                    *+\                                     *∘*                                *-*
                                                         (keine)*                                                                   

  **Aufwand Anwender bei Update / Upgrade**              *∘*                                     *∘*                                *∘*

  **Aufwand zentrale Verwaltung bei Update / Upgrade**   *+\                                     *-*                                *∘*
                                                         (keiner)*                                                                  

  **Automatische Verteilung Addons, Einstellungen**      *-\                                     *∘\                                *+\
                                                         (unmöglich)*                            (einmalig)*                        (kontinuierlich)*

  **Empfohlen für Unternehmensgröße**                    *Einzelpersonen / kleine Unternehmen*   *Mittlere und große Unternehmen*   *Große Unternehmen*
  ---------------------------------------------------------------------------------------------------------------------------------------------------------

### Manuelle Installation

In dieser Variante wird eine Standard-Eclipse-Installation neu heruntergeladen und mittels weniger Klicks installiert. Da es keinen vorgefertigten Installer für ABAP gibt, werden die ADT einzeln nachinstalliert. Ohne Plug-ins, Einstellungen und Perspective-Anpassungen ist hier nach ungefähr 15 Minuten eine benutzbare Installation vorhanden.

SAP hat auf ihrer Lernplattform eine bebilderte Anleitung zur Verfügung gestellt: [[https://developers.sap.com/tutorials/abap-install-adt.html]{.underline}](https://developers.sap.com/tutorials/abap-install-adt.html).

Ist diese Installation abgeschlossen, müssen die gewünschten Einstellungen (siehe Abschnitt ToDo) vorgenommen und die Systemverbindungen hinzugefügt werden.

Sollten weitere Plugins zum Einsatz kommen (siehe Abschnitt ToDo), müssen auch diese nachinstalliert werden.

### Vorkonfigurierte Erstinstallation

Eine vorkonfigurierte Erstinstallation ist im Prinzip eine manuelle Installation, deren Zustand direkt nach der Einrichtung gesichert wird (in der Regel gepackt als .zip-Datei). Dieser Zustand kann dann über diverse Wege an andere Personen verteilt werden.

Bei macOS als Zielsystem gilt es zudem zu beachten, dass hier eine sogenannte App Translocation greifen könnte. Diese erstellt von einer frisch **[im selben Verzeichnis]{.underline}** entpackten Software wie Eclipse bei Ausführung eine Art "Schattenkopie". Das Ergebnis verhält sich wie fehlende Schreibrechte im Installationsordner. Sollten Updates eingespielt werden, kommt es zu Fehlern.

Je nach Einsatzzweck können mehr oder weniger der drei Komponenten der Installation (Installationsordner, User Settings, Workspace) mit hinein gepackt werden.

Nimmt man die User Settings mit, so lassen sich beispielsweise die ADT und deren Update Site für spätere Updates gleich mit verteilen. In Citrix-Umgebungen wäre dies hingegen hinderlich, da die User Settings Schreibrechte benötigen. Nimmt man hierfür 2 getrennte Pakete, könnte auch der Installationsordner in einen schreibgeschützten, zentral provisionierten Teil untergebracht werden.

Den Workspace könnte man ebenfalls als getrenntes Template packen und verteilen. Somit können initiale Einstellungen einmalig verteilt werden. Da sich dieser Bereich allerdings sehr häufig ändert und bei einem Update ohnehin nicht ohne Verlust der aktuellen Einstellungen, Layouts und geöffneten Objekten verwendet werden kann, sollte dies nur mit Bedacht verwendet werden.

### Eclipse-Installer

Auf der Eclipse Webseite kann ein komfortabler Installer mit sehr geringer Downloadgröße heruntergeladen werden (auch *Oomph Installer* genannt). Bei dessen Start kann man dann eine Basiskonfiguration und ein paar Detaileinstellungen auswählen und daraufhin genau diese Installation erstellen lassen.

Hier finden sich allerdings keinerlei Konfigurationen inklusive der ADT wieder. Jedoch handelt es sich hierbei nicht um Magie, sondern die verfügbaren Einstellungen sind lediglich in einem bestimmten Format auf einem Server der Eclipse-Foundation abgelegt. Der Pfad, wo der Installer nach Konfigurationen suchen soll, ist anpassbar. Somit ist es möglich, unternehmensspezifische Konfigurationen zu erstellen, die jeder nach Belieben installieren kann. Dieses Verfahren könnte in einigen Unternehmen bereits für andere Entwicklungssprachen im Einsatz sein, was den Wartungsaufwand deutlich reduziert.

Lediglich der Eclipse Installer muss dann noch beispielsweise per Softwareverteilungs-Mechanismus zusammen mit der Einstellung, wo die Konfigurationen zu finden sind, verteilt werden.

Zusätzlich bietet diese Variante die Möglichkeit, einzelne Einstellungen im Workspace vorzubelegen und aktuell zu halten.

Der Nachteil dieser Variante ist der vergleichsweise hohe zentrale Aufwand. Sie ist somit nicht für einzelne / wenige Standard-Installationen geeignet.

Das Oomph-Projekt hat eine umfangreiche englischsprachige Dokumentation zur Verfügung gestellt, welche sich gut als Nachschlagewerk eignet: [[Link]{.underline}](https://wiki.eclipse.org/Eclipse_Oomph_Authoring).

#### Administratorinformationen

In den folgenden Abschnitten wird die Anlage und Anpassung der Oomph Konfigurationen erklärt. Diese wird in der Regel von wenigen Administratoren durchgeführt.

##### Begriffsdefinitionen

Liest man sich die Dokumentation von Oomph durch, so wird man erst einmal mit vielen neuen Begriffen erschlagen. Daher hier vorab die wichtigsten Begriffe, die in den folgenden Abschnitten verwendet werden. Es werden hier bewusst die englischen Originalbegriffe verwendet, um Wiedererkennungswert zu schaffen.

+--------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+
| **Begriff**        | **Beschreibung**                                                                                                                                         |
+====================+==========================================================================================================================================================+
| **Setup Model**    | Die Oomph-Konfigurationen sind, wie in den meisten Programmiersprachen, dateibasiert. Ein Satz dieser Dateien mit spezifischem Format heißt Setup Model. |
+--------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+
| **Product**        | Eine Konfiguration auf Installationsebene (Plattform mit bestimmter Version + Plugins)                                                                   |
+--------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+
| **Project**        | Projektspezifische Einstellungen. In der git-basierten Welt kann dies beispielsweise die Vorgabe von Standard-Repositories sein.                         |
|                    |                                                                                                                                                          |
|                    | Konfiguration auf Workspace-Ebene.                                                                                                                       |
+--------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+
| **Index**          | Die Bibliothek an verfügbaren Konfigurationen, die im Eclipse Installer ausgewählt werden können.                                                        |
+--------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------+

Eine Installation mittels Eclipse/Oomph Installer installiert somit immer eine Plattform, die Plugins, Oomph Updater, Oomph Recorder und zuletzt die Projekteinstellungen im Workspace.

##### Benötigte Software für Administratoren

Wer eigene Konfigurationen entwerfen und verwalten will, braucht das Oomph SDK. Das ist nichts anderes als ein Set von Plug-ins auf einer Standard-Eclipse Plattform. Hiermit werden die benötigten Views und vorgefertigte Perspektiven ausgeliefert, um die Konfigurationsdateien grafisch aufbereitet bearbeiten zu können (Baumansichten, Formulare, ...).

##### Schritt für Schritt zu einer Basiskonfiguration

Bei der Anlage von Konfigurationen geht man in der Regel von allgemein nach spezifisch vor. Für alle im folgenden genannten benötigten Dateien bietet das Oomph SDK Wizards an (File → New → Other → Oomph → ...).

Zu beachten ist ebenfalls, dass Oomph sehr viele Optionen an vielen Stellen zulässt. Somit gibt es selten **die eine** richtige Implementierung. Was funktioniert und nachvollziehbar ist, ist richtig.

###### Index

Das bedeutet, man legt zuerst einen *Index* an. Ein Index verweist auf verfügbare *Product-Catalogs* und *Project-Catalogs*. Der Index trägt standardmäßig den Dateinamen *org.eclipse.setup*, kann aber beispielsweise auch *myFirst.setup* heißen.

Folgend der Aufbau eines Index:

*\<?xml version=\"1.0\" encoding=\"UTF-8\"?\>*

*\<setup:Index*

*xmi:version=\"2.0\"*

*xmlns:xmi=\"http://www.omg.org/XMI\"*

*xmlns:setup=\"http://www.eclipse.org/oomph/setup/1.0\"*

*name=\"myCompany Eclipse Setups\"*

*label=\"index\"\>*

*\<productCatalog*

*href=\"myCompany.products.setup#/\"/\>*

*\<projectCatalog*

*href=\"myCompany.projects.setup#/\"/\>*

*\</setup:Index\>*

In der erweiterten Ansicht des Eclipse Installers hat man dann auch die Möglichkeit, zwischen mehreren verfügbaren Indizes zu wechseln.

![](./Kapitel 5 - Installation, Verteilung- und Updatestrategien_/media/image3.png){width="6.5in" height="3.9166666666666665in"}

###### Product-Catalog und Product

Ein Product-Catalog listet verschiedene Products. Er enthält darüber hinaus auch übergreifende Einstellungen, wie beispielsweise:

-   interne Umleitungen von Update Sites auf lokale Caches

-   Definition von Variablen, die später pro Product (Version) oder Project anders gefüllt werden können (beispielsweise für versions-spezifische Updatesites)

-   die Installation des Oomph Clients für die spätere Verteilung von Aktualisierungen der in den Products und Projects festgelegten Einstellungen, Oomph Recorder (siehe Abschnitt [[Anwenderinformationen]{.underline}](#anwenderinformationen)).

Auf allen Ebenen (Product-Catalog, Product und Productversion) können folgende Eigenschaften hinterlegt werden:

![](./Kapitel 5 - Installation, Verteilung- und Updatestrategien_/media/image12.png){width="6.5in" height="4.708333333333333in"}

Die meist benötigten Elemente sind:

  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  **Product**           Fügt ein Product dem Product Catalog hinzu (z.B. IDE for JAVA Developers). Kann nur auf Ebene Product Catalog hinzugefügt werden.
  --------------------- -----------------------------------------------------------------------------------------------------------------------------------------------------------------
  **Product Version**   Fügt einem Product eine Version hinzu, in der dann versions-spezifische Einstellungen vorgenommen werden können. Kann nur auf Ebene Product hinzugefügt werden.

  **Compound**          Dies ist eine Art Ordner, in dem später Einstellungen gruppiert werden können.

  **Eclipse ini**       Fügt der Eclipse.ini eine Option hinzu.

  **Variable**          Variable mit Wertzuweisung. Später kann darauf referenziert werden.

  **Redirection**       URL-Weiterleitung. Somit können die Installationen die Originalquellen einbinden, tatsächlich wird jedoch beispielsweise auf ein Artifactory zugegriffen.

  **P2 Director**       Liste der zu installierenden Feature groups.

  **Repository**        Stellt eine Installationsquelle für Feature groups
  ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Für eine minimale ADT-Installation wird folgendes benötigt:

-   Ein Product, z.B. "SAP"

-   Eine Product Version, z.B. "2022-03 (4.23)"

-   Ein P2 Director Task mit:

    -   Eclipse Platform Packages

        -   epp.package.java (Value Range beginnt bei gewünschtem Release → 4.23)

        -   org.eclipse.platform (Value Range beginnt bei gewünschtem Release → 4.23)

        -   org.eclipse.rcp (Value Range beginnt bei gewünschtem Release → 4.23)

        -   org.eclipse.buildship

        -   org.eclipse.tips.feature

        -   org.eclipse.epp.mpc

    -   Repository URLs für die Eclipse Platform Packages

        -   [[https://download.eclipse.org/releases/2202-03/202203161000]{.underline}](https://download.eclipse.org/releases/2202-03/202203161000) (Link releaseabhängig!)

        -   [[https://download.eclipse.org/technology/epp/packages/2022-03/202203101200]{.underline}](https://download.eclipse.org/technology/epp/packages/2022-03/202203101200) (Link releaseabhängig!)

![](./Kapitel 5 - Installation, Verteilung- und Updatestrategien_/media/image2.png){width="6.5in" height="1.6527777777777777in"}

###### Project-Catalog und Project

Der Project Catalog listet verfügbare Projects. Letztere enthalten Anweisungen für die Veränderung des Workspaces. Hier werden beispielsweise auch die zu installierenden Plug-ins hinterlegt.

Die wichtigsten Elemente in einem Project sind:

  ------------------------------------------------------------------------------------------------------------------------------------------------
  **P2 Director**   Gruppiert Requirements und Repositories
  ----------------- ------------------------------------------------------------------------------------------------------------------------------
  **Requirement**   Zu installierende Feature Group

  **Repository**    Update Site URL

  **Stream**        Verpflichtendes Objekt. Unterschiedliche Konfigurationen pro Stream möglich. Kann mit Namen aber ohne Inhalt vorhanden sein.

  **Variable**      Variabler String mit Referenzierbarkeit z.B. in Repositories

  **Eclipse ini**   Veränderungen der Eclipse.ini

  **Preference**    Voreingestellte Veränderung der Einstellungen (Window → Preferences).
  ------------------------------------------------------------------------------------------------------------------------------------------------

Für eine minimale ADT-Installation wird benötigt:

-   P2 Director-Knoten

    -   ADT Feature Groups

        -   com.sap.adt.tools.hana.devedition

        -   com.sap.core.devedition

    -   Repository für ADT

        -   https://tools.hana .ondemand.com/latest

        -   Alternativ: \${Variable} → z.B. \${sap.repository.url}

-   Einen leeren Stream, standardmäßig "Master"

![](./Kapitel 5 - Installation, Verteilung- und Updatestrategien_/media/image1.png){width="3.5416666666666665in" height="1.5208333333333333in"}

Zu beachten ist, dass ein Plugin für Eclipse aus mehreren Feature Groups bestehen kann. Wie man diese herausfinden kann, wird im Abschnitt [[Zusätzliches Plugin installieren]{.underline}](#zusätzliches-plugin-installieren) erklärt.

##### Verteilung des Installers und Konfiguration

Der Eclipse Installer ist dieselbe .exe-Datei, die man von der Eclipse-Startseite herunterladen kann. Diese muss auf die Endgeräte verteilt werden.

Die Verteilung der Konfiguration erfolgt über die Anlage / Anpassung einer Datei mit festgelegtem Namen und Pfad:

*C:\\Users\\\<currentUser\>\\.eclipse\\org.eclipse.oomph.setup\\setups\\indices.xmi*

Beispiel eines Indexliste:

*\<?xml version="1.0" encoding="UTF-8" ?\>*

*\<base:Annotation*

*xmi:version="2.0"*

*xmlns:xmi="[[http://www.omg.org/XMI]{.underline}](http://www.omg.org/XMI)"*

*xmlns:base="[[http://www.eclipse.org/oomph/base/1.0]{.underline}](http://www.eclipse.org/oomph/base/1.0)"*

*source="IndexLocations"\>*

*\<detail key="[[https://pages.github.com/path/subpath/myFirst.setup]{.underline}](https://pages.github.com/path/subpath/myFirst.setup)"\>*

*\<value\>Description of my first Oomph Catalog\</value\>*

*\</detail\>*

*\</base:Annotation\>*

Hierbei ist der Pfad zu einem oder mehreren Indizes anzugeben. Der Pfad kann auch ein Weblink oder git-Repository-Pfad sein. Die verlinkten Dateien werden dann bei jedem Start des Eclipse Installers heruntergeladen/aktualisiert.

Nun kann jeder Anwender, der im Besitz des Installers ist, diese Konfigurationen auswählen und installieren. Alternativ kann auch der Installer noch per Softwareverteilung im Unternehmen verteilt werden. Somit könnte ein Gleichlauf der Versionen zwischen Oomph-Komponenten in den Installationen und dem verwendeten Eclipse Installer erreicht werden.

##### Anpassungsbeispiele

Bisher wurden minimale Konfigurationen zur Installation von Eclipse und SAP ADT beschrieben. Die Stärken der Verwendung des Oomph-Installers ergeben sich jedoch erst in der weiteren Vorkonfiguration bei der Installation. Auf die häufigsten Erweiterungswünsche wird nun eingegangen.

###### Zusätzliches Plugin installieren

Einer der Vorteile von Eclipse als Entwicklungsplattform ist die Offenheit gegenüber Erweiterungen. Somit können von Drittherstellern oder engagierten Community-Mitgliedern Plug-ins geschrieben werden, die den Funktionsumfang der ADT noch erweitern.

Diese können in einem Oomph Project automatisch bei allen Installationen vorinstalliert werden. Leider ist es jedoch nicht möglich, ein Repository als Update Site hinzuzufügen, dann aber kein Plug-in darauf zu installieren. Diese Update Sites werden bei der Installation von Oomph verworfen (aktueller Stand 2022).

Nun besteht ein Plugin aus mindestens einer Feature Group. Im Oomph Project muss letztere hinterlegt werden. Der Name ist jedoch in der Regel nicht bekannt. Hierfür existiert im Oomph SDK eine View "Repository Explorer" (Window → Show View → Other → Oomph → Repository Explorer). Unter (manueller) Angabe einer Update Site wird eine Abfrage der bereitgestellten Feature Groups durchgeführt. Diese können dann per Drag&Drop oder Strg+C und Strg+V in den P2 Director-Knoten des Oomph Project eingefügt werden. Zusätzlich zu den Feature Groups muss auch ein Repository-Knoten existieren, der diese Update Site zur Verfügung stellt (kann auch allgemeiner, d.h. im Project-Catalog oder Product (-Catalog) erfolgen).

Stand Oktober 2022 sieht die Abfrage der SAP Update site wie folgt aus:

![](./Kapitel 5 - Installation, Verteilung- und Updatestrategien_/media/image10.png){width="5.84375in" height="2.90625in"}

###### Vorgeben von Einstellungen

Neben der Installation von ADT ist die Voreinstellung diverser Einstellungen für alle Installationen einer der größten Vorteile.

Alle zu vergebenden Einstellungen kommen in den Compound/Ordner *User Preferences* des Oomph Projects. Darunter gibt es dann einzelne Unterordner pro Feature (als Teil einer Feature Group) und darin dann die Einstellungen. Hierbei ist es jedoch jedem Feature selbst überlassen, in welcher Darstellung es seine Einstellungen speichert. Einige Einstellungen sind als direkte Werte abgelegt (klassische Checkboxen), andere beispielsweise als ein großes XML pro Einstellungsseite. Gerade bei letzterem können dann nur alle Einstellungen der Seite oder keine vorgegeben werden. Ein Beispiel für die XML-Darstellung sind die Code Templates der SAP ADT.

Da diese nicht einheitliche Darstellung unpraktisch zu administrieren ist und die Namen der Features in der Regel auch nicht bekannt sind, existieren auch hier Hilfswerkzeuge: In den Einstellungen wird ein Oomph Recorder installiert. Näheres zur Verwendung als Anwender kann dem Abschnitt [[Oomph Recorder]{.underline}](#oomph-recorder) entnommen werden. Der Oomph Recorder zeichnet den letzten Stand aller geänderten Einstellungen auf Benutzerebene auf (also übergreifend über Eclipse-Installationen) und fragt nach dem Schließen der Einstellungen, ob diese immer/einmal/nie gespeichert werden sollen. Ebenso wird damit ein Button in der Buttonleiste von Eclipse verfügbar, um sich die bereits gespeicherten Einstellungen anzuschauen.

![](./Kapitel 5 - Installation, Verteilung- und Updatestrategien_/media/image8.png){width="2.2083333333333335in" height="0.8125in"}

![](./Kapitel 5 - Installation, Verteilung- und Updatestrategien_/media/image11.png){width="5.447916666666667in" height="2.7916666666666665in"}

Hierbei handelt es sich wieder um .setup-Dateien. Somit können auch hier wieder die aufgezeichneten Einstellungen mit Drag&Drop oder Strg+C und Strg+V in das Oomph Project übernommen werden.

Folgend ein paar Beispiele für allgemein verteilte Einstellungen. Hier finden sich einige der ADT exklusiven Features, die SAP leider nicht alle standardmäßig aktiviert.

-   Code Templates

-   Aktivierung der zusätzlichen Code Highlights in ABAP (verschiedene Farben)

-   Kontinuierliche Syntaxprüfung aktivieren

-   Aktivieren des Occurrence Marker

-   Einheitliche Darstellung von Einrückungen (Tabs oder Leerzeichen, Breite der Einrückung)

###### Grenzen Arbeitsspeicher anheben

Mit SAP ADT existieren keine Grenzen mehr in der Anzahl der geöffneten Fenster/Objekte. Das hat aber auch zur Folge, dass bei vielen gleichzeitigen Tabs der benötigte Arbeitsspeicher in die Höhe schnellt. Da Eclipse selbst auch eine Java Anwendung ist, müssen VM-Parameter festgelegt werden. Ist der verfügbare Speicher einer Java VM überschritten, findet analog der RAM-Verwaltung des Betriebssystems ein Swapping in Festplattendateien statt. Das macht Eclipse langsam und der Spaß an der Benutzung leidet. Es können für den VM-Speicher Mindest- und Maximalgrenzen, initial reservierte Speichermenge und vieles mehr eingestellt werden. Da hiermit aber auch eine Menge kaputt gemacht werden kann und die benötigten Grenzen doch sehr individuell sind, wird bewusst auf konkrete Empfehlungen verzichtet.

Sollten doch solche Parameter für alle Nutzer vorgegeben werden, so müssen diese in der eclipse.ini angegeben werden. Somit müssen im Oomph Project entsprechende Eclipse Ini Tasks angelegt werden.

###### Erster Start mit ABAP Perspective

Werden die SAP ADT installiert, so sind die Standard Perspectives zunächst nur vorhanden, aber nicht geöffnet. Eclipse startet beispielsweise mit der Java Perspective, welche die meisten ABAP Entwickler eher selten benötigen werden. Daher ist es wünschenswert, die Installation direkt mit der ausgelieferten ABAP Perspective zu starten. Die Hinterlegung weiterer Perspectives (z.B. Debug, ABAP Profiling, ...) in den zuletzt verwendeten Perspectives oben rechts ist leider nicht möglich.

Das Erzwingen einer Start-Perspective ist eine Startoption in der eclipse.ini. Es muss somit ein Eclipse Ini Task mit den folgenden Eigenschaften angelegt werden:

  -----------------------------------------------------------------------
  Option             -perspective
  ------------------ ----------------------------------------------------
  Value              com.sap.adt.ui.AbapPerspective

  VM                 false
  -----------------------------------------------------------------------

#### Anwenderinformationen

##### Installation mittels Eclipse Installer

Beim ersten Start des Eclipse Installers kann es sein, dass dieser im einfachen Modus startet. Für die Verwendung eigener Konfigurationen ist jedoch der erweiterte Modus nötig. Über das Menü oben rechts kann in diesen gewechselt werden ("Advanced mode").

![](./Kapitel 5 - Installation, Verteilung- und Updatestrategien_/media/image7.png){width="6.5in" height="3.75in"}

Nun sieht man eine Liste an verfügbaren Products. Dies ist der Inhalt des ersten referenzierten Index in der indices.xmi. Oben rechts kann zwischen allen gelisteten Indizes gewechselt werden.

![](./Kapitel 5 - Installation, Verteilung- und Updatestrategien_/media/image15.png){width="6.5in" height="3.9166666666666665in"}

Hier wählt man einen passenden Eintrag aus. Sobald dies geschehen ist, wird unten die verfügbare Produktversion auswählbar.

![](./Kapitel 5 - Installation, Verteilung- und Updatestrategien_/media/image13.png){width="6.5in" height="5.333333333333333in"}

Im nächsten Bild sind die Projects auswählbar. Hier können theoretisch mehrere Projects für eine Installation gewählt werden. Jedoch kann es passieren, dass diese Projects dann konkurrierende Einstellungen vornehmen und es kommt zu Problemen.

Zu einem Project muss dann auch zwingend ein Stream gewählt werden. Wurde nur einer definiert, ist dieser bereits vorausgewählt.

![](./Kapitel 5 - Installation, Verteilung- und Updatestrategien_/media/image16.png){width="6.5in" height="5.333333333333333in"}

Nun werden noch definierte und verwendete, aber nicht gefüllte Variablen abgefragt. Dies können beispielsweise die Pfade zu Installation und Workspace sein.

![](./Kapitel 5 - Installation, Verteilung- und Updatestrategien_/media/image6.png){width="6.5in" height="5.347222222222222in"}

Zuletzt wird noch eine Bestätigungsseite angezeigt, die aber in der Regel keine neuen Informationen enthält.

Während der Installation kann es passieren, dass Popups angezeigt werden, in welchen Lizenzbedingungen akzeptiert und/oder Zertifikaten vertraut werden sollen.

Ist der Haken im Installer gesetzt, wird die eben installierte Instanz nach der Installation gestartet. Dabei werden dann noch einmal die Einstellungen des Workspace vorkonfiguriert, da der Workspace erst jetzt existiert.

Nun ist die Installation abgeschlossen.

##### Oomph Recorder

Der Oomph Recorder ist ein Hilfswerkzeug, um Einstellungen über Eclipse Installationen hinweg zu vereinheitlichen und zu sichern. Optional können diese Einstellungen auch an den Eclipse User Account geladen werden, so dass hier auch noch eine geräteübergreifende Synchronisation möglich ist. In den meisten Firmen ist die Verwendung eines Eclipse Accounts aber vermutlich nicht möglich oder nicht gern gesehen.

Der Oomph Recorder wird unter Window → Preferences mit einem neuen Aufnahmesymbol aktiviert. Ab dann wird bei jedem Schließen der Einstellungen abgefragt, ob die gerade veränderten Einstellungen immer/einmal/nie im letzten Zustand gespeichert werden sollen.

Diese Einstellungen auf User-Ebene werden bei einer neuen Eclipse Installation erst nach dem ersten Start und nach den Einstellungen des verwendeten Oomph Projects angewendet. Somit können hier auch Standardeinstellungen benutzer-individuell übersteuert werden oder eigene Standardeinstellungen definiert werden.

## Fehlersituationen

Während der Installation oder eines Updates kann es zu verschiedenen Fehlern kommen. Auf die häufigsten Fehler soll hier einmal eingegangen werden.

### Fehler beim Update oder Upgrade

Bei der Durchführung eines Updates oder Upgrades kann es vorkommen, dass die Installation fehlschlägt.

Eine Installation läuft in der Regel in den folgenden Phasen ab:

1.  Zielversionen aller Komponenten und deren Pakete berechnen, inklusive Beachtung der Abhängigkeiten

2.  Pakete herunterladen

3.  Pakete installieren

Die erfahrungsgemäß häufigste Fehlerursache bei der Berechnung der Zielversionen sind nicht erfüllbare Abhängigkeiten.

So fordert in einem erfundenen Beispiel ein ADT-Upgrade auch eine neuere Version von Eclipse selbst. In den verfügbaren Update Sites ist jedoch nur die aktuelle Version zu finden. Somit ist die Fehlerursache hier eine falsche oder veraltete Update Site.

In schwierigeren Fällen kann es sein, dass beispielsweise in neueren Releases von Eclipse eine Komponente entfernt oder ersetzt wird. Ein Plugin wie die ADT haben jedoch noch eine Abhängigkeit dazu definiert. Hier kann nur der Hersteller des Plugins Abhilfe schaffen.

Eine solche Situation kann bei Verwendung von HANA Studio als Eclipse-Plattform entstehen. Hier werden bei der Installation keine öffentlichen Update Sites für die Eclipse Plattform eingestellt, weshalb sie spätestens nach 6 Monaten (= 2 vierteljährliche Releases) nicht mehr mit aktuellen ADT-Versionen zusammenpasst.

Deutlich seltener treten Fehler beim Download auf. Im ersten Schritt wurden bereits die Inventarlisten der Update Sites (artifacts.xml und contents.xml) untersucht und es wurde eine passende Paketversion gefunden. Befindet sich diese dann allerdings nicht im entsprechenden Unterordner, so kommt es zu einem Downloadfehler. Hier ist dann die Update Site inkonsistent. Dies kann ebenfalls nur der Anbieter des Pakets beheben.

### Single Sign On-Bibliotheken

Je nach Single Sign On Strategie können Umgebungsvariablen auf Bibliotheken notwendig sein. Wenn SAP GUI lediglich als 32 Bit Anwendung installiert wird (bis SAP GUI 7.70 einzige Option), werden immer die Bibliotheken verwendet, die in den Umgebungsvariablen SNC_LIB und SNC_LIB_2 referenziert werden.

Wird Eclipse jedoch als 64 Bit Anwendung installiert (Standardfall), so wird die in der Umgebungsvariable SNC_LIB_64 referenzierte SSO-Bibliothek verwendet.

In älteren Blogeinträgen werden noch die Kerberos Bibliotheken von Windows empfohlen (32 Bit: gsskrb5.dll, 64 Bit: gx64krb5.dll). Diese sind jedoch in neueren Windows Installationen nicht mehr vorhanden und sollten auch nicht mehr verwendet werden. SAP liefert mit dem SAP GUI eine SAP CryptoLib aus. Diese liegt in 32 Bit und 64 Bit vor.

### No repository found containing

[[https://launchpad.support.sap.com/#/notes/2186770]{.underline}](https://launchpad.support.sap.com/#/notes/2186770)

Ab und zu scheint es Probleme zu geben, die ADT zu aktualisieren. Im Protokoll erscheinen mehrere Fehler "No repository found containing:....". Der Hinweis empfiehlt, die Update Site zu entfernen, Eclipse neu zu starten und dann die Update site neu hinzu zu fügen.

### PKIX - certificate error

[[https://launchpad.support.sap.com/#/notes/3131747]{.underline}](https://launchpad.support.sap.com/#/notes/3131747)

Hier handelt es sich um einen Zertifikatsfehler. Zur Update-Site wird eine verschlüsselte Verbindung aufgebaut (HTTPS). Wenn im Unternehmensnetzwerk SSL-Verbindungen aufgebrochen werden oder es keinen gemeinsamen keystore für interne und externe Update-sites gibt, dann kann es zu diesem Fehler kommen. Der Hinweis gibt eine mögliche Lösung hierfür. Eine andere Möglichkeit ist die Verteilung eigener angepasster JDKs.

### macOS aarch64 support & SAP GUI for Java

[[https://launchpad.support.sap.com/#/notes/3251738]{.underline}](https://launchpad.support.sap.com/#/notes/3251738)

Die Architektur von SAP GUI und Eclipse-Installation sollte grundsätzlich mit derselben Prozessorarchitektur installiert werden. Gerade bei Apple M1 und folgend könnte es hier zu Abweichungen kommen.

### offline Installation - Download ADT Abhängigkeiten

[[https://launchpad.support.sap.com/#/notes/2369308]{.underline}](https://launchpad.support.sap.com/#/notes/2369308)

Soll ADT offline installiert werden, müssen diverse Abhängigkeiten beachtet werden. Dieser Hinweis gibt einige Möglichkeiten für Abhilfe in diesem Fall.