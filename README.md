- Auteur : Mona Senelier
- 📫 mona.senelier@gmail.com
- Contexte : Stage de deuxième année en école d'ingénieur à l'UVSQ

# Prédiction de l'issue de la grossesse par FIV

Ce document présente le fonctionnement de l'application web et les fichiers utiles pour parvenir au résultat présent à l'adresse : 
https://dashappfiv.herokuapp.com/


## Prérequis :
- instalation des dépendances : python, dash, plotly, git (+ les librairies présentes dans les fichiers python).
- Les 4 bases de données correspondant à chaque étapes chronologiques du protocole de FIV.



## Installation :

1) Le fichier "ga_XGBClassifier.py", contenant l'algorithme génétique permetant d'optimiser les paramètres de l'algorithme XG_Boost. 

2) Le fichier d'export des modèles et objets python ("export_models_rdv1_v4.py") :
- Pour chaque algorithmes est definie la base de données à utiliser. Puis les x,y afin d'entrainer le modèle grâce à la librairie XGBoost ainsi que l'optimisation de ses paramètres contenu dans le fichier "ga_XGBClassifier.py"
- A l'issue de l'entrainement de l'algorithme avec paramètres optimisés on obtient un modèle "m" et une matrice de confusion associée "cf_matrix".
- Ce sont donc "m" et "cf_matrix" que l'ont va exporter à l'aide de la librairie "pickle" et des lignes : 
	```
   pickle_out = open("m.pkl", "wb")
	pickle.dump(m, pickle_out) 
	pickle_out.close()
  
  ```
  
  
- Ce fichier d'export sert également à exporter directement les figures utiles pour l'exploitation graphique future.
De la même manière, en utilisant le jeu de donnée "Medifirst_original.xlsx" et la librairie matplotlil, il est possible de mettre en place les exploitations graphiques et de les exporter grâce à "pickle".  

3) Le fichier "fonctions_prediction_vf.py" n'est pas indispensable mais permet de limiter le code de "app_vf.py" car cela permet d'importer tous les objets et modèles avec "pickle" et des lignes :
 ```
pickle_in = open("m.pkl", 'rb') 
m = pickle.load(pickle_in)
```
  
  Puis de créer des fonctions retournant les objets utiles ainsi que les fonctions de prédictions prenant en paramètres kes varaibles utiles aux algorithmes et faisant appelles aux modèle ainsi qu'aux fonctions python "predict()" et "predict_proba()"


4) Le fichier app_vf.py décrivant l'application web, dans l'ordre d'écriture :
- Les librairies utiles
- Puis :
```
app = Dash(_name_)
```
- les outils nécessaires au front de l'application ainsi que les figures (ou autres objets python) ne nécessitant pas d'input
- Puis :
```
server = app.server
```
- le code du front "app.layout" de l'application :
	- le premier dcc.Tab correspond au premier onglet de 	l'app contenant les exploitations graphiques des 	données initiales
	- les trois autres dcc.Tab correspondent aux trois 	onglets suivant. Il contiennent les inputs nécessaire à 	la prédiction en fonction des algorithmes. Ils 	contiennent aussi un bouton "Calculer la prédiction". 	Ainsi que deux sorties textes "Prédiction de l'issue de 	la grossesse" et "Pourcentage de succès" étant chacune 	associée aux retours des algorithmes de prédictions, 	respectivement l'appartenance à la classe 	"succès"/"échec" et la probabilité de succès.
	Enfin, dans les trois dcc.Tab sont aussi présentes les 	trois matrices de confusion associées aux algorithmes.
- Les callback "@app.callback" de l'application : 
	- Il est nécessaire d'avoir un "@app.callback()" par 	fonction nécessitant des inputs.
	- Chaque fonction associée à un callback est définie 	de la même manière, c-a-d : le @app.callback contenant 	une sortie et les inputs associés ainsi qu'une fonction 	prenant en compte ces inputs.
	Attention : les noms de variables étant prises en 	paramètres par la fonction ne doivent pas être 	identiques à ceux définit dans les inputs du callbacks 	(ils seront quand même associés).

-  Enfin, les lignes :

```
if __name__ == '__main__':
app.run_server(debug=True)

```



## Déploiement :

Concernant le déploiement de l'applocation web j'ai suivis une méthode utilisant Github et Heroku dont l'explication est disponible ici :
https://dash.plotly.com/deployment

Pour son bon fonctionnement il faut :
- un compte github et un compte Heroku
- creer un dossier en local dont le nom n'est pas déjà utilisé par un autre déploiement Heroku
- un fichier app.py, c-a-d l'application web fonctionnelle en Dash en local.
- Un fichier "requirements.txt" comportant les librairies indispensables au projet
- un fichier "runtime.txt" (optionnel mais permet de forcer l'utilisation d'une version de python)
- un fichier "Procfile.txt" avec seulement écrit : 

```
web: gunicorn app:server 
```

- un fichier .gitignore
- l'ensemble des objets et modèles créés par "export_models_rdv1_v4.py" (voir remarque)

De plus, lors de la procédure de déploiement, un environnement virtuel (dossier "venv3") sera créé avec la commande :
```
virtualenv venv
```

Il est donc impératif de réinstaller les dépendances (dash et plotly), ainsi qu'une nouvelle dépendance "gunicorn" necéssaire au déploiement.
Il suffit ensuite de suivre les commandes indiqués dans le lien de méthode pour mettre en place le déploiement.

Remarque : Dans notre cas l'application présente des résultats issues d'algorithmes de prédiction, les fichiers initiaux sont donc trop volumineux pour être introduit tel quel. Pour pallier ce problème, il est possible, à l'aide de la librairie "pickle" d'exporter tous les objets pythons nécéssaires et ainsi de ne pas conserver les fichiers de calculs ni les bases de données. Ces exportations de modèles sont diponibles dans le fichiers python "export_models_rdv1_v4.py"









