.. index::
   single: Tests; Doctrine

Wie man Doctrine Repositorys testet
===================================

Doctrine Repositorys mit Unit-tests zu testen is in einem Symfony Projekt nicht empfohlen.
Wen du mit einem Repository arbeitest, arbeitest du mit etwas,
das gegen eine richtige Datenbankverbindung getestet werden sollte.

Zum Glück kannst du sehr leicht deine Queries gegen eine echte Datenbank testen, wie
weiter unten Beschrieben.

.. _cookbook-doctrine-repo-functional-test:

Funktionales testen
-------------------

Um wirklich einen Query auszuführen, muss der Kernel gebootet werden,
um eine gültige Verbindung zu bekommen. In diesem fall, muss man die Klasse ``WebTestCase`` erweitern,
was dies alles sehr einfach macht::

    // src/Acme/StoreBundle/Tests/Entity/ProductRepositoryFunctionalTest.php
    namespace Acme\StoreBundle\Tests\Entity;

    use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;

    class ProductRepositoryFunctionalTest extends WebTestCase
    {
        /**
         * @var \Doctrine\ORM\EntityManager
         */
        private $em;

        public function setUp()
        {
            $kernel = static::createKernel();
            $kernel->boot();
            $this->em = $kernel->getContainer()->get('doctrine.orm.entity_manager');
        }

        public function testProductByCategoryName()
        {
            $results = $this->em
                ->getRepository('AcmeStoreBundle:Product')
                ->searchProductsByNameQuery('foo')
                ->getResult()
            ;

            $this->assertCount(1, $results);
        }
    }
