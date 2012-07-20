.. index::
    single: Cache; Varnish

Wie man Varnish benutzt um die Webseite zu beschleunigen
========================================================

Weil der Symfony2 Cache die Standard HTTP Cache Headers benutzt, kann der
:ref:`symfony-gateway-cache` leicht mit irgend einem anderen Reverse
Proxy ersetzt werden. Varnish ist ein mächtiger, Open-Source, HTTP Beschleuniger geeignet um
Gecachte Inhalte schnell auszuliefern samt :ref:`Edge Side
Includes<edge-side-includes>` Unterstützung.

.. index::
    single: Varnish; Konfiguration

Konfiguration
-------------

Wie man vorher gesehen hat ist Symfony2 intelligent genug um zu erkennen ob er mit einem
Reverse Proxy spricht der ESI versteht oder nicht. Es funktioniert out of the box wenn du
den Symfony2 Reverse Proxy verwendest, du brauchst aber eine Spezielle Konfiguration damit es
mit Varnish funktioniert. Dankenswerter weise basiert Symfony2 auf noch einem anderen Standard
geschrieben von Akamaï (`Edge Architecture`_), so dass Konfigurationstipps in diesem Kapitel
nützlich sein können auch wenn du nicht Symfony2 verwendest.

.. note::

    Varnish unterstützt nur das ``src`` Attribute der ESI Tags (``onerror`` und
    ``alt`` Eigenschaften werden ignoriert).

Zuerst musst du Varnish Konfigurieren, damit er seinen ESI Support ankündigt indem er den
``Surrogate-Capability`` Header in Requests mitschickt die zur Backendanwendung
weitergeleitet werden:

.. code-block:: text

    sub vcl_recv {
        set req.http.Surrogate-Capability = "abc=ESI/1.0";
    }

Danach optimiere Varnish so dass dieser nur den Response Inhalt parst, wenn
mindestens ein ESI Tag vorhanden ist indem er auf den ``Surrogate-Control`` Header Eintrag prüft, den
Symfony2 automatisch hinzufügt:

.. code-block:: text

    sub vcl_fetch {
        if (beresp.http.Surrogate-Control ~ "ESI/1.0") {
            unset beresp.http.Surrogate-Control;

            // for Varnish >= 3.0
            set beresp.do_esi = true;
            // for Varnish < 3.0
            // esi;
        }
    }

.. caution::

    Kompression mit ESI wurde bis Version 3.0 von Varnish nicht unterstützt
    (Lies `GZIP and Varnish`_). Wenn du nicht Varnish 3.0 verwendest, stelle einen Webserver
    vor Varnish welcher die Kompression durchführt.

.. index::
    single: Varnish; Invalidierung

Cache Invalidierung
-------------------

Du solltest es nie brauchen die gecachten Daten zu invalidieren, da dies bereits
Nativ im HTTP Cache Model greift (siehe :ref:`http-cache-invalidation`).

Dennoch kann Varnish konfiguriert werden die spezielle HTTP ``PURGE`` Methode zu akzeptieren,
welche den Cache für die gegeben Resource invalidiert:

.. code-block:: text

    sub vcl_hit {
        if (req.request == "PURGE") {
            set obj.ttl = 0s;
            error 200 "Purged";
        }
    }

    sub vcl_miss {
        if (req.request == "PURGE") {
            error 404 "Not purged";
        }
    }

.. caution::

    Du musst die HTTP Methode ``PURGE`` schützen, um zu verhindern das irgendwelche Leute
    deine Cache Daten leert.

.. _`Edge Architecture`: http://www.w3.org/TR/edge-arch
.. _`GZIP and Varnish`: https://www.varnish-cache.org/docs/3.0/phk/gzip.html
