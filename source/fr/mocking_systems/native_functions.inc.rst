
.. _mock-native-function:

Le bouchonnage (mock) des fonctions natives de PHP
**************************************************

atoum permet de très facilement simuler le comportement des fonctions natives de PHP.

.. code-block:: php

   <?php

   $this
      ->assert('the file exist')
         ->given($this->newTestedInstance())
         ->if($this->function->file_exists = true)
         ->then
         ->object($this->testedInstance->loadConfigFile())
            ->isTestedInstance()
            ->function('file_exists')->wasCalled()->once()

      ->assert('le fichier does not exist')
         ->given($this->newTestedInstance())
         ->if($this->function->file_exists = false )
         ->then
         ->exception(function() { $this->testedInstance->loadConfigFile(); })
   ;

L'expression ``$this->function->file_exists = …`` permet de définir avec une valeur, une expression ou même une fonction anonyme le comportement souhaité de la fonctionne pour le test.

Exemple avec la fonction native mail() de PHP :

.. code-block:: php

   <?php

   $that = $this;
   $this
       ->assert('mail')
       ->given($this->newTestedInstance())
	   ->if($this->function->mail = function (string $to, string $subject, string $message, $headers, string $params = '') use ($that) {
	       $that
		   ->string($to)
		   ->isNotEmpty()
		   ->boolean(filter_var($to, FILTER_VALIDATE_EMAIL) !== false)
		   ->isTrue()
		   ->string($subject)
		   ->isNotEmpty()
		   ->string($message)
		   ->isNotEmpty()
	       ;

   	       return true;
        })
	->then
	…

.. important::
	On ne peut pas mettre de \\ devant les fonctions à simuler, car atoum s’appuie sur le mécanisme de résolution des espaces de nom de PHP.

.. important::
	Pour la même raison, si une fonction native a déjà été appelée, son bouchonnage sera sans effet.

.. code-block:: php

   <?php

   $this
      ->given($this->newTestedInstance())
      ->exception(function() { $this->testedInstance->loadConfigFile(); }) //la fonction file_exists est appelée avant son bouchonnage

      ->if($this->function->file_exists = true ) // le bouchonnage ne pourra pas prendre la place de la fonction native file_exists
      ->object($this->testedInstance->loadConfigFile())
         ->isTestedInstance()
   ;

.. note::
	Plus d'information via :ref:`isTestedInstance()<object-is-tested-instance>`.
