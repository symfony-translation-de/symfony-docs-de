.. index::
   single: Controller; Anpassen der Fehlerseiten
   single: Fehlerseiten

Wie man Fehlerseiten anpasst
============================

Wenn Symfony2 eine Exception wirft, wird diese Exception in der
``Kernel`` Klasse gefangen und schließlich an einen Speziellen Controller,
``TwigBundle:Exception:show``, weitergeleitet und behandelt. Der Controller, welcher
im Core ``TwigBundle`` sich befindet, legt fest welches Fehler Template angezeigt werden soll und
welcher Status code gesetzt werden soll für die aufgetretene Exception.

Fehlerseiten können über zwei unterschiedliche wege angepasst werden, jenachdem wieviel
kontrolle du brauchst:

1. Anpassen der Fehler Templates der verschiedenen Fehlerseiten (weiterunten erklärt);

2. Den Standard Exception Controller ``TwigBundle::Exception:show``
   mit deinem eigenen Controller ersetzen und den Fehler wieimmer du willst behandeln (siehe
   :ref:`exception_controller in der Twig Referenz<config-twig-exception-controller>`);

.. tip::

    Das anpassen des Exceptionhandlings ist deutlich mächtiger
    als es hier beschrieben wird. Ein Internes Event, ``kernel.exception``, wird geschmissen
    was die komplette Kontroller über das Exceptionhandlings ermöglicht. Für mehr
    mehr Informationen siehe :ref:`kernel-kernel.exception`.

Alle Fehlertemplates befinden sich im ``TwigBundle``. Um diese
Templates zu überschreiben können wir auf die einfachen Standardmethoden um Templates zu überschreiben,
welche sich in einem Bundle befinden, zurückgreifen. Für mehr Informationen:
:ref:`overriding-bundle-templates`.

Zum Beispiel um das Standard Fehlertemplate, welches dem End-User angezeigt wird, zu überschreiben,
erstelle ein neues template an folgendem Ort
``app/Resources/TwigBundle/views/Exception/error.html.twig``:

.. code-block:: html+jinja

    <!DOCTYPE html>
    <html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
        <title>An Error Occurred: {{ status_text }}</title>
    </head>
    <body>
        <h1>Oops! An Error Occurred</h1>
        <h2>The server returned a "{{ status_code }} {{ status_text }}".</h2>
    </body>
    </html>

.. tip::

    Wenn du dich nicht mit Twig auskennst verzweifle nicht. Twig ist eine Einfach, Powerfolle
    und optionale Template Engine welche mit ``Symfony2`` integriert ist. Für mehr
    Information über Twig siehe :doc:`/book/templating`.

Zusätzlich zur Standard HTML Fehlerseite, stellt Symfony Standard Fehler Seiten
für die meisten gewähnliche Antwortformate bereit, wie JSON
(``error.json.twig``), XML (``error.xml.twig``) und auch Javascript
(``error.js.twig``), um nur einige zu nennen. Um irgendeins dieser Templates zu überschreiben,
erstelle einfach eine neue Datei mit dem selben Namen im
``app/Resources/TwigBundle/views/Exception`` Verzeichnis. Das ist der Standardweg
um irgendein Template welches sich in einem Bundel befindet zu überschreiben.

.. _cookbook-error-pages-by-status-code:

Anpassen der 404 Seite und anderer Fehlerseiten
-----------------------------------------------

Du kannst auch spezifische Fehler Templates zugehörig zum HTTP status
code anpassen. Zum Beispiel erstelle ein
``app/Resources/TwigBundle/views/Exception/error404.html.twig`` Template um
eine Spezielle 404 (Seite nicht gefunden) Fehlerseite anzuzeigen.

Symfony benutzt den folgenden Algorithmus um herauszufinden welches Template benutzt werden soll:

* Zuerst wird nach einem Template mit dem gebeen Format und Statuscode geschaut (z.B.
  ``error404.json.twig``);

* Wenn das nicht existiert, wird nach einem Template für das gegeben Format geschaut (z.B.
  ``error.json.twig``);

* Wen das nicht exisitert, nimmt er das HTML Template als fallback (z.B.
  ``error.html.twig``).

.. tip::

    Für eine komlette liste der Standard Fehler Templates, siehe das
    ``Resources/views/Exception`` Verzeichnis im ``TwigBundle``. In einer
    Standard Symfony2 Installation befindet sich das ``TwigBundle`` im
    ``vendor/symfony/symfony/src/Symfony/Bundle/TwigBundle`` Verzeichnis. Meistens ist es am einfachsten
    eine Fehlerseite anzupassen diese zuerst von ``TwigBundle`` nach
    ``app/Resources/TwigBundle/views/Exception`` kopieren und dann anzupassen.

.. note::

    Die Debug-freundliche Exception Seite, welche dem Entwickler gezeigt wird kann auch
    auf dem selben weg angepasst werden, indem man einfach Templates erstellt, wie
    ``exception.html.twig`` für die Standard HTML Exception Seite oder
    ``exception.json.twig`` für die JSON Exception Seite.
