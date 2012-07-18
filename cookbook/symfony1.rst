.. index::
   single: symfony1

Wie unterscheidet sich Symfony2 von symfony1
============================================

Das Symfony2 Framework enthält eine signifikante Weiterentwicklung wenn man es
mit der ersten Version des Frameworks vergleicht. Glücklicherweise, durch die MVC Architektur
in seinem Kern, sind die Fertigkeiten die man für symfony1 Projekte gebraucht hat ziemlich
die selben sind um mit Symfony2 zu entwicklen. Klar, ``app.yml`` ist weg, aber
routing, controllers und templates bleiben alle erhalten.

In diesem Kapitel werden wir uns die unterschiede zwischen symfony1 und Symfony2 anschauen.
Wie du sehen wirst, sind einige Aufgaben ein wenig anderst gelöst. Du wirst
merken, dass diese kleinen Unterschiede Stabilität, Voraussagbarkeit,
Testbarkeit und entkoppelten Code in deine Symfony2 Applikation bringen.

Nun lehn dich zurück, den wir werden dich von "damals" bis zum "jetzt" führen.

Verzeichnisstruktur
-------------------

Wenn Du dir ein Symfony2 Projekt anschaust - zum Beispiel, dass `Symfony2 Standard`_ Projekt -
wirst du eine komplett andere Verzeichnisstruktur vorfinden im Gegensatz zu symfony1. Die
Unterschiede sind jedoch nur oberflächlich.

Das ``app/`` Verzeichnis
~~~~~~~~~~~~~~~~~~~~~~~~

In symfony1, hatte dein Projekt eine oder mehrere Applikationen, und jede lag im
``apps/`` Verzeichnis (z.B. ``apps/frontend``). Standardmäßig hat man in Symfony2
nur eine Applikation welche im ``app/`` Verzeichnis liegt. Wie
in symfony1 enthält das ``app/`` Verzeichnis die Konfiguration welche zur Applikation gehören.
Es enthält außerdem die Applikationen spezifischen cache, log und template
Verzeichnisse, genauso wie die ``Kernel`` Klasse (``AppKernel``), welches das Basis
Objekt der Applikation bildet.

Anders als in symfony1, gibt es keinen PHP code im ``app/`` Verzeichnis. Das
Verzeichnis ist nicht dafür ausgelegt Module oder Bibliotheksdateien zu enthalten wie es noch in symfony1 war.
Stattdessen ist es einfach nur das Zuhause für die Konfiguration und anderen Ressourcen (Templates, 
Übersetungsdateien).

Das ``src/`` Verzeichnis
~~~~~~~~~~~~~~~~~~~~~~~~

Um es einfach zu halten, hier kommt dein eigentlicher Code hinein. In Symfony2 lebt der komplette Applikationscode
in einem Bundel (ungefähr vergleichbar mit einem symfony1 Plugin) und Standardmäßig
befindet sich jedes Bundel im ``src`` Verzeichnis. Daher ist das ``src``
Verzeichnis ein wenig wie das ``plugins`` Verzeichnis in symfony1, nur sehr viel
flexibler. Da *Deine* Bundles im ``src/`` Verzeichnis zu finden sind,
sind Drittanbieter Bundles irgendwo im ``vendor/`` Verzeichnis zu finden.

Um ein besseres Bild vom ``src/`` Verzeichnis zu bekommen, lass uns zuerst an eine
symfony1 Applikation denken. Zuerst befindet sich dein Code in einer oder
mehreren Applikationen. Meistens enthalten diese Module, können aber auch
irgendwelche anderen PHP Klassen enthalten, welche du in deine Applikation gepackt hast. Du hast vielleicht auch
eine ``schema.yml`` Datei im ``config`` Verzeichnis deines Projektes angelegt sowie
mehrere Model Dateien. Zu guter Letzt, als Hilfe für gewöhnliche Funktionen, hast du
mehrere Drittanbieter Plugins benutzt, welche im ``plugins/`` Verzeichnis liegen.
Mit anderen Worten, der Code für deine Applikation lebt in vielen verschiedenen
Orten.

In Symfony2 ist das Leben deutlich einfacher, da der *komplette* Symfony2 Code in
einem Bundel leben muss. In unserem fiktiven symfony1 Projekt *kann* der gesamte Code
in ein oder mehrere Plugins (was außerdem eine sehr gute Arbeitsweise ist) verschoben werden. Angenommen,
das alle Module, PHP Klassen, Schema, Routing Konfiguration, usw.
in ein Plugin verschoben wurden, wird das symfony1 ``plugins/`` Verzeichnis ziemlich identisch
zum Symfony2 ``src/`` Verzeichnis.

