Email
#####

.. php:namespace:: Cake\Mailer

.. php:class:: Email(mixed $profile = null)

``Email`` est une nouvelle classe pour envoyer des emails. Avec cette classe,
vous pouvez envoyer des emails depuis n'importe endroit de votre application.

Utilisation basique
===================

Premièrement, vous devez vous assurer que la classe est chargée::

    use Cake\Mailer\Email;

Après avoir chargé ``Email``, vous pouvez envoyer un email avec ce qui suit::

    $email = new Email('default');
    $email->setFrom(['moi@example.com' => 'Mon Site'])
        ->setTo('toi@example.com')
        ->setSubject('À propos')
        ->send('Mon message');

Puisque les méthodes de setter d'``Email`` retournent l'instance de la classe,
vous pouvez définir ses propriétés avec le chaînage des méthodes.

``Email`` comporte plusieurs méthodes pour définir les destinataires -
``setTo()``, ``setCc()``, ``setBcc()``, ``addTo()``, ``addCc()`` et
``addBcc()``. La principale
différence est que les trois premières méthodes vont réinitialiser ce qui était
déjà défini et les suivantes vont ajouter plus de destinataires dans leur champs
respectifs::

    $email = new Email();
    $email->setTo('to@example.com', 'To Example');
    $email->addTo('to2@example.com', 'To2 Example');
    // Les destinataires de l'email sont: to@example.com et to2@example.com
    $email->setTo('test@example.com', 'ToTest Example');
    // Le destinataire de l'email est: test@example.com

Choisir l'émetteur
------------------

Quand on envoie des emails de la part d'autre personne, c'est souvent une
bonne idée de définir l'émetteur original en utilisant le header Sender.
Vous pouvez faire ceci en utilisant ``setSender()``::

    $email = new Email();
    $email->setSender('app@example.com', 'MyApp emailer');

.. note::

    C'est aussi une bonne idée de définir l'enveloppe de l'émetteur quand on
    envoie un mail de la part d'une autre personne. Cela les empêche d'obtenir
    tout message sur la délivrance.

.. _email-configuration:

Configuration
=============

Les configurations des profiles du Mailer et du transport d'emails sont définies
dans les fichiers de configuration de votre application. Les clés ``Email`` et
``EmailTransport`` définissent respectivement les configurations des profiles du
mailer et du transport des emails. Pendant le bottstrap de l'application, les
paramètres de configuration sont passés de la classe ``Configure`` aux classes
``Mailer`` et ``TransportFactory`` en utilisant ``setConfig()``. En définissant
des profiles et des transports, vous pouvez libérer le code de votre application
de toutes données de configuration, et éviter des duplications de code qui
rendent plus difficiles la maintenance et le déploiement.

Pour charger une configuration prédéfinie, vous pouvez utiliser la méthode
``setProfile()`` ou la passer au constructeur d'``Email``::

    $email = new Email();
    $email->setProfile('default');

    //ou dans le constructeur::
    $email = new Email('default');

Plutôt que de passer une chaîne avec le bon nom de configuration prédéfini,
vous pouvez aussi juste charger un tableau d'options::

    $email = new Email();
    $email->setProfile(['from' => 'me@example.org', 'transport' => 'my_custom']);

    // ou dans le constructeur::
    $email = new Email(['from' => 'me@example.org', 'transport' => 'my_custom']);

.. _email-configurations:

Profiles de Configurations
--------------------------

Définir des profiles de délivrance vous permet d'ajouter les configurations
habituelles d'email dans des profiles réutilisables. Votre application peut
avoir autant de profiles que nécessaire. Les clés de configuration suivantes
sont utilisées:

- ``'from'``: Email ou un tableau d'emmeteur. Regardez ``Email::setFrom()``.
- ``'sender'``: Email ou un tableau d'émetteur réel. Regardez
  ``Email::setSender()``.
- ``'to'``: Email ou un tableau de destination. Regardez ``Email::setTo()``.
- ``'cc'``: Email ou un tableau de copy carbon. Regardez ``Email::setCc()``.
- ``'bcc'``: Email ou un tableau de copy carbon blind. Regardez
  ``Email::setBcc()``.
- ``'replyTo'``: Email ou un tableau de répondre à cet e-mail. Regardez
  ``Email::setReplyTo()``.
