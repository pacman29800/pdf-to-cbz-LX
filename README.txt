pdf-to-cbz-LX
=============

Convertisseur PDF → CBZ (avec interface graphique YAD)

Un outil Linux simple pour convertir vos BD au format PDF vers des fichiers CBZ, sans fioritures,
basé sur des outils standards et l'esprit KISS.

À propos
--------

pdf-to-cbz-LX est un script Bash permettant de convertir un ou plusieurs fichiers PDF 
de bande dessinée en CBZ (Comic Book ZIP), en utilisant uniquement des outils de base
disponibles sur Linux.

Ce projet a été créé par un bidouilleur (moi !), pas un développeur pro.
J’ai utilisé ChatGPT pour le concevoir, dans l’idée de me faciliter la vie... et j’ai décidé 
de le partager à la communauté, sans prétention.

Il est :
- Fonctionnel
- Simple
- Fiable
- Sans dépendances lourdes
- Utilisable tel quel (script seul) ou via un paquet .deb

Fonctionnalités
---------------

- Interface graphique (YAD)
- Conversion PDF → CBZ
- Choix du format image : JPEG, PNG, TIFF
- Réglage de la résolution (DPI)
- Suppression des PDF originaux (optionnelle)
- Log enregistrable à la fin
- Utilise uniquement des outils standard Linux
- Philosophie KISS : Keep It Simple, Stupid

Installation
------------

Option 1 : Utilisation directe (sans installation)
--------------------------------------------------

1. Rends le script exécutable :

   chmod +x pdf-to-cbz-LX.sh

2. Lance-le :

   ./pdf-to-cbz-LX.sh

Option 2 : Installation avec le paquet .deb
-------------------------------------------

Un paquet Debian est fourni dans le dépôt (pdf-to-cbz-LX.deb).

Pour l’installer :

   sudo dpkg -i pdf-to-cbz-LX.deb

Ensuite, tu peux le lancer via le menu ou en ligne de commande :

   pdf-to-cbz-LX

Dépendances
-----------

Le script utilise :

- yad
- pdftoppm
- pdfinfo
- zip

À installer si nécessaire (une vérification est faite dans le script) :

   sudo apt install yad poppler-utils zip

Utilisation
-----------

Tu peux lancer le script de deux façons :

1. Sans arguments :

   pdf-to-cbz-LX

2. Avec fichiers PDF en argument :

   pdf-to-cbz-LX fichier1.pdf fichier2.pdf

Interface graphique
-------------------

L’interface permet de :
- Choisir les fichiers PDF
- Sélectionner le format image (jpeg, png, tiff)
- Régler la résolution (DPI, par défaut 200)
- Choisir le dossier de sortie
- Activer la suppression des PDF d’origine
- Suivre la progression
- Obtenir un résumé de conversion
- Sauvegarder un fichier log

Performances
------------

- Le script n’utilise qu’un seul cœur CPU
- Exemple : ~28 minutes pour 50 pages en PNG (200 DPI)
- JPEG : conversion rapide
- PNG : plus lent
- TIFF : non testé (très lourd)

Pourquoi ce projet ?
--------------------

Je cherchais un convertisseur PDF → CBZ :
- sans dépendances lourdes
- sans Python
- sans interface compliquée
- sans services en ligne douteux

Alors je l’ai fait moi-même, pour le plaisir.

Ce projet suit l’idée KISS : Keep It Simple, Stupid.

Contribuer
----------

Je suis ouvert aux contributions ! Voici quelques idées :

- Ajout du multi-thread
- Amélioration des performances
- Support d'autres formats image
- Nettoyage du code
- Ajout de traductions (i18n)...

Pull requests bienvenues !

Licence
-------

Licence MIT

Ce projet est libre. Tu peux l’utiliser, le modifier, le distribuer.

Remerciements
-------------

- La communauté Linux
- Les créateurs de YAD et Poppler
- ChatGPT pour l’aide à la création du script
- Toutes les personnes qui aiment les outils simples

Merci !!!

Utilise-le, partage-le, améliore-le si tu veux !

Désinstallation
---------------

Pour désinstaller sans supprimer les fichiers de configuration :

   sudo dpkg -r pdf-to-cbz-LX

Pour purger et supprimer aussi les fichiers de configuration :

   sudo dpkg --purge pdf-to-cbz-LX

=====================================================================
pdf-to-cbz-LX
=============

PDF to CBZ Converter (with YAD graphical interface)

A simple Linux tool to convert your comic books in PDF format into CBZ files,
based on standard Linux tools and the KISS philosophy (Keep It Simple, Stupid).

About
-----

pdf-to-cbz-LX is a Bash script that converts one or more comic book PDFs
into CBZ (Comic Book ZIP) format, using only basic and widely available Linux tools.

This project was made by a tinkerer (me!), not a professional developer.
I created it with the help of ChatGPT, to make my own life easier — and I decided
to share it with the community, with no pretension.

It is:
- Functional
- Simple
- Reliable
- Without heavy dependencies
- Usable as-is (standalone script) or through a `.deb` package

Features
--------

- Graphical interface (YAD)
- PDF → CBZ conversion
- Choose image format: JPEG, PNG, TIFF
- Set image resolution (DPI)
- Optionally delete original PDF files
- Saveable log file at the end
- Uses only standard Linux tools
- Follows the KISS philosophy: Keep It Simple, Stupid

Installation
------------

Option 1: Run directly (no installation)
----------------------------------------

1. Make the script executable:

   chmod +x pdf-to-cbz-LX.sh

2. Run it:

   ./pdf-to-cbz-LX.sh

Option 2: Install the .deb package
----------------------------------

A Debian package is provided in the repository (pdf-to-cbz-LX.deb).

To install it:

   sudo dpkg -i pdf-to-cbz-LX.deb

Then run it via your app menu or command line:

   pdf-to-cbz-LX

Dependencies
------------

The script uses:

- yad
- pdftoppm
- pdfinfo
- zip

Install them if needed (the script will check for them):

   sudo apt install yad poppler-utils zip

Usage
-----

You can run the script in two ways:

1. Without arguments:

   pdf-to-cbz-LX

2. With PDF files as arguments:

   pdf-to-cbz-LX file1.pdf file2.pdf

Graphical Interface
-------------------

The interface allows you to:
- Select PDF files
- Choose the output image format (jpeg, png, tiff)
- Set DPI resolution (default: 200)
- Choose the output folder
- Enable/disable deletion of original PDFs
- Follow conversion progress
- View a summary at the end
- Optionally save a log file

Performance
-----------

- The script uses only one CPU core
- Example: ~28 minutes for 50 pages in PNG (200 DPI)
- JPEG conversion is much faster
- TIFF not tested (very large format)

Why this project?
-----------------

I wanted a PDF → CBZ converter:
- without heavy dependencies
- without Python
- without complicated interfaces
- without shady online services

So I made my own, for fun.

This project follows the KISS idea: Keep It Simple, Stupid.

Contributing
------------

Contributions are welcome! Some ideas for improvement:

- Add multi-core/thread support
- Improve performance
- Support more image formats
- Clean and refactor the code
- Add translations (i18n)...

Pull requests are welcome!

License
-------

MIT License

This project is free. Use it, modify it, share it.

Thanks
------

- The Linux community
- Developers of YAD and Poppler
- ChatGPT for help writing the script
- Everyone who enjoys simple tools

Thank you!

Use it, share it, improve it — or just enjoy it.

Uninstall
---------

To remove the package without deleting config files:

   sudo dpkg -r pdf-to-cbz-LX

To remove the package and its config files:

   sudo dpkg --purge pdf-to-cbz-LX

