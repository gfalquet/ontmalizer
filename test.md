% Guide
% G. Falquet
% mai 2020

# Micro introduction pratique à RDF, GraphDB, SPARQL

## Principe RDF

RDF est un cadre pour la description d'entités (appelées ressources) sous forme de triplets

    <sujet> <prédicat> <objet>

par exemple:

    <ferdinand> <est-né-à> <Genève>

Le sujet d'un triplet doit être une ressource identifiée par un identificateur global ou local. Dans ce dernier cas on parle de noeud blanc. Un identifiant global doit être un IRI (International Resource Identifier") tel qu'on les trouve sur le web. Pour être correct, l'exemple ci-dessus pourrait prendre la forme

   <https://exemple/ferdinand> <http://exemple/est-né-à> <http://exemple/Genève> .

On peut choisir n'importe quel IRI, pourvu qu'il soit syntaxiquement correct. 

Pour simplifer l'écriture des triplets on définit en général un ou des préfix qui servent d'abréviation pour la première partie d'un IRI. Notre exemple s'écrit alors

    @prefix ex:  <https://exemple/> .
    
    ex:ferdinand ex:est-né-à ex:Genève .

L'objet d'un triplet peut être un littéral. Les littéraux sont les valeurs simples qu'on trouve usuellement dans les bases de données: nombres entiers, nombres réels, chaines de caractères, dates. Complétons l'exemple :

    @prefix ex:  <https://exemple/> .
    
    ex:ferdinand ex:est-né-à ex:Genève .
    ex:ferdinand ex:date-naissance 1857 .
    ex:ferdinand ex:prénom "Ferdinand" .
    ex:ferdinand ex:nom "de Saussure" .

    ex:ferdinand ex:collègue ex:charles .

    ex:charles ex:prénom "Charles" .
    ex:charles ex:nom "Bally".

Par ces triplets nous avons représenté le fait que Ferdinand de Saussure a comme collègue Charles Bally.

On peut voir chaque triplet comme un arc d'un graphe. L'arc lie le noeud sujet au noeud objet et est étiqueté par le prédicat.


## Base de données RDF (triple store)

La manière la plus simple de conserver et transmettre un graphe RDF est de le placer dans un fichier texte.

Exercice: copiez l'exemple ci-dessus, ouvrez votre éditeur de texte préféré (NotePad (Windows), Textedit (Mac), ou mieux: VIsual Studio Code, __mais pas MS Word !__), collez et enregistrez sous le nom exemple.ttl (ttl pour Turtle)

Pour rendre un graphe RDF disponible localement ou sur le web et pour pouvoir lui appliquer des traitements sophistiqués (recherche, inférence logique, etc.) on doit le stocke dans un système de gestion de triplets (triple store).

Nous avons installé un triple store GraphDB sur un serveur du CUI. Pour y accéder via son interface web ouvrez l'URL http://ke.unige.ch:7200/ ensuite donnez le nom d'utilisateur kos et le mot de passe qu'on vous a communiqué.

Le triple store gère plusieurs entrepôts (repositories). Dans le menu en haut à droite choisissez l'entrepôt kos.

Chaque entrepôt contient un graphe (appelé graphe par défaut) et éventuellement d'autres graphes.

## Ajouter des triplets dans le triple store

Dans le menu de gauche choisissez Import, puis RDF, puis le rectangle Upload RDF files.

Sélectionnez ensuite votre fichier ttl

Il doit ensuite apparaitre en haut de la liste des importation.

Cliquez import à droite du nom du fichier pour ouvrir le dialogue d'importation

  - Base IRI: lassez vide
  - Target graph: choisissez Named graph et donnez comme nom de grape http://exemple.xxx où xxx est votre nom (vous pouvez donner n'importe quel nom pourvu qu'il respecte la syntaxe des IRI)

Cliquez Import

Si le fichier était syntaxiquement correct le texte "Imported successfully in less than a second. " doit apparaitre sou le nom de votre fichier. Sinon corrigez votre fichier, sauvegardez-le et recommencez la procédure d'importation.


## Voir le contenu de votre graphe dans le triple store

Dans le menu de gauche choisissez Explore, puis Graphs overview, et cliquez ensuite le nom de votre graphe.

Les triplets du graphe doivent apparaitre.

En cliquant les IRI des sujets prédicats et objets on peut naviguer dans le graphe

**Remarque.** À droite de chaque triple apparait le nom du graphe dans une colonne appelée "context". Ce terme est synonyme de "graph". 

## Supprimer un graphe

Dans le menu de gauche choisissez Explore, puis Graphs overview.

Cliquez l'icône de la poubelle qui se trouve à droite du nom du graphe à éliminer


## RDF (2)