- ``'readReceipt'``: Adresse Email ou un tableau d'adresses pour recevoir un
  récepissé de lecture. Regardez ``Email::readReceipt()``.
- ``'returnPath'``: Adresse Email ou un tableau des adresses à retourner si
  vous avez une erreur. Regardez ``Email::setReturnPath()``.
- ``'messageId'``: ID du Message de l'e-mail. Regardez
  ``Email::setMessageId()``.
- ``'subject'``: Sujet du message. Regardez ``Email::setSubject()``.
- ``'message'``: Contenu du message. Ne définissez pas ce champ si vous
  utilisez un contenu rendu.
- ``'priority'``: Priorité de l'email, exprimée avec un nombre (généralement de
  1 à 5, 1 étant la priorité la plus haute).
- ``'headers'``: Headers à inclure. Regardez ``Email::setHeaders()``.
- ``'viewRender'``: Si vous utilisez un contenu rendu, définissez le nom de
  classe de la vue. Regardez ``Email::viewRenderer()``.
- ``'template'``: Si vous utilisez un contenu rendu, définissez le nom du
  template. Regardez ``Email::template()``.
- ``'theme'``: Theme utilisé pour le rendu du template. Voir
  ``Email::setTheme()``.
- ``'layout'``: Si vous utilisez un contenu rendu, définissez le layout à
  rendre. Si vous voulez rendre un template sans layout, définissez ce champ
  à null. Regardez ``Email::template()``.
- ``'viewVars'``: Si vous utilisez un contenu rendu, définissez le tableau avec
  les variables devant être rendus dans la vue. Regardez
  ``Email::viewVars()``.
- ``'attachments'``: Liste des fichiers à attacher. Regardez
  ``Email::attachments()``.
- ``'emailFormat'``: Format de l'email (html, text ou both). Regardez
  ``Email::setEmailFormat()``.
- ``'transport'``: Nom du Transport. Regardez
  :php:meth:`~Cake\\Mailer\\Email::configTransport()`.
- ``'log'``: Niveau de Log pour connecter les headers de l'email headers et le
  message. ``true`` va utiliser LOG_DEBUG. Regardez aussi :ref:`logging-levels`.
- ``'helpers'``: Tableau de helpers utilisés dans le template email.
  ``ViewBuilder::setHelpers()``/``ViewBuilder::addHelpers()``

Toutes ces configurations sont optionnelles, excepté ``'from'``.

.. note::

    Les valeurs des clés ci-dessus utilisant Email ou un tableau, comme from,
    to, cc etc... seront passées en premier paramètre des méthodes
    correspondantes. L'equivalent pour
    ``Email::setFrom('my@example.com', 'My Site')`` sera défini comme
    ``'from' => ['my@example.com' => 'My Site']`` dans votre config.

Définir les Headers
-------------------

Dans ``Email``, vous êtes libre de définir les headers que vous souhaitez.
Si vous migrez pour utiliser Email, n'oubliez pas de mettre le préfixe
``X-`` dans vos headers.

Regardez ``Email::setHeaders()`` et ``Email::addHeaders()``

Envoyer les Emails Templatés
----------------------------

Les Emails sont souvent bien plus que de simples message textes. Afin de
faciliter cela, CakePHP fournit une façon d'envoyer les emails en utilisant la
:doc:`view layer </views>` de CakePHP.

Les templates pour les emails se placent dans un dossier spécial appelé
``Email`` dans le répertoire ``Template`` de votre application. Les templates
des emails peuvent aussi utiliser les layouts et éléments tout comme les
templates normales::

    $email = new Email();
    $email
        ->setTemplate('welcome', 'fancy')
        ->setEmailFormat('html')
        ->setTo('bob@example.com')
        ->setFrom('app@domain.com')
        ->send();

Ce qui est au-dessus utilise **templates/email/html/welcome.php** pour la
vue, et **templates/layout/email/html/fancy.php** pour le layout. Vous pouvez
aussi envoyer des messages email templaté multipart::

    $email = new Email();
    $email
        ->setTemplate('welcome', 'fancy')
        ->setEmailFormat('both')
        ->setTo('bob@example.com')
        ->setFrom('app@domain.com')
        ->send();

