.. index::
   single: Requirements
   
Systemvoraussetzungen für den Betrieb von Symfony2
=================================

Für Symfony2 musst du die untenstehende Liste von Systemvoraussetzungen erfüllen. Du kannst
ganz einfach prüfen ob dein System die Systemvoraussetzungen erfüllt, in dem du ``web/config.php``
in deiner Symfony Distribution ausführst. Da die Konsole oft eine andere ``php.ini``
Konfigurationsdatei nutzt, ist es auch ein gute Idee deine Systemvoraussetzungen
über die Kommandozeile, mit dem folgendem Kommando zu prüfen:

.. code-block:: bash

    php app/check.php

Unten ist die Liste der erforderlichen und optionalen Anforderungen.

Benötigt
--------

* PHP muss mindestens mit der Version PHP 5.3.3 installiert sein
* JSON muss verfügbar sein
* ctype muss verfügbar sein
* In deiner php.ini muss die Option date.timezone ausgefüllt sein

Optional
--------

* Das PHP-XML Modul sollte verfügbar sein
* libxml sollte mindestens mit der Version 2.6.21 verfügbar sein
* PHP tokenizer sollte verfügbar sein
* Die mbstring Funktionen sollten verfügbar sein
* iconv sollte verfügbar sein
* POSIX sollte verfügbar sein (nur für \*nix Systeme)
* Intl sollte mit ICU4+ verfügbar sein
* APC 3.0.17+ oder ein ander Opcode Cache sollte verfügbar sein
* Emfohlene PHP.ini Einstellungen

  * ``short_open_tag = Off``
  * ``magic_quotes_gpc = Off``
  * ``register_globals = Off``
  * ``session.autostart = Off``

Doctrine
--------

Wenn du Doctrine nutzen möchtest, dann musst du PDO installiert haben. Zusätzlich
benötigst du noch den PDO Datenbanktreiber, für die Datenbank die du nutzen möchtest.