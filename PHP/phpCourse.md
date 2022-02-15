# 🐘PHP

***
### 🖊Forme d'un script
```php
	<?php 
	//script 
	?>
```

## ⚙Configuration
- On peut modifier la configuration de PHP en éditant `php.ini`
- Pour le localiser on peut executer la fonction `php.info`
```php
<?php phpinfo(); ?>
```
- Pour activer l'affichage d'erreur ont met `display_errors = on` et `error_reporting = E_ALL`  

## 👨‍💻Code
⚠️Le nom des variables commence toujours par `$`
- Concatener une string avec une variable :
	- **Avec des guillemets doubles**
	```php
	<?php
	$fullname = "Mathieu Nebra";
	echo "Bonjour $fullname et bienvenue sur le site !";
	?>
	```
	- **Avec des guillemets simples** *(Cette méthode est un peu plus rapide)*  
	`echo 'Bonjour $fullname et bienvenue sur le site !'` ne fonctionnera pas
	```php
	<?php
	$fullname = 'Mathieu Nebra';
	echo 'Bonjour ' . $fullname . ' et bienvenue sur le site !'; // OK
	?>
	```  
	
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

	//On peut aussi crée le tableau en utilisant (array)
	$mickael = array('Mickaël Andrieu', 'mickael.andrieu@exemple.com', 'S3cr3t', 34);

	$mathieu = ['Mathieu Nebra', 'mathieu.nebra@exemple.com', 'devine', 33];
	$laurene = ['Laurène Castor', 'laurene.castor@exemple.com', 'P4ssw0rD', 28];

	$users = [$mickael, $mathieu, $laurene];
	echo $users[1][1]; // "mathieu.nebra@exemple.com"
	?>
	```  
	- **Tableau associatif**  
Tableau qui sert a *étiqueter* les cases au lieu de les *numéroter*  
	```php
		<?php
		$recipe = [
	    	'title' => 'Cassoulet',
	    	'recipe' => 'Etape 1 : des flageolets, Etape 2 : ...',
	    	'author' => 'john.doe@exemple.com',
	    	'enabled' => true,
		];

		echo $recipe['title']; //Cassoulet
		?>
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
	- **foreach**
	```php
	<?php
	//On utilise le tableau $recipe défini dans l'exemple de tableau associatif
	foreach ($recipe as $value) {
    echo $value;
	}
	?>
	```
	On peut aussi recuperer la clé de l'element du tableau 
	```php
	<?php
	$recipe = [
    	'title' => 'Salade Romaine',
    	'recipe' => 'Etape 1 : Lavez la salade ; Etape 2 : euh ...',
    	'author' => 'laurene.castor@exemple.com',
	];

	foreach($recipe as $property => $propertyValue)
	{
    	echo '[' . $property . '] vaut ' . $propertyValue . PHP_EOL; 
    	//On obtient pour la premiere case : [title] vaut Salade Romaine
	}
	?>
	```

- Fonctions :
	```php
	function fonctionSample(typeparam param) : typereturn
	{
		//corps de la fonction
		return variable
	}
	```