Ceci utiliserait les fichiers de template suivants:

* **templates/email/text/welcome.php**
* **templates/layout/email/text/fancy.php**
* **templates/email/html/welcome.php**
* **templates/layout/email/html/fancy.php**

Quand on envoie les emails templatés, vous avez la possibilité d'envoyer soit
``text``, ``html`` soit ``both``.

Vous pouvez définir des variables de vue avec ``Email::viewVars()``::

    $email = new Email('templated');
    $email->viewVars(['value' => 12345]);

Dans votre email template, vous pouvez utiliser ceux-ci avec::

    <p>Ici est votre valeur: <b><?= $value; ?></b></p>

Vous pouvez aussi utiliser les helpers dans les emails, un peu comme vous
pouvez dans des fichiers de template normaux. Par défaut, seul
:php:class:`HtmlHelper` est chargé. Vous pouvez chargez des helpers
supplémentaires en utilisant la méthode ``ViewBuilder::addHelpers()``::

    $email->viewBuilder()->addHelpers(['Html', 'Custom', 'Text']);

Quand vous ajoutez des helpers, assurez vous d'inclure 'Html' ou il sera
retiré des helpers chargés dans votre template d'email.

.. note::
    Dans les versions antérieures à 4.3.0, vous deviez utilisez ``setHelpers()``
    à la place.

Si vous voulez envoyer un email en utilisant templates dans un plugin, vous
pouvez utiliser la :term:`syntaxe de plugin` familière pour le faire::

    $email = new Email();
    $email->setTemplate('Blog.new_comment', 'Blog.auto_message');

Ce qui est au-dessus utiliserait les templates à partir d'un plugin de Blog par
exemple.

Dans certains cas, vous devez remplacer le template par défaut fourni par
les plugins. Vous pouvez faire ceci en utilisant les themes en disant à Email
d'utiliser le bon theme en utilisant la méthode ``Email::theme()``::

    $email = new Email();
    $email->setTemplate('Blog.new_comment', 'Blog.auto_message');
    $email->setTheme('TestTheme');

Ceci vous permet de remplacer le template `new_comment` dans votre theme sans
modifier le plugin Blog. Le fichier de template devra être créé dans le
chemin suivant:
**templates/plugin/TestTheme/plugin/Blog/email/text/new_comment.php**.

Envoyer les pièces jointes
==========================

.. php:method:: setAttachments($attachments = null)

Vous pouvez aussi attacher des fichiers aux messages d'email. Il y a quelques
formats différents qui dépendent de quel type de fichier vous avez, et comment
vous voulez que les noms de fichier apparaissent dans le mail de réception du
client:

1. Chaîne de caractères: ``$email->setAttachments('/full/file/path/file.png')`` va
   attacher ce fichier avec le nom file.png.
2. Tableau: ``$email->setAttachments(['/full/file/path/file.png'])`` aura le
   même comportement qu'en utilisant une chaîne de caractères.
3. Tableau avec clé:
   ``$email->setAttachments(['photo.png' => '/full/some_hash.png'])`` va
   attacher some_hash.png avec le nom photo.png. Le récipiendaire va voir
   photo.png, pas some_hash.png.
4. Tableaux imbriqués::

    $email->setAttachments([
        'photo.png' => [
            'file' => '/full/some_hash.png',
            'mimetype' => 'image/png',
            'contentId' => 'my-unique-id'
        ]
    ]);

   Ce qui est au-dessus va attacher le fichier avec différent mimetype et avec
   un content ID personnalisé (Quand vous définissez le content ID, la pièce
   jointe est transformée en inline). Le mimetype et contentId sont optionnels
   dans ce formulaire.

   4.1. Quand vous utilisez ``contentId``, vous pouvez utiliser le fichier dans
   corps HTML comme ``<img src="cid:my-content-id">``.

   4.2. Vous pouvez utiliser l'option ``contentDisposition`` pour désactiver le
   header ``Content-Disposition`` pour une pièce jointe. C'est utile pour
   l'envoi d'invitations ical à des clients utilisant outlook.

   4.3 Au lieu de l'option ``file``, vous pouvez fournir les contenus de
   fichier en chaîne en utilisant l'option ``data``. Cela vous permet
   d'attacher les fichiers sans avoir besoin de chemins de fichier vers eux.