En général un graphe représente des entités des différents types. Par exemple dans

    @prefix ex:  <https://exemple/> .
    
    ex:ferdinand ex:nom "de Saussure" .
    ex:ferdinand ex:étudie-à ex:uniGE .
    ex:ferdinand ex:étudie-à ex:uniLeipzig .
    ex:ferdinand ex:collègue ex:charles .

    ex:charles ex:nom "Bally" .
    ex:charles ex:enseigne-à ex:uniGE .

ex:ferdinand et ex:charles sont des personnes, ex:uniGE et ex:uniLeipzig sont des universités.

Pour représenter ces faits ont peut définir deux classes: ex:Personne et ex:Université et attribuer un type aux entités. Une classe est une ressource, elle possède donc un IRI. Pour dire que C est une classe on écrira un triple de la forme

    C rdf:type owl:Class .

rdf:type et owl:Class sont des IRI standards, définis par le W3C (WWW consortium) 

De même, pour dire qu'une entité E est de type C on écrira

    E rdf:type C .

On obtiendra le graphe

    @prefix owl: <http://www.w3.org/2002/07/owl#> .
    @prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
    @prefix ex:  <https://exemple/> .
 
    # Personne et Université sont des classes.

    ex:Personne rdf:type owl:Class .   
    ex:Université rdf:type owl:Class .

    ex:ferdinand rdf:type ex:Personne .  # ferdinand est une personne
    ex:ferdinand ex:nom "de Saussure" .
    ex:ferdinand ex:étudie-à ex:uniGE .
    ex:ferdinand ex:étudie-à ex:uniLeipzig .
    ex:ferdinand ex:collègue ex:charles .

    ex:charles rdf:type ex:Personne .    # charles est une personne
    ex:charles ex:nom "Bally" .
    ex:charles ex:enseigne-à ex:uniGE .

    ex:uniGE rdf:type ex:Université .       # uniGe est une université
    ex:uniLeipzig  rdf:type ex:Université . # uniLeipzig également

**Exercice** Enregistrez ce graphe dans un fichier texte et chargez-le dans graphDB, dans votre graphe http://exemple*votre nom*

En explorant le graphe vous constaterez que

1. les triplets qui étaient déjà dans votre graphe y sont restés (p.ex. ex:ferdinand ex:date-naissance 1857 .)
2. les triplets qui étaient déjà dans le graphe et qui ont été importés à nouveau n'apparaissent pas à double (p.ex. ex:ferdinand ex:collègue ex:charles .). 


## Un peu de SPARQL

Le langage SPARQL est l'équivalent de SQL pour les bases RDF. Une requête SPARQL prend la forme

    select <variables à afficher>
    where {<condition>}

La condition porte sur

 1. la manière dont les variables doivent être liées dans le graphe (patron de trplets (triple pattern))
 2. des conditions booléennes sur les valeurs (< > = && (et) || (ou) etc. )

 Exemple. On veut savoir quelles sont les personnes décrites dans le graphe, c-à-d quelles sont les resources de type ex:Personne

    PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
    PREFIX ex: <https://exemple/>

    select  ?p
    where { ?p rdf:type ex:Personne }

Les variables sont toujours préfixées par un ? (ou un $). 

Les patrons de triplet peuvent porter sur plusieurs. Par exemple, si on veut connaitre le nom des collègues de Ferdinand on écrira

    PREFIX rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#>
    PREFIX ex: <https://exemple/>

    select  ?n
    where { ex:ferdinand ex:collègue ?x. ?x ex:nom ?n}

On cherche un __x__ qui est collègues avec ferdinand et qui porte le nom __n__.

## RDF (3)

Les classes peuvent être organisées hiérarchiquement au moyen du prédicat rdfs:subClassOf. Pour indiquer que les personnes peuvent être des linguistes ou des physiciens on écrira :

    @prefix owl: <http://www.w3.org/2002/07/owl#> .
    @prefix rdf: <http://www.w3.org/1999/02/22-rdf-syntax-ns#> .
    @prefix rdfs: <http://www.w3.org/2000/01/rdf-schema#> .
    @prefix ex:  <https://exemple/> .
 

    ex:Personne rdf:type owl:Class .  
    ex:Linguiste  rdf:type owl:Class ; rdfs:subClassOf ex:Personne .
    ex:Physicien  rdf:type owl:Class ; rdfs:subClassOf ex:Personne .
    
    ex:ferdinand rdf:type ex:Linguiste .
    ex:marie rdf:type ex:Physicien .
    ex:charles rdf:type ex:Linguiste .
    ex:anna rdf:type ex:Personne .

Dans la syntaxe Turtle du RDF un énoncé de la forme

    ex:Linguiste  rdf:type owl:Class ; rdfs:subClassOf ex:Personne .

est une abréviation pour 

    ex:Linguiste  rdf:type owl:Class .
    ex:Linguiste  rdfs:subClassOf ex:Personne .

