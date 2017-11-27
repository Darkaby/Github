# Projet Java

Programme de Gestion d'une Banque

## Préréquis

Le code de gestion est séparer en trois modules distincts:
* Le `logger` qui gère les messages à afficher en fonction de leur catégorie et historise les évènements;
* Le `testframework` pour tester le code;
* Le `banking` qui contient toues les méthodes de gestion de la banque;

## Module Logger

### Interface Logger

Elle contient les méthodes de base ci-dessous. Ces dernières nous seront utiles plus tard pour
définir la manière dont les messages seront affichés.

```java
void log (String message);
void debug (String category, String message);
void info (String category, String message);
void error (String category, String message);
```
* `debug` désigne un message adressé uniquement au créateur du programme;
* `error` signale une erreur dans le programme;
* `info` signale une information à l'attention de l'utilisateur ou du client.
  Le mot category est defini pour trois valeurs bien précises:
    * "OUTPUT": message affiché sur la console;
    * "INPUT": entrée de l'utilisateur;
    * "PROG": résultat du programme.
    
### ContextualLogger

C'est une implémentationde l'interface `logger` qui permet d'imprimer le message avec un format composé
de l'annéee et de l'heure correspondant à l'appel d'une méthode, du nom de la classe et enfin du message.

### FileLogger

Cette classe permet de créér un fichier `FileLogger.txt` dans lequel sera historiser tous les évènemeents du programme.

### CompositeLogger

Ici, on détermine le format à adopter dans la console et dans le fichier FileLogger. Ainsi, toutes les sorties `OUPUT`
qui permettent à l'utilisateur d'avancer dans le programme seront affichés dans la console sans mise en forme, et dans le fichier avec la mise en forme.
Les catégories `INPUT` et `PROG` ne seront affichés que dans le fichier texte.

### LoggerFactory

C'est une autre instance de l'interface `logger` permettant de déclarer une nouvelle instance de la classe `CompositeLogger`.
```java
public static Logger getLogger(FileLogger fileLogger, ContextualLogger contextualLogger){
        return new CompositeLogger(fileLogger, contextualLogger);
    }
```

## Module Banking

Dans chaque classe du module `Banking`, on déclarera un `Logger` et un `CompositeLogger` qui nous permettra de préciser dans le fichier logger, de quelle classe provient le message.


### Interface Account

```java
void transfert(CurrentAccount debitAccount, CurrentAccount creditAccount, double amount);
void getListOfAccountToString();
void setListOfAccount(CurrentAccount account);
void deposit(double amount);
void withdrawal (double amount);
void setListOfLoaning(Loaning loaning);
void getListOfLoaningToString();
void getListOfOperation();
void setListOfOperation(Operation operation);
```
Grâce à cette interface, nous allons implémenter deux classes:
* `ClientAccount` implémentera les 3 premières méthodes;
* `CurrentAccount` implémentera les autres.

### ClientAccount

Cette classe nous permet de gérer le compte en banque d'un client. On pourra, pour un même client (nom, prénom et âge définis), créer plusieurs compte avec des soldes différents.

```java
public void transfert(CurrentAccount debitAccount, CurrentAccount creditAccount, double amount) {
```
La somme `amount` est transférée du compte `debitAccount` vers le compte `creditAccount`, appartenant tous les deux au même client.

```java
public void setListOfAccount(CurrentAccount account)
```
On construit une liste de comptes dont la taille s'agrandira au fur et à mesure, à chaque fois que le client créera un compte `account`. 

```java
public void getListOfAccountToString()
```
Afin que lors de ses opérations, l'utilisateur ait la possibilté de choisir sur le compte qu'il souhaite modifier, on affichera la liste des comptes disponibles.

```java
public CurrentAccount getAccount(int index)
```
Cette méthode permet de sélectionner le compte situer dans la liste decompte au numéro d'emplacement `index`.


### Loaning

La classe `Loaning` permet de définir les paramètres d'un prêt, de le calculer, et d'en afficher les caractéristiques:
* Paramètres
  * Taux annuel: devra être supérieur à 1;
  * Durée du prêt: Dans la classe `CurrentAccount`, on la définit telle que n'amenant pas le demandeur du prêt à un âge supérieur ou égal à 70 ans;
  * Montant à emprunter: Ne peut être ni négatif, ni nul.
  