.. deprecated:: 3.4.0
    Utilisez ``setAttachments()`` à la place de ``attachments()``.

Utiliser les Transports
=======================

Les Transports sont des classes destinées à envoyer l'email selon certain
protocoles ou méthodes. CakePHP supporte les transports Mail (par défaut),
Debug et SMTP.

Pour configurer votre méthode, vous devez utiliser la méthode
:php:meth:`Cake\\Mailer\\Email::transport()` ou avoir le transport dans
votre configuration::

    $email = new Email();

    // Use a named transport already configured using Email::setConfigTransport()
    $email->setTransport('gmail');

    // Use a constructed object.
    $transport = new DebugTransport();
    $email->setTransport($transport);

Créer des Transports Personnalisés
----------------------------------

Vous pouvez créer vos transports personnalisés pour intégrer avec d'autres
systèmes email (comme SwiftMailer). Pour créer votre transport, créez tout
d'abord le fichier **src/Mailer/Transport/ExampleTransport.php** (où
Exemple est le nom de votre transport). Pour commencer, votre fichier devrait
ressembler à cela::

    namespace App\Mailer\Transport;

    use Cake\Mailer\AbstractTransport;
    use Cake\Mailer\Email;

    class ExampleTransport extends AbstractTransport
    {
        public function send(Email $email)
        {
            // Logique d'exécution
        }
    }

Vous devez intégrer la méthode ``send(Email $email)`` avec votre
logique personnalisée. En option, vous pouvez intégrer la méthode
``config($config)``. ``config()`` est appelée avant send() et vous permet
d'accepter les configurations de l'utilisateur. Par défaut, cette méthode
met la configuration dans l'attribut protégé ``$_config``.

Si vous avez besoin d'appeler des méthodes supplémentaires sur le transport
avant l'envoi, vous pouvez utiliser
:php:meth:`Cake\\Mailer\\Email::transportClass()` pour obtenir une
instance du transport. Exemple::

    $yourInstance = $email->setTransport('your')->transportClass();
    $yourInstance->myCustomMethod();
    $email->send();

Faciliter les Règles de Validation des Adresses
-----------------------------------------------

.. php:method:: setEmailPattern($pattern = null)

Si vous avez des problèmes de validation lors de l'envoi vers des adresses
non conformes, vous pouvez faciliter le patron utilisé pour valider les
adresses email. C'est parfois nécessaire quand il s'agit de certains
ISP Japonais::

    $email = new Email('default');

    // Relax le patron d'email, ainsi vous pouvez envoyer
    // vers des adresses non conformes
    $email->setEmailPattern($newPattern);

Envoyer des Messages Rapidement
===============================

Parfois vous avez besoin d'une façon rapide d'envoyer un email, et vous n'avez
pas particulièrement envie en même temps de définir un tas de configuration.
:php:meth:`Cake\\Mailer\\Email::deliver()` est présent pour ce cas.

Vous pouvez créer votre configuration dans
:php:meth:`Cake\\Mailer\\Email::config()`, ou utiliser un
tableau avec toutes les options dont vous aurez besoin et utiliser
la méthode statique ``Email::deliver()``.
Exemple::

    Email::deliver('you@example.com', 'Subject', 'Message', ['from' => 'me@example.com']);

Cette méthode va envoyer un email à you@example.com, à partir de me@example.com
avec le sujet Subject et le contenu Message.

Le retour de ``deliver()`` est une instance de :php:class:`Cake\\Mailer\\Email`
avec l'ensemble des configurations. Si vous ne voulez pas envoyer l'email
maintenant, et souhaitez configurer quelques trucs avant d'envoyer, vous pouvez
passer le 5ème paramètre à ``false``.

Le 3ème paramètre est le contenu du message ou un tableau avec les variables
(quand on utilise le contenu rendu).

Le 4ème paramètre peut être un tableau avec les configurations ou une chaîne de
caractères avec le nom de configuration dans ``Configure``.

Si vous voulez, vous pouvez passer les to, subject et message à null et faire
toutes les configurations dans le 4ème paramètre (en tableau ou en utilisant
``Configure``).
Vérifiez la liste des :ref:`configurations <email-configurations>` pour voir
toutes les configs acceptées.

