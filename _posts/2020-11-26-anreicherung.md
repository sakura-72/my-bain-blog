---
title: "Anreicherung mit GND-Einträge"
date: 2020-11-26
---

<h1>Aufgabe Anreicherung mit lobid-gnd</h1>
Man öffnet OpenRefine und somit auch das bis jetzt bearbeiteten Projekt in OpenRefine. Anhand der [allgemeine Anleitung im Blog von lobid-gnd](https://blog.lobid.org/2018/08/27/openrefine.html) wurden die Autoren Namen mit der GND abgestummen (reconcialiate). Dies mit folgende Schritte:
* Spalte Autoren auswählen
* Dann auf "Reconcialiate" und > "Start Reconciliation"
* Man muss den Service hinzufügen, dafür klickt man auf "Add Standard Service..." 
* und gibt https://lobid.org/hnd/reconcile als URL ein.

Sobald den Service hinzugefügt wurde, folgte ich diese Schritte:
* Man klickt links auf den GND-Service und wählt Individualisierte Person (DifferentiatedPerson) aus

Nicht alle Zellen haben gematched, aber ich fahre weiter mit den Zellen die gepasst haben:
* Pfeil der Kolumne "Autor" auswählen
* dann auf "Edit column" und "Add column from reconciled values..." anklicken.
* Im Fenster wählen wir "GND-Nummer" und "Geburstagdatum"
* OK drücken

Somit kommen zwei neuen Kolumnen rechts von der Kolumne "Autor", dies aber nur wo die Namen von Autoren wirklich gematched haben.

<h3>Jetzt wird exportiert! Hier nutze ich die Vorlage, welche wir gemeinsam am 7. November erschafft haben:</h3>
Wir wählen Export > Templating ... Und fügen die Ausgangsbasis und den Row Template. Wenn das gemacht worden ist, einfach auf "Export" drücken und lokal speichern.

<h4>Die Ausgangsbasis:</h4>

* Prefix:
    ```xml
    <collection xmlns="http://www.loc.gov/MARC21/slim">
    ```
* Row Separator: (Zeilenumbruch)
* Suffix:
    ```xml
    </collection>
    ```
    
* Und den Row Template:
    ```xml
    <record>
    <leader>     nab a22     uu 4500</leader>
    <controlfield tag="001">{{cells['URL'].value.replace('https://doaj.org/article/','').escape('xml')}}</controlfield>
    <datafield tag="022" ind1=" " ind2=" ">
        <subfield code="a">{{cells['ISSNs'].value.escape('xml')}}</subfield>
    </datafield>{{
    forNonBlank(
        cells['DOI'].value,
        v,
        '
    <datafield tag="024" ind1="7" ind2=" ">
        <subfield code="a">' + v.escape('xml') + '</subfield>
        <subfield code="2">doi</subfield>        
    </datafield>',
        ''
    )
    }}
    <datafield tag="041" ind1=" " ind2=" ">
        <subfield code="a">{{cells['Language'].value.escape('xml')}}</subfield>
    </datafield>
    <datafield tag="100" ind1="0" ind2=" ">
        <subfield code="a">{{cells['Authors'].value.split('|')[0].escape('xml')}}</subfield>
    </datafield>{{forNonBlank(cells["Title"].value.escape('xml'), v, '
    <datafield tag="245" ind1="0" ind2="0">
        <subfield code="a">' + v + '</subfield>
    </datafield>', '')}}
    <datafield tag="260" ind1=" " ind2=" ">
        <subfield code="b">{{forNonBlank(cells["Publisher"].value.escape('xml'), v, v, '')}}</subfield>
    </datafield>
    <datafield tag="264" ind1=" " ind2=" ">
        <subfield code="b">{{cells["Publisher"].value.escape('xml')}}</subfield>
    </datafield>{{
    forEach(cells['Subjects'].value.split('|'), v ,'
    <datafield tag="650" ind1="0" ind2=" ">
        <subfield code="a">' + v.escape('xml') + '</subfield>
    </datafield>')
    }}{{
    forEach(cells['Authors'].value.split('|').slice(1), v ,'
    <datafield tag="700" ind1="0" ind2=" ">
        <subfield code="a">' + v.escape('xml') + '</subfield>
    </datafield>')
    }}
    </record>
    ```

<h2>Kommentar zu dieser Anreicherung</h1>
* Die leere Zellen, also wo keine GND-Nummer und keine Geburtsdatum steht, brauchen ein Platzhalter. Dies ist nötig damit die Autoren wieder zusammengeführt werden können und damit im Export keine "null" Zellen (wo eben keine Daten verfügrbar sind) mitkommen.
* Auch habe ich die Zellen nicht zusammengeführt.
* Um dies nachzuvollziehen, kann [dieser Lösungsweg(https://pad.gwdg.de/ywogyRNTQ_CTg9PvrQywsQ?both#)] angeschaut werden.




