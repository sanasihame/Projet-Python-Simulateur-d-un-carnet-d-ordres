# Projet Python : Simulateur d'un carnet d'ordres

## Introduction

Dans le cadre de notre formation en L3 Économie et Ingénierie Financière à l'Université Paris Dauphine-PSL, Nous, **Sana Harchaoui et Sihame Jdid**, avons décidé, pour notre projet d'Informatique appliquée à la finance, de travailler sur le sujet C : Création d'un Simulateur de Carnet d'Ordres. 

Ce choix reflète notre volonté de combiner nos connaissances économiques et financières acquises tout au long de notre formation à nos compétences techniques en programmation, en s'attaquant à l'un des aspects principaux des marchés financiers.

Le simulateur que nous avons développé est conçu pour gérer de manière dynamique les ordres d'achat et de vente dans un environnement qui simule la fluidité d'un marché électronique. Pour ce faire, nous avons créer une classe permettant l'ajout et l'annulation d'ordres par participant, respectant les principes d'un carnet d'ordres continu et fournir également un exemple d'utilisation de la classe.

Ce projet a été dirigé par Monsieur **Rémi Génet**, professeur d'Informatique appliquée à la finance.


## 1. Définition de la classe du Carnet d'Ordres

La classe **Carnet_Ordres** gère un ensemble d'ordres répartis en deux listes, une pour les achats et une pour les ventes. Les ordres d'achat sont triés par prix décroissant tandis que les ordres de vente sont triés par ordre croissant. Le choix de tick = 0.01 et lot = 100 est arbitraire et basé sur les standards ou conventions du marché financier que l'on essaye de simuler.

Le tick de 0.01 est courant pour de nombreux marchés d'actions, signifiant que le prix d'une action peut varier par incréments de un cent.
Un lot de 100 est standard pour de nombreuses bourses, où un ordre pour moins de 100 actions est considéré comme un ordre au détail.

Dans un scénario réel, le tick et le lot peuvent être déterminés en fonction des besoins de l'application et des contraintes de marché. Par exemple, si l'application gère des ordres de petites quantités d'actions, alors le tick et le lot peuvent être définis avec des valeurs plus petites pour permettre des opérations plus précises.

```bash
class Carnet_Ordres:
    """
    Gère le carnet d'ordres, permettant l'ajout, l'affichage et l'annulation d'ordres.
    Les ordres sont triés par prix, et séparés en achat et vente.
    """
    def __init__(self, tick=0.01, lot=100):
        self.tick = tick
        self.lot = lot
        self.achat = []
        self.vente = []
        self.dernier_id = 0
```
## 2. Ajout et Annulation d'Ordres

Nous utilisons tout d'abord une méthode privée pour arrondir le prix à l'unité de tick la plus proche.

```bash
    def __round_price(self, prix):
        """
        Arrondit le prix à l'unité de tick la plus proche.
        """
        return round(prix / self.tick) * self.tick
```

La méthode **__creer_ordre** créer l'ordre, en arrondissant le prix et en ajustant la quantité au multiple du lot.
La méthode **ajout_ordre** ajoute un ordre à la liste appropriée (achat ou vente) et le trie immédiatement. 
La méthode **annuler_ordre** cherche un ordre par son ID et le supprime s'il est trouvé.
Enfin, la méthode **afficher_carnet** affiche le contenu actuel du carnet d'ordres, en lisant séparément les achats et les ventes.

