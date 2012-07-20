.. index::
   single: Request; Ein Request Format und Mime Type hinzufügen

Wie man ein neues Request Format und ein neuen Mime Type registriert
====================================================================

Jeder ``Request`` hat ein "format" (z.B. ``html``, ``json``), welches benutzt wird
um festzustellen welcher Typ des Contents im ``Response`` zurückgegeben wird. Tatsächlich,
kann das Request Format, abrufbar mit
:method:`Symfony\\Component\\HttpFoundation\\Request::getRequestFormat`,
wird benutzt um den MIME Type des ``Content-Type`` Headers des ``Response``
Objektes zu setzen. Intern enthält Symfony eine Map mit den meist gebräuchlichsten Formaten (z.B.
``html``, ``json``) und deren zugehörigen MIME Types (z.B. ``text/html``,
``application/json``). Natürlich können auch zusätzliche Format-MIME Type Einträge
leicht hinzugefügt werden. Dieses Dokument wird dir zeigen, wie man das ``jsonp`` Format
samt dem zugehörigen MIME Type hinzufügt.

Erstelle einen ``kernel.request`` Listener
------------------------------------------

Der Schlüssel um ein neuen MIME Type zu definieren ist es eine Klasse zu erstellen welche "zuhört" zum
``kernel.request`` Event welche vom Symfony Kernel ausgelöst wird. Das
``kernel.request`` Event wird früh im Symfony's Request Handling ausgelöst
und erlaubt es das Request Objekt zu verändern.

Erstelle die folgende Klasse und ersetze den Pfad durch einen Pfad zu einem Bundel in deinem
Projekt::

    // src/Acme/DemoBundle/RequestListener.php
    namespace Acme\DemoBundle;

    use Symfony\Component\HttpKernel\HttpKernelInterface;
    use Symfony\Component\HttpKernel\Event\GetResponseEvent;

    class RequestListener
    {
        public function onKernelRequest(GetResponseEvent $event)
        {
            $event->getRequest()->setFormat('jsonp', 'application/javascript');
        }
    }

Registriere deinen Listener
-------------------------

Wie bei jedem anderen Listener auch musst zu deinem Konfigurations Datei hinzufügen
und den Listener Registrieren indem du das ``kernel.event_listener`` Tag hinzufügst:

.. configuration-block::

    .. code-block:: xml

        <!-- app/config/config.xml -->
        <?xml version="1.0" ?>

        <container xmlns="http://symfony.com/schema/dic/services"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://symfony.com/schema/dic/services http://symfony.com/schema/dic/services/services-1.0.xsd">
            <services>
            <service id="acme.demobundle.listener.request" class="Acme\DemoBundle\RequestListener">
                <tag name="kernel.event_listener" event="kernel.request" method="onKernelRequest" />
            </service>
            </services>
        </container>

    .. code-block:: yaml

        # app/config/config.yml
        services:
            acme.demobundle.listener.request:
                class: Acme\DemoBundle\RequestListener
                tags:
                    - { name: kernel.event_listener, event: kernel.request, method: onKernelRequest }

    .. code-block:: php

        # app/config/config.php
        $definition = new Definition('Acme\DemoBundle\RequestListener');
        $definition->addTag('kernel.event_listener', array('event' => 'kernel.request', 'method' => 'onKernelRequest'));
        $container->setDefinition('acme.demobundle.listener.request', $definition);

An diesem Punkt ist der Service ``acme.demobundle.listener.request``
konfiguriert und wird informiert wenn der Symfony Kernel das
``kernel.request`` Event auslöst.

.. tip::

    Du kannst auch den Listener in einer Konfigurations Erweiterungs Klasse registrieren (siehe
    :ref:`service-container-extension-configuration` für mehr Informationen).