- Fonctions prédefinies :
	- **Manipulation de String**  
	```php
	<?php
	$recipe = 'Etape 1 : des flageolets ! Etape 2 : de la saucisse toulousaine';
	//strlen retourne la longueur d'un String
	$length = strlen($recipe);
	
	//str_replace remplace un String par un autre 
	echo str_replace('c', 'C', 'le cassoulet, c\'est très bon');
	//on obtiendra ici : le Cassoulet, C'est très bon

	//sprintf permet de formater un String
	$recipe = [
	    'title' => 'Salade Romaine',
	    'recipe' => 'Etape 1 : Lavez la salade ; Etape 2 : euh ...',
	    'author' => 'laurene.castor@exemple.com',
	];

	echo sprintf(
	    '%s par "%s" : %s',
	    $recipe['title'],
	    $recipe['author'],
	    $recipe['recipe']
	);
	?>	
	``` 
	- **Manipuler les dates**
	```php
	<?php

	$day = date('d');
	$month = date('m');
	$year = date('Y');

	$hour = date('H');
	$minut = date('i');

	echo 'Bonjour ! Nous sommes le ' . $day . '/' . $month . '/' . $year . 'et il est ' . $hour. ' h ' . $minut;
	?>
	```
	- **Test de l'existence d'une var**
	```php
	<?php

	//isset() teste si une variable existe
	if (!isset($_GET['email']) || !isset($_GET['message']))
	{
		echo('Il faut un email et un message pour soumettre le formulaire.');
		
		// Arrête l'exécution de PHP
	    return;
	}
	?>
	```
	- **print_r**  
	Permet d'afficher entièrement un tableau (une sorte d'echo spécialisé pour les tableaux)  
	Utile pour le déboggage
	```php
	<?php
	$recipes = [
    	[
        	'title' => 'Cassoulet',
        	'recipe' => '',
        	'author' => 'mickael.andrieu@exemple.com',
        	'is_enabled' => true,
    	],
    	[
        	'title' => 'Couscous',
        	'recipe' => '',
        	'author' => 'mickael.andrieu@exemple.com',
        	'is_enabled' => false,
    	],
	];

	echo '<pre>'; //permet d'avoir un affichage plus lisible
	print_r($recipes);
	echo '</pre>';
	?>
	```
	- **array_key_exists**, **in_array** et **array_search**
	```php
	<?php
	$recipe = [
	    'title' => 'Salade Romaine',
	    'recipe' => 'Etape 1 : Lavez la salade ; Etape 2 : euh ...',
	    'author' => 'laurene.castor@exemple.com',
	];

	//renvoie true si la clé est dans le tableau, false sinon
	if (array_key_exists('title', $recipe))
	{
    	echo 'La clé "title" se trouve dans la recette !';
	}

	//renvoie true si la val se trouve dans le tableau, false sinon
	if (in_array('Salade Romaine', $recipe))
	{
	    echo 'carré';
	}

	//renvoie la clé correspondante (num ou nom ça dépend du tableau) si elle trouve la val, faux sinon
	$cleSalade = array_search('Salade Romaine', $recipe)
	echo '"Salade Romaine" se trouve dans' . $cleSalade . PHP_EOL;
	?>
	```
	- **Controle**
	```php
	<?php

	//filter_var(var, filtre) verifie si var passe le filtre
	//empty(var) vérifie si var est vide
	if (
	    (!isset($_GET['email']) || !filter_var($_GET['email'], FILTER_VALIDATE_EMAIL))
	    || (!isset($_GET['message']) || empty($_GET['message']))
	    )
	{
		echo('Il faut un email et un message valides pour soumettre le formulaire.');
	    return;
	}
	?>
	```

- Formulaires :  
Forme d'un formulaire :
	```php
	<!-- index.php -->
	<form method="post" action="submit_form.php">
	 
	<p>
	    On insèrera ici les éléments de notre formulaire.
	</p>
	 
	</form>
	```  
- `action` = Page cible appelée par le formulaire. Elle recevra les données du formulaire et les traitera.
- `method` = Méthode d'envoie utilisée  
On peut en employer deux :
	- `get` : Les données transitent par l'url (données limitées car URL <= 256 de préference). On les recupère avec le tableau `$_GET`.
	- `post` : Les données ne transitent pas par l'url (donc données illimités). On les recupère avec le tableau `$_POST`
	⚠️On vérifie quand meme les parametres.  
⚠️On privilégie `post`   
<br>Exemple avec `get` :
	```php
	<form action="submit_contact.php" method="GET">
	    <div>
	        <label for="email">Email</label>
	        <input type="email" name="email">
	    </div>
	    <div>
	        <label for="message">Votre message</label>
	        <textarea placeholder="Exprimez vous" name="message"></textarea>
	    </div>
	    <button type="submit">Envoyer</button>
	</form>
	```
	<p align="center"><strong>index.php</strong></p>  
	
		- L'utilisateur a entré dans le formulaire `utilisateur@example.com` et `Bonjour`  
		- Le formulaire va etre converti en lien vers : `submit_contact.php?email=utilisateur%40example.com&message=Bonjour`  
		- Ces informations pourront etre recuperés dans `submit_contact.php`
		- Une variable <a href="https://www.php.net/manual/fr/language.variables.superglobals.php" target="_blank">superglobale</a> `$_GET` 
va contenir les données envoyées  
| Nom             	| Valeur          	      |
|:-----------------:|:-----------------------:|
| $\_GET['email'] 	| utilisateur@example.com |
| $\_GET['message'] | Bonjour				  |
  
	```php
	<h1>Message bien reçu !</h1>
        
	<div class="card">
	    
	    <div class="card-body">
	        <h5 class="card-title">Rappel de vos informations</h5>
	        <p class="card-text"><b>Email</b> : <?php echo $_GET['email']; ?></p>
	        <p class="card-text"><b>Message</b> : <?php echo $_GET['message']; ?></p>
	    </div>
	</div>
	```  
	<p align="center"><strong>submit_contact.php</strong></p>  
⚠️Ne jamais faire confiance qui transitent de page en page 
