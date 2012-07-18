.. index::
   single: Controller; Als Services

Wie definiere ich Controller als Services
=========================================

Im Buch hast du gelernt wie einfach ein Controller benutzt werden kann wenn man
die basis Klasse
:class:`Symfony\\Bundle\\FrameworkBundle\\Controller\\Controller` erweitert. Weil
dies so gut funktioniert, können Controller auch als Services festgelegt werden.

Um sich auf einen Controller zu beziehen welcher auch als Service definiert ist, benutze eine einzelne Doppelpunkt (:)
Schreibweise. Zum Beispiel, angenommen wir haben einen Service definiert welchen wir 
``my_controller`` genannt haben und wir wollen auf den Methoden aufruf ``indexAction()`` weiterleiten
innerhalb des Services::

    $this->forward('my_controller:indexAction', array('foo' => $bar));

Du musst auch die selbe Notation beim definieren des ``_controller`` werts
benutzen:

.. code-block:: yaml

    my_controller:
        pattern:   /
        defaults:  { _controller: my_controller:indexAction }

Um einen Controller auf diese weise zu benutzen muss dieser im Servicecontainer
konfiguriert werden. Für mehr Informationen, siehe das :doc:`Service Container
</book/service_container>` Kapitel.

Wenn du einen Controller als Service benutzen willst, erweitert dieser meistens nicht
die Basis ``Controller`` Klasse. Anstatt auf die Shortcut Methoden sich zu verlassen,
interagierst du direkt mit den benötigten Services. Zum Glück ist dies,
normalerweise echt einfach und die ``Controller`` Klasse selbst ist eine tolle Quelle
wie man die meisten gewöhnlichen Tasks durchführt.

.. note::

    Einen Controller als Service Task zu definieren benötigt ein wenig mehr Arbeit. Der
    hauptsächliche Vorteil ist das der komplette Controller oder egal welcher Service welcher dem
    Controller übergeben wird, einfach über den Service Container Konfiguriert werden kann.
    Dies ist besonders nützlich wenn man ein Open-Source Bundle oder ein Bundle Entwickelt
    welches in vielen verschiedenen Projekten eingesetzt wird. selbst wenn du selbst nicht
    deine Controller als Services spezifizierst, wirst du dies oft in vielen
    Open-Source Symfony2 Bundles vorfinden.
