.. index::
   single: Tests; Profiling

Wie man den Profiler in einem funktionalen Test verwendet
=========================================================

Ist wird sehr empfohlen das ein funktionaler Test nur die Antwort testet. Aber wenn
du funktionale Tests schreibst um den Produktiven Server zu überwachen, möchtest du 
Tests schreiben welche auf den Profilingdaten basieren, da diese eine gute Möglichkeit bieten
verschiedene Dinge zu testen und einige Messungen durchzuführen.

Der Symfony2 :ref:`Profiler <internals-profiler>` sammelt viele Informationen über
jede Anfrage. Benutze diese Daten um die Anzahl der Datenbank aufrufe zu überprüfen, die zeit
die das Framework gebraucht hat, ... Aber bevor du Überprüfungen schreibst, überprüfe immer
ob der Profiler wirklich verfügbar ist (er ist Standardmäßig in der ``test`` Umgebung aktiviert
)::

    class HelloControllerTest extends WebTestCase
    {
        public function testIndex()
        {
            $client = static::createClient();
            $crawler = $client->request('GET', '/hello/Fabien');

            // ... write some assertions about the Response

            // Check that the profiler is enabled
            if ($profile = $client->getProfile()) {
                // check the number of requests
                $this->assertLessThan(10, $profile->getCollector('db')->getQueryCount());

                // check the time spent in the framework
                $this->assertLessThan(500, $profile->getCollector('time')->getTotalTime());
            }
        }
    }

Wenn ein Test aufgrund der Profilingdaten fehlschlägt (zum Beispiel wegen zu vielen DB Querys),
möchtest du wahrscheinlich den Web Profiler nutzen um die Anfrage nach beenden des Tests genauer zu analysieren
. Dies ist leicht zu erreichen wenn du den Token in die Fehlermeldung einbindest::

    $this->assertLessThan(
        30,
        $profile->get('db')->getQueryCount(),
        sprintf('Checks that query count is less than 30 (token %s)', $profile->getToken())
    );

.. caution::

     Der Profiler Speicher kann abhängig von ihrer Umgebung unterschiedlich sein
     (vor allem wenn du den SQLite Speicher verwendest, welcher der Standardmäßig konfigurierte
     ist).

.. note::

    Die Profiler Information sind auch verfügbar solltest du den Client isolieren oder
    wenn du einen HTTP Layer für deine Tests verwendest.

.. tip::

    lese die API Dokumentation für eingebaute :doc:`data collectors</cookbook/profiler/data_collector>`
    um mehr über deren Interfaces herauszufinden.
