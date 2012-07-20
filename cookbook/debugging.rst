.. index::
   single: Debugging

Wie man seine Entwicklungsumgebung für das Debugging optimiert
==============================================================

Wenn du an deinem Symfony Projekt lokal arbeitest solltest du die
``dev`` Umgebung (``app_dev.php`` Front Controller) benutzen. Diese Umgebung
ist für zwei Hauptaufgaben optimiert:

* Dem Entwickler genaues Feedback zu geben wann immer was falsch gelaufen ist (Web
  Debug Toolbar, hübsche Exception Seiten, Profiler, ...);

* So identisch wie möglich zur Produktionsumgebung sein, um Probleme zu verhindern
  wenn man das Projekt deployt.

.. _cookbook-debugging-disable-bootstrap:

Deaktivieren der Bootstrap Datei und das Klassen Caching
--------------------------------------------------------

Um die Produktionsumgebung so schnell wie möglich zu machen, erzeugt Symfony
große PHP Dateien in deinem Cache welche die Zusammenfassung von deinen PHP Klassen
, die dein Projekt für jede Anfrage benötigt, enthält. Wie auch immer, kann es vorkommen das dieses verhalten deine IDE
oder deinen Debugger verwirrt. Dieses Rezept zeigt dir wie du diesen Caching Mechanismus
so freundlich wie möglich machen kannst, wenn du Code Debuggen musst in dem 
Symfony Klassen involviert sind.

Der ``app_dev.php`` Front Controller sieht standardmäßig so aus::

    // ...

    require_once __DIR__.'/../app/bootstrap.php.cache';
    require_once __DIR__.'/../app/AppKernel.php';

    use Symfony\Component\HttpFoundation\Request;

    $kernel = new AppKernel('dev', true);
    $kernel->loadClassCache();
    $kernel->handle(Request::createFromGlobals())->send();

Um deinen Debugger glücklicher zu machen, deaktiviere das komplette Caching von PHP Klassen indem du den
Aufruf zur Funktion ``loadClassCache()`` entfernst und durch das entfernen von require ausdrücken
wie unten gezeigt::

    // ...

    // require_once __DIR__.'/../app/bootstrap.php.cache';
    require_once __DIR__.'/../vendor/symfony/symfony/src/Symfony/Component/ClassLoader/UniversalClassLoader.php';
    require_once __DIR__.'/../app/autoload.php';
    require_once __DIR__.'/../app/AppKernel.php';

    use Symfony\Component\HttpFoundation\Request;

    $kernel = new AppKernel('dev', true);
    // $kernel->loadClassCache();
    $kernel->handle(Request::createFromGlobals())->send();

.. tip::

    Wenn du das PHP Caching deaktivierst, vergiss nicht dies rückgängig zu machen sobald du mit deiner
    Debug Sitzung fertig bist.

Einige IDEs mögen die Tatsache nicht, dass einige Klassen in verschiedenen Orten gespeichert
sind. Um dieses Problem zu vermeiden kannst du in deiner IDE einstellen, diese PHP
Cache Dateien zu ignorieren, oder du änderst die Extension die Symfony für diese Dateien verwendet::

    $kernel->loadClassCache('classes', '.php.cache');