Envoyer des Emails depuis CLI
=============================

Quand vous envoyez des emails à travers un script CLI (Shells, Tasks, ...),
vous devez définir manuellement le nom de domaine que Email doit utiliser.
Il sera utilisé comme nom d'hôte pour l'id du message (puisque il n'y a pas
de nom d'hôte dans un environnement CLI)::

    $email->setDomain('www.example.org');
    // Resulte en ids de message comme ``<UUID@www.example.org>`` (valid)
    // au lieu de `<UUID@>`` (invalid)

Un id de message valide peut permettre à ce message de ne pas finir dans un
dossier de spam.

Créer des emails réutilisables
==============================

Les ``Mailers`` vous permettent de créer des emails réutilisables pour votre
application. Ils peuvent aussi servir à contenir plusieurs configurations
d'emails en un seul et même endroit. Cela vous permet de garder votre code
DRY ainsi que la configuration d'emails en dehors des autres parties
constituant votre application.

Dans cet exemple, vous allez créer un ``Mailer`` qui contient des emails liés
aux utilisateurs. Pour créer votre ``UserMailer``, créez un fichier
**src/Mailer/UserMailer.php**. Le contenu de ce fichier devra ressembler à ceci::

    namespace App\Mailer;

    use Cake\Mailer\Mailer;

    class UserMailer extends Mailer
    {
        public function welcome($user)
        {
            $this
                ->setTo($user->email)
                ->setSubject(sprintf('Welcome %s', $user->name))
                ->setTemplate('welcome_mail', 'custom'); // Par défaut le template avec le même nom que le nom de la méthode est utilisé.
        }

        public function resetPassword($user)
        {
            $this
                ->setTo($user->email)
                ->setSubject('Reset password')
                ->set(['token' => $user->token]);
        }
    }

Dans notre exemple, nous avons créé deux méthodes, une pour envoyer l'email de
bienvenue et l'autre pour envoyer un email de réinitialisation de mot de passe.
Chacune de ces méthodes prend une ``Entity`` ``User`` et utilise ses propriétés
pour configurer chacun des emails.

Vous pouvez maintenant utiliser votre ``UserMailer`` pour envoyer tous les
emails liés aux utilisateurs depuis n'importe où dans l'application. Par
exemple, si vous souhaitez envoyer l'email de bienvenue, vous pouvez faire la
chose suivante::

    namespace App\Controller;

    use Cake\Mailer\MailerAwareTrait;

    class UsersController extends AppController
    {
        use MailerAwareTrait;

        public function register()
        {
            $user = $this->Users->newEntity();
            if ($this->request->is('post')) {
                $user = $this->Users->patchEntity($user, $this->request->getData())
                if ($this->Users->save($user)) {
                    $this->getMailer('User')->send('welcome', [$user]);
                }
            }
            $this->set('user', $user);
        }
    }

Si vous voulez complétement séparer l'envoi de l'email de bienvenue du code de
l'application, vous pouvez utiliser votre ``UserMailer`` via l'évènement
``Model.afterSave``. En utilisant un évènement, vous pouvez complètement
séparer la logique d'envoi d'emails du reste de votre logique "utilisateurs".
Vous pourriez par exemple ajouter ce qui suit à votre ``UserMailer``::

    public function implementedEvents()
    {
        return [
            'Model.afterSave' => 'onRegistration'
        ];
    }

    public function onRegistration(Event $event, EntityInterface $entity, ArrayObject $options)
    {
        if ($entity->isNew()) {
            $this->send('welcome', [$entity]);
        }
    }

L'objet mailer serait ainsi enregistré en tant qu'écouteur (listeners)
d'événement et la méthode ``onRegistration()`` serait appellée à chaque fois
que l'événement ``Model.afterSave`` serait déclenché. Plus d'information sur
comment enregistrer des objets écouteurs d'événements sont disponibles dans la
documentation :ref:`registering-event-listeners`.

.. meta::
    :title lang=fr: Email
    :keywords lang=fr: envoyer mail,email emmetteur sender,envelope sender,classe php,database configuration,sending emails,meth,shells,smtp,transports,attributes,array,config,flexibilité,php email,nouvel email,sending email,models
