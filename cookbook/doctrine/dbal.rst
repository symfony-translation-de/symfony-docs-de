.. index::
   pair: Doctrine; DBAL

Wie nutze ich Doctrine's DBAL Layer
===================================

.. note::

    Dieser Artikel befasst sich mit Doctrine's DBAL layer. Meistens wirst du mit
    dem Doctrine ORM Layer arbeiten, welcher im Hintergrund den DBAL Layer, für
    die Kommunikation mit der Datenbank, nutzt. Für mehr Informationen über Doctrine
    ORM, siehe ":doc:`/book/doctrine`".

Der `Doctrine` _ Datenbank Abstraktions Layer (DBAL) ist ein Abstraktions Layer der
auf PDO baut. Er stellt eine intuitive und flexible API, für die Kommunkiation
zwischen den beliebtesten relationalen Datenbanken, bereit. In anderen Worten, die DBAL Bibelothek
macht es dir leicht Queries auszuführen und andere Datenbank Aktionen auszuführen.

.. tip::

    Lies die offiziele Doctrine `DBAL Dokumentation`_ um alle Details und Möglichkeiten
    zu erlernen.

Um zu starten, konfiguriere die Datenbankverbindungs Parameter:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config.yml
        doctrine:
            dbal:
                driver:   pdo_mysql
                dbname:   Symfony2
                user:     root
                password: null
                charset:  UTF8

    .. code-block:: xml

        // app/config/config.xml
        <doctrine:config>
            <doctrine:dbal
                name="default"
                dbname="Symfony2"
                user="root"
                password="null"
                driver="pdo_mysql"
            />
        </doctrine:config>

    .. code-block:: php

        // app/config/config.php
        $container->loadFromExtension('doctrine', array(
            'dbal' => array(
                'driver'    => 'pdo_mysql',
                'dbname'    => 'Symfony2',
                'user'      => 'root',
                'password'  => null,
            ),
        ));

Für die komplette DBAL Konfiguration schau in :ref:`reference-dbal-configuration` nach.


Du kannst mit dem Service ``database_connection`` auf die Doctrine DBAL
Verbindung zugreifen:

.. code-block:: php

    class UserController extends Controller
    {
        public function indexAction()
        {
            $conn = $this->get('database_connection');
            $users = $conn->fetchAll('SELECT * FROM users');

            // ...
        }
    }

Registriere Eigene Mapping Typen
Registering Custom Mapping Types
--------------------------------

Du kannst eigene Mapping Typen durch die Symfony's Konfiguration registrieren. Diese
werden dann allen konfigurierten Verbindungen hinzugefügt. Für mehr Informationen über
eigene Mapping Typen, lies die Doctrine's `Eigene Mapping Typen`_ Bereich, in Ihrer Dokumentation.

.. configuration-block::

    .. code-block:: yaml

        # app/config/config.yml
        doctrine:
            dbal:
                types:
                    custom_first: Acme\HelloBundle\Type\CustomFirst
                    custom_second: Acme\HelloBundle\Type\CustomSecond

    .. code-block:: xml

        <!-- app/config/config.xml -->
        <container xmlns="http://symfony.com/schema/dic/services"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:doctrine="http://symfony.com/schema/dic/doctrine"
            xsi:schemaLocation="http://symfony.com/schema/dic/services http://symfony.com/schema/dic/services/services-1.0.xsd
                                http://symfony.com/schema/dic/doctrine http://symfony.com/schema/dic/doctrine/doctrine-1.0.xsd">

            <doctrine:config>
                <doctrine:dbal>
                <doctrine:dbal default-connection="default">
                    <doctrine:connection>
                        <doctrine:mapping-type name="enum">string</doctrine:mapping-type>
                    </doctrine:connection>
                </doctrine:dbal>
            </doctrine:config>
        </container>

    .. code-block:: php

        // app/config/config.php
        $container->loadFromExtension('doctrine', array(
            'dbal' => array(
                'connections' => array(
                    'default' => array(
                        'mapping_types' => array(
                            'enum'  => 'string',
                        ),
                    ),
                ),
            ),
        ));

Registrieren von Eigenen Mapping Typen im SchemaTool
----------------------------------------------------

Das SchemaTool wird benutzt, zum Vergleichen des Datenbank Schema's. Zum
erreichen dieser Aufgabe, muss es wissen welcher Mapping Typ für jeden
Datenbank Typ eingesetzt werden muss. Neue Typen können über die Konfiguration
registriert werden.

Lass uns den ENUM Typ (wird standardmäßig nicht von DBAL unterstützt) zum ``String``
Mapping Typ hinzufügen.

.. configuration-block::

    .. code-block:: yaml

        # app/config/config.yml
        doctrine:
            dbal:
                connections:
                    default:
                        // Other connections parameters
                        mapping_types:
                            enum: string

    .. code-block:: xml

        <!-- app/config/config.xml -->
        <container xmlns="http://symfony.com/schema/dic/services"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:doctrine="http://symfony.com/schema/dic/doctrine"
            xsi:schemaLocation="http://symfony.com/schema/dic/services http://symfony.com/schema/dic/services/services-1.0.xsd
                                http://symfony.com/schema/dic/doctrine http://symfony.com/schema/dic/doctrine/doctrine-1.0.xsd">

            <doctrine:config>
                <doctrine:dbal>
                    <doctrine:type name="custom_first" class="Acme\HelloBundle\Type\CustomFirst" />
                    <doctrine:type name="custom_second" class="Acme\HelloBundle\Type\CustomSecond" />
                </doctrine:dbal>
            </doctrine:config>
        </container>

    .. code-block:: php

        // app/config/config.php
        $container->loadFromExtension('doctrine', array(
            'dbal' => array(
                'types' => array(
                    'custom_first'  => 'Acme\HelloBundle\Type\CustomFirst',
                    'custom_second' => 'Acme\HelloBundle\Type\CustomSecond',
                ),
            ),
        ));

.. _`PDO`:           http://www.php.net/pdo
.. _`Doctrine`:      http://www.doctrine-project.org
.. _`DBAL Dokumentation`: http://docs.doctrine-project.org/projects/doctrine-dbal/en/latest/index.html
.. _`Eigene Mapping Typen`: http://docs.doctrine-project.org/projects/doctrine-dbal/en/latest/reference/types.html#custom-mapping-types