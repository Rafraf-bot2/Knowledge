# 🌱 Git 

Resumé de mes connaissances sur Git

***
### 🗒 Explanations
**Commit** => Snapshot du repertoire de travail, à chaque commit git n'enregistre que les changements (deltas) survenu apres le dernier commit.  
**Branche** => Pointeur vers un commit.  
**Merge** => Fusion de deux branche.

### 👨‍💻 CMD
- **git commit -m *messageCommit***  
Commit les changement survenu apres le dernier commit.
	- *messageCommit* : Message explicatif du commit.

- **git branch *nomBranche***  
Créé une branche *nomBranche*.

- **git checkout *nomBranche***    
Positionne sur *nomBranche*.

- **git switch *nomBranche***  
Positionne sur *nomBranche* (askip nouvelle version).

- **git checkout -b [yourbranchname]**  
Créé la branche *nomBranche* et nous positionne dessus.

- **git merge *nomBranche***  
Fusion *nomBranche* avec *main*.
	</p>
		<p align="center">
		<img src="img/merge1.png" width="400" height ="350">
		<br>
		Arbo <strong>avant</strong> merge
	</p>
		</p>
		<p align="center">
		<img src="img/merge2.png" width="400" height ="350">
		<br>
		Arbo <strong>après</strong> merge
	</p>

- **git rebase *nomBranche***  
Fusionne aussi les branches mais cette fois en gardant une ramification.
	</p>
		<p align="center">
		<img src="img/rebase1.png" width="400" height ="350">
		<br>
		Arbo <strong>avant</strong> rebase
	</p>
		</p>
		<p align="center">
		<img src="img/rebase2.png" width="400" height ="350">
		<br>
		Arbo <strong>après</strong> rebase
	</p>


### 🖊 Annexes
- <a href="https://learngitbranching.js.org/" target="_blank">Lab tiptop</a>   