```bash
def __creer_ordre(self, prix, quantite, type_ordre, id_ordre):
        prix = self.__round_price(prix)
        quantite = int(quantite / self.lot) * self.lot
        return {
            'prix': prix,
            'quantite': quantite,
            'type_ordre': type_ordre,
            'id_ordre': id_ordre,
        }

    def ajout_ordre(self, prix, quantite, type_ordre):
        """
        Ajoute un ordre au carnet et le trie. Les achats sont triés par prix décroissant,
        les ventes par prix croissant. Refuse l'ordre si la quantité est inférieure au lot.
        """
        if quantite < self.lot:
            print(f"Quantité d'ordre invalide: elle doit être au moins égale au lot minimum de                      {self.lot}.")
            return
        if type_ordre not in ['achat', 'vente']: # Gestion des erreurs
            print("Type d'ordre invalide. Veuillez entrer 'achat' ou 'vente'.")
            return
        ordre = self.__creer_ordre(prix, quantite, type_ordre, self.dernier_id)
        if ordre['type_ordre'] == 'achat':
            self.achat.append(ordre)
            self.achat.sort(key=lambda x: x['prix'], reverse=True) # Tri par ordre décroissant
        elif ordre['type_ordre'] == 'vente':
            self.vente.append(ordre)
            self.vente.sort(key=lambda x: x['prix']) # Tri par ordre croissant
        self.dernier_id += 1
        print(f"Ordre ajouté. ID: {self.dernier_id - 1}, Type: {type_ordre}, Prix: {prix}, Quantité:            {quantite}")

    def annuler_ordre(self, id_ordre):
        """
        Annule un ordre basé sur son identifiant.
        """
        for liste in (self.achat, self.vente):
            for i, ordre in enumerate(liste):
                if ordre['id_ordre'] == id_ordre:
                    del liste[i]
                    return True
        return False

    def afficher_carnet(self):
        """
        Affiche le carnet d'ordres, séparant les achats des ventes.
        """
        print("Carnet d'Ordres:\nAchats:")
        for ordre in self.achat:
            print(f"ID: {ordre['id_ordre']}, Prix: {ordre['prix']}, Quantité: {ordre['quantite']}")
        print("Ventes:")
        for ordre in self.vente:
            print(f"ID: {ordre['id_ordre']}, Prix: {ordre['prix']}, Quantité: {ordre['quantite']}")

```
## 3. Interaction avec l'Utilisateur

L'interface utilisateur offre un menu simple avec des options permettant à l'utilisateur d'ajouter, afficher ou supprimer des ordres, ainsi de quitter le programme. Chaque action est gérée via des entrées utilisateur simples.

```bash
def interface_utilisateur():
    carnet_ordres = Carnet_Ordres()  # Initialisation de la classe Carnet_Ordres
    while True:
        print("\n1. Ajouter un ordre\n2. Afficher les ordres\n3. Supprimer un ordre\n4. Quitter")
        choix =input("Sélectionnez une option: ")
        if choix == '1':
            prix = float(input("Entrez le prix de l'ordre: "))
            quantite = int(input("Entrez la quantité de l'ordre: "))
            type_ordre = input("Type d'ordre (achat/vente): ").lower()
            carnet_ordres.ajout_ordre(prix, quantite, type_ordre)
        elif choix == '2':
            carnet_ordres.afficher_carnet()
        elif choix == '3':
            id_ordre = int(input("ID de l'ordre à supprimer: "))
            if carnet_ordres.annuler_ordre(id_ordre):  # Appel à la méthode d'annulation
                print("Ordre annulé.")  # Confirmation de l'annulation
            else:
                print("Ordre non trouvé.")  # Notification si l'ordre n'est pas trouvé
        elif choix == '4':
            print("Au revoir!")
            break
        else:
            print("Option invalide. Veuillez réessayer.")
```

Le bloc de code ci-dessus détermine si le script est exécuté directement ou importé dans un autre fichier. S'il est exécuté directement, la fonction interface_utilisateur() est appelée, lançant l'interface du carnet d'ordres.

```bash
if __name__ == "__main__":
    interface_utilisateur()
```

## 4. Exemple d'utilisation

Dans cette partie, nous allons créer un scénario permettant de fournir un exemple concret d'utilisation de notre code.
Pour ce faire, on se met dans la situation d'une journée basique de Trading.

En exécutant le code, voilà ce que le terminal nous affiche :

```plaintext
1. Ajouter un ordre
2. Afficher les ordres
3. Supprimer un ordre
4. Quitter
Sélectionnez une option: 
```

Premièrement, les Traders entrent leurs ordres dans le carnet à l'ouverture du marché.
- Un premier acheteur ajoute un ordre d'achat pour 30 unités à 190€
- Un deuxième acheteur ajoute un ordre d'achat pour 250 unités à 130€
- Un troisième vendeur ajoute un ordre de vente pour 100 unités à 50€
- Un quatrième vendeur ajoute un ordre de vente pour 150 unités à 98€

Dès lors, on sélectionne l'option 1
Après plusieurs manipulations. Nous avons entré les 3 derniers ordres de la manière suivante. En effet, le premier ordre ayant une quantité inférieure au lot de 100, il n'a pas été pris en compte :

```plaintext
Sélectionnez une option: 1
Entrez le prix de l'ordre: 190
Entrez la quantité de l'ordre: 30
Type d'ordre (achat/vente): achat
Quantité d'ordre invalide: elle doit être au moins égale au lot minimum de 100.

1. Ajouter un ordre
2. Afficher les ordres
3. Supprimer un ordre
4. Quitter
Sélectionnez une option: 1  
Entrez le prix de l'ordre: 130
Entrez la quantité de l'ordre: 250
Type d'ordre (achat/vente): achat
Ordre ajouté. ID: 0, Type: achat, Prix: 130.0, Quantité: 250

1. Ajouter un ordre
2. Afficher les ordres
3. Supprimer un ordre
4. Quitter
Sélectionnez une option: 1
Entrez le prix de l'ordre: 50
Entrez la quantité de l'ordre: 100
Type d'ordre (achat/vente): vente
Ordre ajouté. ID: 1, Type: vente, Prix: 50.0, Quantité: 100

1. Ajouter un ordre
2. Afficher les ordres
3. Supprimer un ordre
4. Quitter
Sélectionnez une option: 1
Entrez le prix de l'ordre: 98
Entrez la quantité de l'ordre: 150
Type d'ordre (achat/vente): vente
Ordre ajouté. ID: 2, Type: vente, Prix: 98.0, Quantité: 150
```

Ensuite, on fait afficher le carnet d'ordre.
Les ordres d'achats seront triés par ordre décroissant et les ordres de vente par ordre croissant.

```plaintext
1. Ajouter un ordre
2. Afficher les ordres
3. Supprimer un ordre
4. Quitter
Sélectionnez une option: 2
Carnet d'Ordres:
Achats:
ID: 0, Prix: 130.0, Quantité: 200
Ventes:
ID: 1, Prix: 50.0, Quantité: 100
ID: 2, Prix: 98.0, Quantité: 100
```

On ajoute finalement un nouvel ordre de vente pour 300 unités à 45€. Ce prix attractif deviendra le premier ordre de vente.

```plaintext
1. Ajouter un ordre
2. Afficher les ordres
3. Supprimer un ordre
4. Quitter
Sélectionnez une option: 1
Entrez le prix de l'ordre: 45
Entrez la quantité de l'ordre: 300
Type d'ordre (achat/vente): vente
Ordre ajouté. ID: 3, Type: vente, Prix: 45.0, Quantité: 300

1. Ajouter un ordre
2. Afficher les ordres
3. Supprimer un ordre
4. Quitter
Sélectionnez une option: 2
Carnet d'Ordres:
Achats:
ID: 0, Prix: 130.0, Quantité: 200
Ventes:
ID: 3, Prix: 45.0, Quantité: 300
ID: 1, Prix: 50.0, Quantité: 100
ID: 2, Prix: 98.0, Quantité: 100
```

Finalement, on annule l'ordre numéro 1.

```plaintext
1. Ajouter un ordre
2. Afficher les ordres
3. Supprimer un ordre
4. Quitter
Sélectionnez une option: 3
ID de l'ordre à supprimer: 1
Ordre annulé.

1. Ajouter un ordre
2. Afficher les ordres
3. Supprimer un ordre
4. Quitter
Sélectionnez une option: 2
Carnet d'Ordres:
Achats:
ID: 0, Prix: 130.0, Quantité: 200
Ventes:
ID: 3, Prix: 45.0, Quantité: 300
ID: 2, Prix: 98.0, Quantité: 100
```

On quitte par la suite le code :

```plaintext
1. Ajouter un ordre
2. Afficher les ordres
3. Supprimer un ordre
4. Quitter
Sélectionnez une option: 4
Au revoir!
```

## 5. Testez le code !

À vous de tester le code en l'exécutant dans son ensemble :

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/sanasihame/Projet-Python-Simulateur-d-un-carnet-d-ordres/HEAD?labpath=PYTHONC.ipynb)


## Conclusion

Ce projet a été une expérience enrichissante à plusieurs niveaux. En effet, n'ayant plus fait de Python depuis notre première année de licence, il nous a permis de nous réapproprier ce langage, essentiel à notre vie professionnelle future.

De plus, en intégrant une dimension financière, ce projet a également renforcé notre compréhension des dynamiques des marchés financiers tout en renforcant nos compétences en programmation. 

Malgré des orientatitions futures différentes -- l'une d'entre nous (*Sihame Jdid*) a pour ambition de ne plus continuer dans la finance et de poursuivre dans le master **Quantitative Economics**, l'autre (*Sana Harchaoui*) continuant dans le master **EIF**, ce projet a été autant enrichissant l'une que pour l'autre.

Enfin, nous tenons à exprimer notre gratitude envers notre professeur, Monsieur **Rémi Genet**, pour ses enseignements enrichissants tout au long de ce semestre.
