# ☮ PHP

***
### Forme d'un script
```php
	<?php 
	//script 
	?>
```

## Configuration
- On peut modifier la configuration de PHP en éditant `php.ini`
- Pour le localiser on peut executer la fonction `php.info`
```php
<?php phpinfo(); ?>
```
- Pour activer l'affichage d'erreur ont met `display_errors = on` et `error_reporting = E_ALL`  

## Code
⚠️Le nom des variables commence toujours par `$`
- Concatener une string avec une variable :
	- **Avec des guillemets doubles**
	```php
	<?php
	$fullname = "Mathieu Nebra";
	echo "Bonjour $fullname et bienvenue sur le site !";
	?>
	```
	- **Avec des guillemets simples**  
	`echo 'Bonjour $fullname et bienvenue sur le site !'` ne fonctionnera pas
	```php
	<?php
	$fullname = 'Mathieu Nebra';
	echo 'Bonjour ' . $fullname . ' et bienvenue sur le site !'; // OK
	?>
	```
	*Cette méthode est un peu plus rapide*
- Conditions :  
	- **if... else.. elif**
	`&& == ET`, `|| == OU`
	```php
	<?php
	$isAllowedToEnter = "Oui";
	// Si on a l'autorisation d'entrer
	if ($isAllowedToEnter == "Oui") {
	    // instructions à exécuter quand on est autorisé à entrer
	} // SINON SI on n'a pas l'autorisation d'entrer
	elseif ($isAllowedToEnter == "Non") {
	    // instructions à exécuter quand on n'est pas autorisé à entrer
	} // SINON (la variable ne contient ni Oui ni Non, on ne peut pas agir)
	else {
	    echo "Euh, je ne comprends pas ton choix, tu peux me le rappeler s'il te plaît ?";
	}
	?>
	```
	- **switch case**
	```php
	<?php
	$grade = 10;
	switch ($grade) // on indique sur quelle variable on travaille
	{ 
	    case 0: // dans le cas où $grade vaut 0
	        echo "Tu es vraiment un gros nul !!!";
	    break;
	    case 5: // dans le cas où $grade vaut 5
	        echo "Tu es très mauvais";
	    break;
	    case 7: // dans le cas où $grade vaut 7
	        echo "Tu es mauvais";
	    break;
	    default:
        echo "Désolé, je n'ai pas de message à afficher pour cette note";
	}
	```
	⚠️Le `switch` ira voir tous les cases écrits meme s'il entre dans un case favorable, on utilise le `break` pour le faire sortir dès qu'il entre dans le bon case

	- **Les terniaires**  
	C'est une condition condensée qui sert à tester une var et affecter une val à une var selon la condition (true ou false) **en une seule ligne**
	```php
	<?php
	$userAge = 24;
	if ($userAge >= 18) {
		$isAdult = true;
	}
	else {
		$isAdult = false;
	}
	?>
	```
	Devient :
	```php
	<?php
	$userAge = 24;

	$isAdult = ($userAge >= 18) ? true : false;

	// Ou mieux, dans ce cas précis
	$isAdult = ($userAge >= 18);
?>
	```
- Tableaux :
```php
<?php
$mickael = ['Mickaël Andrieu', 'mickael.andrieu@exemple.com', 'S3cr3t', 34];
$mathieu = ['Mathieu Nebra', 'mathieu.nebra@exemple.com', 'devine', 33];
$laurene = ['Laurène Castor', 'laurene.castor@exemple.com', 'P4ssw0rD', 28];
$users = [$mickael, $mathieu, $laurene];
echo $users[1][1]; // "mathieu.nebra@exemple.com"
```

- Boucles :
	- **while**
	```php
	<?php
	while ($isValid) {
    // instructions à exécuter dans la boucle
	}
	?>
	```
	- **for**
	```php
	<?php
	for ($lines = 0; $lines <= 2; $lines++)
	{
    	echo $users[$lines][0] . ' ' . $users[$lines][1] . '<br />';
	}
	?>
	```