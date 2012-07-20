.. index::
   single: Konsole; Kommando erstellen

Wie man ein Konsolen Kommando erstellt
======================================

Die Konsolen Seite im Komponenten Bereich (:doc:`/components/console`) erklärt
wie man Konsolen Kommandos erstellt. Dieser Kochbuch Artikel erklärt die Unterschiede
beim erstellen von Konsolen Kommandos im Symfony2 Framework.

Automatische Registrierung von Kommandos
----------------------------------------

Um ein Konsolen Kommando automatisch in Symfony2 verfügbar zu machen erstelle im
``Command`` Verzeichnis innerhalb deines Bundles eine PHP Datei welche mit 
``Command.php`` endet für jedes Kommando das du bereitstellen willst. Wenn du zum Beispiel
das Bundle ``AcmeDemoBundle`` erweitern möchtest (welches in der Symfony Standard
Version verfügbar ist). dass es uns von der Kommandozeile her grüßt, erstelle die Datei ``GreetCommand.php`` und
füge das Folgende ein::

    // src/Acme/DemoBundle/Command/GreetCommand.php
    namespace Acme\DemoBundle\Command;

    use Symfony\Bundle\FrameworkBundle\Command\ContainerAwareCommand;
    use Symfony\Component\Console\Input\InputArgument;
    use Symfony\Component\Console\Input\InputInterface;
    use Symfony\Component\Console\Input\InputOption;
    use Symfony\Component\Console\Output\OutputInterface;

    class GreetCommand extends ContainerAwareCommand
    {
        protected function configure()
        {
            $this
                ->setName('demo:greet')
                ->setDescription('Greet someone')
                ->addArgument('name', InputArgument::OPTIONAL, 'Who do you want to greet?')
                ->addOption('yell', null, InputOption::VALUE_NONE, 'If set, the task will yell in uppercase letters')
            ;
        }

        protected function execute(InputInterface $input, OutputInterface $output)
        {
            $name = $input->getArgument('name');
            if ($name) {
                $text = 'Hello '.$name;
            } else {
                $text = 'Hello';
            }

            if ($input->getOption('yell')) {
                $text = strtoupper($text);
            }

            $output->writeln($text);
        }
    }

Dieses Kommando wird nun automatisch für die Ausführung verfügbar sein:

.. code-block:: bash

    app/console demo:greet Fabien

Kommandos testen
----------------

Um ein Kommando zu testen welches teil des kompletten Frameworks ist sollte :class:`Symfony\\Bundle\\FrameworkBundle\\Console\\Application`
anstatt von :class:`Symfony\\Component\\Console\\Application` verwendet werden::

    use Symfony\Component\Console\Tester\CommandTester;
    use Symfony\Bundle\FrameworkBundle\Console\Application;
    use Acme\DemoBundle\Command\GreetCommand;

    class ListCommandTest extends \PHPUnit_Framework_TestCase
    {
        public function testExecute()
        {
            // mock the Kernel or create one depending on your needs
            $application = new Application($kernel);
            $application->add(new GreetCommand());

            $command = $application->find('demo:greet');
            $commandTester = new CommandTester($command);
            $commandTester->execute(array('command' => $command->getName()));

            $this->assertRegExp('/.../', $commandTester->getDisplay());

            // ...
        }
    }

Services vom Service Container bekommen
---------------------------------------

Wenn man :class:`Symfony\\Bundle\\FrameworkBundle\\Command\\ContainerAwareCommand` 
als Basis Klasse für das Kommando verwendet (anstelle der mehr grundlegenden Klasse 
:class:`Symfony\\Component\\Console\\Command\\Command`), hast du zugriff auf den
Service Container. Mit anderen worten du hast zugriff auf jeden Konfigurierten Service.
Zum Beispiel kannst du den Task leicht übersetzbar machen::

    protected function execute(InputInterface $input, OutputInterface $output)
    {
        $name = $input->getArgument('name');
        $translator = $this->getContainer()->get('translator');
        if ($name) {
            $output->writeln($translator->trans('Hello %name%!', array('%name%' => $name)));
        } else {
            $output->writeln($translator->trans('Hello!'));
        }
    }
