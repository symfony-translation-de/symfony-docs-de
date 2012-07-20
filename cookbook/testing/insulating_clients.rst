.. index::
   single: Tests; Clients isolieren

Wie man die Interaktion zwischen mehreren Clients testet
========================================================

Wenn du eine Interaktion zwischen mehreren Clients simulieren willst (denk zum Beispiel
an einen Chat), erstelle mehrere Clients::

    $harry = static::createClient();
    $sally = static::createClient();

    $harry->request('POST', '/say/sally/Hello');
    $sally->request('GET', '/messages');

    $this->assertEquals(201, $harry->getResponse()->getStatusCode());
    $this->assertRegExp('/Hello/', $sally->getResponse()->getContent());

Das funktioniert, außer der Code den du Verwaltest verwendet einen Globalen Zustand oder er basiert auf einer
Drittanbieter Bibliothek welches in irgendeiner Form Globale Zustände verwendet. In diesem Fall
kannst du deine Clients isolieren::

    $harry = static::createClient();
    $sally = static::createClient();

    $harry->insulate();
    $sally->insulate();

    $harry->request('POST', '/say/sally/Hello');
    $sally->request('GET', '/messages');

    $this->assertEquals(201, $harry->getResponse()->getStatusCode());
    $this->assertRegExp('/Hello/', $sally->getResponse()->getContent());

Isolieren von Clients führt deren Anfragen erkennbar in einem dedizierten und
sauberen PHP Prozess aus und verhindert dadurch jedweden Seiteneffekt.

.. tip::

    Da isolierte Clients langsamer sind kannst du einen Client im Hauptprozess
    lassen und die anderen isolieren.
