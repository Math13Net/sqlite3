# Utiliser une base de données SQL avec Python sqlite 3.  

Une base de données ou database est un outil qui permet de stocker des données numériques telles que les informations et mots de passes des utilisateurs d'un site, les messages et posts d'un forum ou d'un blog...

Les bases de données SQL sont stockées dans des fichiers text (.txt) que l'on ne modifie pas directement mais par l'intermédiaire d'un logiciel de gestion de bases de données. Le module sqlite 3 de Python permet de gérer des bases de données SQL: il est ainsi possible de créer des bases de données, de stocker des informations dans des tables, modifier, supprimer, ou extraire ces informations...

L'utilisation des bases de données SQL nécessite d'employer un langage particulier: le langage SQL.  

### Créer une base de données.  

On utilise pour cela la fonction connect(). Notre base de données s'appèlera *data*.  

    from sqlite3 import *
    
    conn = connect("data.txt")
  
Un fichier data.txt se crée alors automatiquement.
Il est désormais possible de se connecter à cette base de données en utilisant la même ligne de code.

Avant de réaliser toutes actions sur cette base de données il sera nécessaire de créer un "curseur" grâce à la fonction cursor().

    cur = conn.cursor()
  
### Fermer une base de données.  

    cur.close()
    conn.close()

### Créer une table.  

Une table est un moyen de ranger les informations dans votre base. La meilleure représentation que l'on puisse s'en faire est un tableau. Cependant si vous ouvrez directement votre fichier data.txt (à éviter et à ne surtout pas modifier directement) vous verrez que les informations sont rangées très différemment.

Notre table s'appellera "membres", elle contiendra trois colonnes: une colonne pour l’id qui acceptera des données de type integer , une colonne pour le pseudo qui acceptera des données de type text, et une colonne pour le mail qui acceptera aussi des donnees de type text
   
    cur.execute("CREATE TABLE membres (id INTEGER pseudo TEXT, mail TEXT)") 
   
Si la table existe déjà ca bug, pour éviter cela, on peut utiliser la requête "if not exists"

    cur.execute("CREATE TABLE IF NOT EXISTS membres (id INTEGER pseudo TEXT, mail TEXT)") 
   
### Supprimer une table.  

    cur.execute("DROP TABLE membres")
   
### Entrer des données dans une table.  

    cur.execute("INSERT INTO membres(id, pseudo, mail) VALUES(243, 'Robert ','robert@hotmail.fr')")
   
Il faut ensuite toujours penser à enregistrer les modifications de la table grâce à la commande suivante:

    conn.commit()
   
   
Une autre méthode consiste à rentrer au préalable les valeurs dans une tuple; cela permet de compléter en même temps plusieurs lignes de la table:  

    d =[(32, "Simon","simon@hotmail.fr"),(33, "Patrick","patrick@yahoo.com")]
		
	for i in d:
		cur.execute("INSERT INTO membres(id, pseudo,mail) VALUES(?,?,?)", i)
	conn.commit()

Cela permet aussi d'automatiser les entrées dans la table:

    ID = 67
    Pseudonyme = "Sylvain"
    Email = "syl@gmail.com"
    
    d =[(ID, Pseudonyme, Email)]
		
	for i in d:
		cur.execute("INSERT INTO membres(id, pseudo,mail) VALUES(?,?,?)", i)
	conn.commit()

### Extraire des données.  

`cur.fetchall()` renvoie une liste de tuple contenant l’intégralité de ce que la requête a retourné.

    >>> cur.execute("SELECT * FROM membres") 
	<sqlite3.Cursor object at 0x10acc36c0>
	>>> r = cur.fetchall()
	>>> r
	[(32, 'Simon', 'simon@hotmail.fr'), (33, 'Patrick', 'patrick@yahoo.com')]


`cur.fetchone()` renvoie seulement la première tuple. Utilisez le si vous ne voulez que la première tuple, ou plus généralement si vous vous attendez à ce que votre requête ne retourne qu’une seule tuple.

	>>> cur.execute("SELECT * FROM membres") 
	<sqlite3.Cursor object at 0x10acc36c0>
	>>> r = cur.fetchone()
	>>> r
	(32, 'Simon', 'simon@hotmail.fr')
  

Extraire seulement les données de la colonne pseudo:

    >>> cur.execute("SELECT pseudo FROM membres")
	<sqlite3.Cursor object at 0x10acc36c0>
	>>> r = cur.fetchall()
	>>> r
	[('Simon',), ('Patrick',)]

Extraire seulement les données des colonnes pseudo et mail :

    >>> cur.execute("SELECT pseudo,mail FROM membres")
	<sqlite3.Cursor object at 0x10964d810>
	>>> r = cur.fetchall()
	>>> r
	[('Simon', 'simon@hotmail.fr'), ('Patrick', 'patrick@yahoo.com')]
  
Extraire le mail du membre ayant exactement le pseudo "Patrick":

    >>> x = 'Patrick'
	>>> cur.execute("SELECT mail FROM membres WHERE pseudo = ?",(x,))
	<sqlite3.Cursor object at 0x10964d810>
	>>> r = cur.fetchone()
	>>> r
	('patrick@yahoo.com',)

Extraire tous les pseudo dont l'adresse mail contient le mot "yahoo" :

    >>> x = '%yahoo%'
	>>> cur.execute("SELECT pseudo FROM membres WHERE mail LIKE ?",(x,))
	<sqlite3.Cursor object at 0x1097a5810>
	>>> r = cur.fetchall()
	>>> r
	[('Patrick',)]

Les requêtes SELECT suivantes vous donneront un petit aperçu de ce qu'il est possible de faire. (Elles font reference à une table ayant pour nom: "table" et pour colonnes: "colA" , "colB" , "colC", "colD", colC et colD étant de type INTEGER)

    select * from table
    select * from table where colA = 'Robert'
    select * from table where colA = 'Robert' and colB = ‘Diatta’
    select colA, colB from table order by colB
    select colA, colB from table where colA='Darwin' or colA='Mendel' order by colB
    select sum(colC) from table
    select sum(colC) from oeuvres where colA='Robert' 
    select * from table where colC >35 order by colD desc 
    select * from table where colD <1800
    select * from table where colD <1800 limit 3


### Modifier des données.  

Pour remplacer des données:  

    cur.execute("UPDATE membres SET pseudo ='Patrick' WHERE pseudo ='Robert'")
   
Pour supprimer des données:

    cur.execute("DELETE FROM membres WHERE pseudo ='Robert'")
   
N'oubliez pas d'enregistrer vos modifications:

    conn.commit()
   
 
### Faire une jointure entre 2 tables.     

    SELECT * FROM table1, table2 WHERE table1.cola = table2.colb
   
   
### Ajouter une colonne à une table

    ALTER TABLE NomTable ADD COLUMN NomNouvelleColonne ColonneType
   
   
### Obtenir des informations sur une table ou une base
 
#### Obtenir une liste des colonnes d'une table

	cur.execute("SELECT * FROM table)
	r = list(cur.description) 
	col = [i[0] for i in r]

#### Obtenir une liste des tables d'une base

	cur.execute("SELECT name FROM sqlite_master WHERE type='table';")
	cel = list(cur)