* Caractéristiques
  * Montant à rembourser par mois
  

### Operation

Une opération ici est definit comme une action sur un compte pouvant être un retrait ou un dépôt. Elle a 3 paramètres:

* `Origins`: Origine de l'opération: si le client le fait lui-même, le type est `Inconnu` et dans le cas d'un transfert, précise vers quel compte est déposé ou de quel compte provient l'argent;
* `NameOperation`: Retrait ou Dépôt;
* `Amount`: Montant déposer ou retirer.

### CurrentAccount

A chaque compte courant, est associé un solde, un client, une liste d'opérations et une liste de crédits.
```java
private double balance;
private double firstbalance;
private ClientAccount clientAccount;
private ArrayList<Loaning> listOfLoaning = new ArrayList<>();
private ArrayList<Operation> listOfOperation = new ArrayList<>();
```

Lors de l'affichage de la liste des opérations, la variable `firstbalance` correspond au premier solde du compte client, soit à sa création. On l'affiche au début de la liste, et le montant actuel à la fin. Cela permet au client de pouvoir juger plus facilement les opérations qu'il a effectué sur son compte en banque.

Nous avons implémenté les méthodes suivantes:
* Dépôt: ilest défendu de déposer un montant négatif
```java
public void deposit(double amount)
```

* Retrait: on ne peut retirer un montant négatif, ni un montant supérieur à celui du solde du compte;
```java
public void withdrawal(double amount)
```

* Création de la liste de crédits: on ajoute `loaning` à la liste seulement si sa taille est inférieure à 2, et bien sûr en considérant la restriction sur l'âge dont nous avons parlé tantôt;
```java
public void setListOfLoaning(Loaning loaning)
```

* Affichage de la liste de prêts
```java
public void getListOfLoaningToString()
```

* Création et Affichage de la liste d'opérations
```java
public void setListOfOperation(Operation operation)
public void getListOfOperation()
```
### ChoiceAccount

Afin de ne pas trop encombrer notre fonction main, on a créé cette classe qui affiche la liste d'actions que le client peut effectuer. Elle réapparaîtra à chaque fois que l'utilisateur aura terminé une action.
```java
public void setListOfOperation(Operation operation)
public void getListOfOperation()
```


### And coding style tests

Explain what these tests test and why

```java
public class ChoiceAction {

    private Logger logger;
    private CompositeLogger compositeLogger = (CompositeLogger) LoggerFactory.getLogger(new FileLogger("D:/Documents/ESIEA/ProjetJava/Bank/FileLogger.txt"),
            new ContextualLogger(logger, "ChoiceAction"));

    @Override
    public String toString() {
        return "Liste des actions:\n" +
                "1 : Faire un dépôt; \n" +
                "2 : Faire un retrait; \n" +
                "3 : Demander un crédit; \n" +
                "4 : Consulter le solde; \n" +
                "5 : Voir la liste des opérations et consulter le solde; \n" +
                "6 : Voir la liste des crédits; \n" +
                "7 : Ouvrir un autre compte; \n" +
                "8 : Faire un transfert; \n" +
                "9 : Quitter la session.\n";
    }
}
```

## Deployment

Add additional notes about how to deploy this on a live system

## Built With

* [Dropwizard](http://www.dropwizard.io/1.0.2/docs/) - The web framework used
* [Maven](https://maven.apache.org/) - Dependency Management
* [ROME](https://rometools.github.io/rome/) - Used to generate RSS Feeds

## Contributing

Please read [CONTRIBUTING.md](https://gist.github.com/PurpleBooth/b24679402957c63ec426) for details on our code of conduct, and the process for submitting pull requests to us.

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/your/project/tags). 

## Authors

* **Billie Thompson** - *Initial work* - [PurpleBooth](https://github.com/PurpleBooth)

See also the list of [contributors](https://github.com/your/project/contributors) who participated in this project.

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments

* Hat tip to anyone who's code was used
* Inspiration
* etc
