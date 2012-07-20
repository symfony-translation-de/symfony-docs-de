.. index::
   single: Profiling; Data collector

Wie man einen eigenen Data Collector erstellt
=============================================

Der Symfony2 :ref:`Profiler <internals-profiler>` delegiert das Datensammeln an
data collectors. Symfony2 kommt bereits mit einigen von ihnen mit, du kannst aber leicht
deinen eigenen erstellen.

Erstellen eines eigenen Data Collector
--------------------------------------

Das erstellen eines eigenen data collector ist sehr simpel da man nur das Interface
:class:`Symfony\\Component\\HttpKernel\\DataCollector\\DataCollectorInterface` implementieren muss::

    interface DataCollectorInterface
    {
        /**
         * Collects data for the given Request and Response.
         *
         * @param Request    $request   A Request instance
         * @param Response   $response  A Response instance
         * @param \Exception $exception An Exception instance
         */
        function collect(Request $request, Response $response, \Exception $exception = null);

        /**
         * Returns the name of the collector.
         *
         * @return string The collector name
         */
        function getName();
    }

Die ``getName()`` Funktion muss einen eindeutigen Namen zurück geben. Dieser wird benutzt um später zugriff
auf die Informationen zu bekommen (siehe :doc:`/cookbook/testing/profiling` als
Beispiel).

Die ``collect()`` funktion ist dafür verantwortlich die Daten zu speichern, welche man später
als lokale Eingenschaften verfügbar machen will.

.. caution::

    Da der Profiler die data collector Instanzen serialisiert solltest du keine
    Objekte speichern welche sich nicht serialisieren lassen (wie PDO Objekte) außer du
    lieferst deine eigene ``serialize()`` Funktion.

Die meiste Zeit ist es üblich die Klasse
:class:`Symfony\\Component\\HttpKernel\\DataCollector\\DataCollector` zu erweitern und
die variable ``$this->data`` zu veröffentlichen (dies passt auf das die variable
``$this->data`` serialisiert wird)::

    class MemoryDataCollector extends DataCollector
    {
        public function collect(Request $request, Response $response, \Exception $exception = null)
        {
            $this->data = array(
                'memory' => memory_get_peak_usage(true),
            );
        }

        public function getMemory()
        {
            return $this->data['memory'];
        }

        public function getName()
        {
            return 'memory';
        }
    }

.. _data_collector_tag:

Aktivieren des eigenen Data Collectors
--------------------------------------

Um einen data collector zu aktivieren muss dieser als regulärer Service in deine
Konfiguration aufgenommen werden und mit dem Tag ``data_collector`` versehen werden:

.. configuration-block::

    .. code-block:: yaml

        services:
            data_collector.your_collector_name:
                class: Fully\Qualified\Collector\Class\Name
                tags:
                    - { name: data_collector }

    .. code-block:: xml

        <service id="data_collector.your_collector_name" class="Fully\Qualified\Collector\Class\Name">
            <tag name="data_collector" />
        </service>

    .. code-block:: php

        $container
            ->register('data_collector.your_collector_name', 'Fully\Qualified\Collector\Class\Name')
            ->addTag('data_collector')
        ;

Adding Web Profiler Templates
-----------------------------

Wenn du die von deinem Data Collector gesammelten Daten in der Web
Debug Toolbar oder im Web Profiler darstellen willst erstelle ein Twig Template mit folgendem
Gerüst:

.. code-block:: jinja

    {% extends 'WebProfilerBundle:Profiler:layout.html.twig' %}

    {% block toolbar %}
        {# the web debug toolbar content #}
    {% endblock %}

    {% block head %}
        {# if the web profiler panel needs some specific JS or CSS files #}
    {% endblock %}

    {% block menu %}
        {# the menu content #}
    {% endblock %}

    {% block panel %}
        {# the panel content #}
    {% endblock %}

Jeder Block ist optional. Der ``toolbar`` Block wird benutzt für die Web Debug
Toolbar und ``menu`` und ``panel`` werden benutzt um ein Panel zum Web Profiler
hinzuzufügen.

Alle Blöcke haben zugriff auf das ``collector`` Objekt.

.. tip::

    Built-in Templates benutzen base64 encodierte Bilder für die Toolbar (``<img
    src="src="data:image/png;base64,..."``). Du kannst leicht den
    base64 wert für dein Image mit diesem kleinen Script berechnen: ``echo
    base64_encode(file_get_contents($_SERVER['argv'][1]));``.

Um das template zu aktiveren fürge ein ``template`` attribut zum ``data_collector``
Tag in deiner Konfiguration hinzu. Zum Beispiel angenommen dein Template ist im Bunel
``AcmeDebugBundle``:

.. configuration-block::

    .. code-block:: yaml

        services:
            data_collector.your_collector_name:
                class: Acme\DebugBundle\Collector\Class\Name
                tags:
                    - { name: data_collector, template: "AcmeDebug:Collector:templatename", id: "your_collector_name" }

    .. code-block:: xml

        <service id="data_collector.your_collector_name" class="Acme\DebugBundle\Collector\Class\Name">
            <tag name="data_collector" template="AcmeDebug:Collector:templatename" id="your_collector_name" />
        </service>

    .. code-block:: php

        $container
            ->register('data_collector.your_collector_name', 'Acme\DebugBundle\Collector\Class\Name')
            ->addTag('data_collector', array('template' => 'AcmeDebugBundle:Collector:templatename', 'id' => 'your_collector_name'))
        ;
