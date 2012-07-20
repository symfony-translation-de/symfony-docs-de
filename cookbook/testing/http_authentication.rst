.. index::
   single: Tests; HTTP Authentifizierung

Wie man HTTP Authentifizierung in einem funktionalen Test simuliert
===================================================================

Wenn deine Applikation HTTP Authentifizierung benötigt kannst du einfach Username und Passwort
als Server variabel der Funktion ``createClient()`` übergeben::

    $client = static::createClient(array(), array(
        'PHP_AUTH_USER' => 'username',
        'PHP_AUTH_PW'   => 'pa$$word',
    ));

Du kannst auch für jede Anfrage dies überschrieben::

    $client->request('DELETE', '/post/12', array(), array(
        'PHP_AUTH_USER' => 'username',
        'PHP_AUTH_PW'   => 'pa$$word',
    ));

Wenn deine Applikation ein ``form_login`` benutzt, kannst du deine Tests vereinfachen
indem du in deiner Test Konfiguration die Benutzung von HTTP Authentifizierung erlaubst. Auf
diesem weg kannst du die Methode von oben nehmen um dich in deinen Tests zu Authentifizieren, währenddessen deine User sich
normal über den ``form_login`` einloggen müssen. Der Trick dabei ist ``http_basic`` Schlüssel
in deiner Firewall Konfiguration neben dem ``form_login`` Schlüssel einzutragen:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config_test.yml
        security:
            firewalls:
                your_firewall_name:
                    http_basic:
