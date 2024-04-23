# Projet Python : Simulateur d'un carnet d'ordres

## Introduction

Dans le cadre de notre formation en L3 Économie et Ingénierie Financière à l'Université Paris Dauphine-PSL, Nous, **Sana Harchaoui et Sihame Jdid**, avons décidé, pour notre projet d'Informatique appliquée à la finance, de travailler sur le sujet C : Création d'un Simulateur de Carnet d'Ordres. 

Ce choix reflète notre volonté de combiner nos connaissances économiques et financières acquises tout au long de notre formation à nos compétences techniques en programmation, en s'attaquant à l'un des aspects principaux des marchés financiers.

Le simulateur que nous avons développé est conçu pour gérer de manière dynamique les ordres d'achat et de vente dans un environnement qui simule la fluidité d'un marché électronique. Pour ce faire, nous avons créer une classe permettant l'ajout et l'annulation d'ordres par participant, respectant les principes d'un carnet d'ordres continu et fournir également un exemple d'utilisation de la classe.

Ce projet a été dirigé par Monsieur **Rémi Génet**, professeur d'Informatique appliquée à la finance.

## 1. Définition de la Classe 'Ordre'

La classe **Ordre** représente un ordre individuel dans le carnet d'ordres. Celle-ci est caractérisée par des attributs tels que le prix de l'actif financier, la quantité et le type d'ordre de ce dernier, mais également l'identifiant du participant qui a passé l'ordre.

```bash
class Ordre:
    """
    Représente un ordre dans le carnet d'ordres, avec prix, quantité, type,
    identifiant de l'ordre, et identifiant du participant.
    """
    def __init__(self, prix, quantite, type_ordre, id_ordre, id_participant):
        self.prix = prix
        self.quantite = quantite
        self.type_ordre = type_ordre
        self.id_ordre = id_ordre
        self.id_participant = id_participant
```

## 2. Création du Carnet d'Ordres

La classe **Carnet_Ordres** gère un ensemble d'ordres répartis en deux listes, une pour les achats et une pour les ventes. Les ordres d'achat sont triés par prix décroissant tandis que les ordres de vente sont triés par ordre croissant. 

```bash
class Carnet_Ordres:
    """
    Gère le carnet d'ordres, permettant l'ajout, l'affichage et l'annulation d'ordres.
    Les ordres sont triés par prix, et séparés en achat et vente.
    """
    def __init__(self):
        self.achat = []
        self.vente = []
        self.dernier_id = 0
```
## 3. Ajout et Annulation d'Ordres

La méthode **ajout_ordre** ajoute un ordre à la liste appropriée (achat ou vente) et le trie immédiatement. La méthode **annuler_ordre** cherche un ordre par son ID et le supprime s'il est trouvé.

```bash
    def ajout_ordre(self, ordre):
        """
        Ajoute un ordre au carnet et le trie. Les achats sont triés par prix décroissant,
        les ventes par prix croissant.
        """
        if ordre.type_ordre == 'achat':
            self.achat.append(ordre)
            self.achat.sort(key=lambda x: x.prix, reverse=True)
        elif ordre.type_ordre == 'vente':
            self.vente.append(ordre)
            self.vente.sort(key=lambda x: x.prix)

    def annuler_ordre(self, id_ordre):
        """
        Annule un ordre basé sur son identifiant.
        """
        for liste in (self.achat, self.vente):
            for i, ordre in enumerate(liste):
                if ordre.id_ordre == id_ordre:
                    del liste[i]
                    return True
        return False
```
## 4. Interaction avec l'Utilisateur

L'interface utilisateur offre un menu simple avec des options permettant à l'utilisateur d'ajouter, afficher ou supprimer des ordres, ainsi de quitter le programme. Chaque action est gérée via des entrées utilisateur simples.

```bash
# Exemple d'utilisation :
def interface_utilisateur():
    carnet_ordres = Carnet_Ordres()  # Initialisation de la classe Carnet_Ordres
    while True:
        print("\n1. Ajouter un ordre\n2. Afficher les ordres\n3. Supprimer un ordre\n4. Quitter")
        choix = input("Sélectionnez une option: ")
        if choix == '1':
            prix = float(input("Entrez le prix de l'ordre: "))
            quantite = int(input("Entrez la quantité de l'ordre: "))
            type_ordre = input("Type d'ordre (achat/vente): ").lower()
            id_participant = int(input("ID du participant: "))
            carnet_ordres.ajouter_ordre_utilisateur(prix, quantite, type_ordre, id_participant)
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

## 5. Testez le code !

À vous de tester le code en l'exécutant dans son ensemble :

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/sanasihame/Projet-Python-Simulateur-d-un-carnet-d-ordres/main?labpath=PROJETC.ipynb)


## Conclusion

Ce projet a été une expérience enrichissante à plusieurs niveaux. En effet, n'ayant plus fait de Python depuis notre première année de licence, il nous a permis de nous réapproprier ce langage, essentiel à notre vie professionnelle future.

De plus, en intégrant une dimension financière, ce projet a également renforcé notre compréhension des dynamiques des marchés financiers tout en renforcant nos compétences en programmation. 

Malgré des orientatitions futures différentes -- l'une d'entre nous (*Sihame Jdid*) a pour ambition de ne plus continuer dans la finance et de poursuivre dans le master **Quantitative Economics**, l'autre (*Sana Harchaoui*) continuant dans le master **EIF**, ce projet a été autant enrichissant l'une que pour l'autre.

Enfin, nous tenons à exprimer notre gratitude envers notre professeur, Monsieur **Rémi Genet**, pour ses enseignements enrichissants tout au long de ce semestre.