Um es nochmal einfach zu sagen, dass ``src/`` Verzeichnis ist der Ort wo dein Code, Assets,
Templates und das meiste andere Projekt spezifische Sachen sich befinden.

Das ``vendor/`` Verzeichnis
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Das ``vendor/`` Verzeichnis ist  grundsätzlich das selbe wie das ``lib/vendor/``
Verzeichnis in symfony1, welches das übliche Verzeichnis für alle Drittanbieter Bibliotheken
und Bundles ist. Standardmäßig findest du alle Dateien der Symfony2 Bibliotheken in
diesem Verzeichnis. Neben vielen anderen abhängigen Bibliotheken wie Doctrine2,
Twig und Swiftmailer. Symfony2 Drittanbieter Bundles leben irgendwo im
``vendor/`` Verzeichnis.

Das ``web/`` Verzeichnis
~~~~~~~~~~~~~~~~~~~~~~~~

Es hat sich nicht viel im ``web/`` Verzeichnis geändert. Der auffallendste Unterschied
ist die Abwesenheit der ``css/``, ``js/`` and ``images/`` Verzeichnisse. Dies ist
bewusst. Genauso wie mit deinem PHP Code sollten alle Assets in einem
Bundle leben. Mit Hilfe eines Konsolen Kommandos wird das ``Resources/public/``
Verzeichnis von jedem Bundle nach ``web/bundles/`` Verzeichnis kopiert oder verknüpft.
Das erlaubt es alle Assets in deinem Bundle zu Organisieren, diese aber
trotzdem öffentlich zu Verfügung zu stellen. Um sicher zu gehen das alle Bundles
verfügbar sind, führe das folgende Kommando aus::

    php app/console assets:install web

.. note ::

   Dieses Kommando ist das Symfony2 äquivalent zu dem symfony1 Kommando ``plugin:publish-assets``.


Autoloading
-----------

One of the advantages of modern frameworks is never needing to worry about
requiring files. By making use of an autoloader, you can refer to any class
in your project and trust that it's available. Autoloading has changed in
Symfony2 to be more universal, faster, and independent of needing to clear
your cache.

In symfony1, autoloading was done by searching the entire project for the
presence of PHP class files and caching this information in a giant array.
That array told symfony1 exactly which file contained each class. In the
production environment, this caused you to need to clear the cache when classes
were added or moved.

In Symfony2, a new class - ``UniversalClassLoader`` - handles this process.
The idea behind the autoloader is simple: the name of your class (including
the namespace) must match up with the path to the file containing that class.
Take the ``FrameworkExtraBundle`` from the Symfony2 Standard Edition as an
example::

    namespace Sensio\Bundle\FrameworkExtraBundle;

    use Symfony\Component\HttpKernel\Bundle\Bundle;
    // ...

    class SensioFrameworkExtraBundle extends Bundle
    {
        // ...
    }

The file itself lives at
``vendor/sensio/framework-extra-bundle/Sensio/Bundle/FrameworkExtraBundle/SensioFrameworkExtraBundle.php``.
As you can see, the location of the file follows the namespace of the class.
Specifically, the namespace, ``Sensio\Bundle\FrameworkExtraBundle``, spells out
the directory that the file should live in
(``vendor/sensio/framework-extra-bundle/Sensio/Bundle/FrameworkExtraBundle/``).
This is because, in the ``app/autoload.php`` file, you'll configure Symfony to
look for the ``Sensio`` namespace in the ``vendor/sensio`` directory:

.. code-block:: php

    // app/autoload.php

    // ...
    $loader->registerNamespaces(array(
        ...,
        'Sensio'           => __DIR__.'/../vendor/sensio/framework-extra-bundle',
    ));

If the file did *not* live at this exact location, you'd receive a
``Class "Sensio\Bundle\FrameworkExtraBundle\SensioFrameworkExtraBundle" does not exist.``
error. In Symfony2, a "class does not exist" means that the suspect class
namespace and physical location do not match. Basically, Symfony2 is looking
in one exact location for that class, but that location doesn't exist (or
contains a different class). In order for a class to be autoloaded, you
**never need to clear your cache** in Symfony2.

As mentioned before, for the autoloader to work, it needs to know that the
``Sensio`` namespace lives in the ``vendor/bundles`` directory and that, for
example, the ``Doctrine`` namespace lives in the ``vendor/doctrine/orm/lib/``
directory. This mapping is entirely controlled by you via the
``app/autoload.php`` file.

If you look at the ``HelloController`` from the Symfony2 Standard Edition you
can see that it lives in the ``Acme\DemoBundle\Controller`` namespace. Yet, the
``Acme`` namespace is not defined in the ``app/autoload.php``. By default you
do not need to explicitly configure the location of bundles that live in the
``src/`` directory. The ``UniversalClassLoader`` is configured to fallback to
the ``src/`` directory using its ``registerNamespaceFallbacks`` method:

.. code-block:: php

    // app/autoload.php

    // ...
    $loader->registerNamespaceFallbacks(array(
        __DIR__.'/../src',
    ));

Using the Console
-----------------

In symfony1, befindet sich die Konsole in deinem Root Verzeichnis von deinem Projekt und
nennt sich ``symfony`` :

.. code-block:: text

    php symfony

In Symfony2 ist die Konsole jetzt im app Unterverzeichnis und nennt sich
``console``:

.. code-block:: text

    php app/console

Applications
------------

In a symfony1 project, it is common to have several applications: one for the
frontend and one for the backend for instance.

In a Symfony2 project, you only need to create one application (a blog
application, an intranet application, ...). Most of the time, if you want to
create a second application, you might instead create another project and
share some bundles between them.

And if you need to separate the frontend and the backend features of some
bundles, you can create sub-namespaces for controllers, sub-directories for
templates, different semantic configurations, separate routing configurations,
and so on.

Of course, there's nothing wrong with having multiple applications in your
project, that's entirely up to you. A second application would mean a new
directory, e.g. ``my_app/``, with the same basic setup as the ``app/`` directory.

.. tip::

    Read the definition of a :term:`Project`, an :term:`Application`, and a
    :term:`Bundle` in the glossary.

Bundles and Plugins
-------------------

In a symfony1 project, a plugin could contain configuration, modules, PHP
libraries, assets and anything else related to your project. In Symfony2,
the idea of a plugin is replaced by the "bundle". A bundle is even more powerful
than a plugin because the core Symfony2 framework is brought in via a series
of bundles. In Symfony2, bundles are first-class citizens that are so flexible
that even core code itself is a bundle.

In symfony1, a plugin must be enabled inside the ``ProjectConfiguration``
class::

    // config/ProjectConfiguration.class.php
    public function setup()
    {
        $this->enableAllPluginsExcept(array(... some plugins here));
    }

In Symfony2, the bundles are activated inside the application kernel::

    // app/AppKernel.php
    public function registerBundles()
    {
        $bundles = array(
            new Symfony\Bundle\FrameworkBundle\FrameworkBundle(),
            new Symfony\Bundle\TwigBundle\TwigBundle(),
            ...,
            new Acme\DemoBundle\AcmeDemoBundle(),
        );

        return $bundles;
    }

Routing (``routing.yml``) and Configuration (``config.yml``)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In symfony1, the ``routing.yml`` and ``app.yml`` configuration files were
automatically loaded inside any plugin. In Symfony2, routing and application
configuration inside a bundle must be included manually. For example, to
include a routing resource from a bundle called ``AcmeDemoBundle``, you can
do the following::

    # app/config/routing.yml
    _hello:
        resource: "@AcmeDemoBundle/Resources/config/routing.yml"

This will load the routes found in the ``Resources/config/routing.yml`` file
of the ``AcmeDemoBundle``. The special ``@AcmeDemoBundle`` is a shortcut syntax
that, internally, resolves to the full path to that bundle.

You can use this same strategy to bring in configuration from a bundle:

.. code-block:: yaml

    # app/config/config.yml
    imports:
        - { resource: "@AcmeDemoBundle/Resources/config/config.yml" }

In Symfony2, configuration is a bit like ``app.yml`` in symfony1, except much
more systematic. With ``app.yml``, you could simply create any keys you wanted.
By default, these entries were meaningless and depended entirely on how you
used them in your application:

.. code-block:: yaml

    # some app.yml file from symfony1
    all:
      email:
        from_address:  foo.bar@example.com

In Symfony2, you can also create arbitrary entries under the ``parameters``
key of your configuration:

.. code-block:: yaml

    parameters:
        email.from_address: foo.bar@example.com

You can now access this from a controller, for example::

    public function helloAction($name)
    {
        $fromAddress = $this->container->getParameter('email.from_address');
    }

In reality, the Symfony2 configuration is much more powerful and is used
primarily to configure objects that you can use. For more information, see
the chapter titled ":doc:`/book/service_container`".

.. _`Symfony2 Standard`: https://github.com/symfony/symfony-standard
