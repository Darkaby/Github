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

### Bank

Cette classe contient notre fonction main. La séquence d'action est effectuée comme suit:
* Ouverture du programme et création d'un nouveau compte (nom, prénom, âge et solde);
* Affichage de la liste d'actions et choix d'action du client;
* Affichage de la liste des comptes et choix du compte du client;
* En fonction du choix d'action de l'utilisateur définit par la classe `ChoiceAccount`, on appelle les classes et méthodes correspondantes;
* Lorsque l'utilisateur quitte sa session, il a la possibilité d'en démarrer une nouvelle lui-même ou de laisser la place à un autre utilisateur, ou il peut quiter définitivement le programme.

```java
private static CurrentAccount ChooseAccount(){
        compositeLogger.info("OUTPUT","Choisir un compte: ");
        clientAccount.getListOfAccountToString();
        AtomicInteger choiceAccount = new AtomicInteger(scan.nextInt());
        compositeLogger.info("INPUT", "Choix de compte: "+ choiceAccount.get());
        return clientAccount.getAccount(choiceAccount.get());
    }
```
Cette méthode affiche la liste des comptes du client pour qu'il puisse faire un choix sur la suite des opérations.

Nous avons définit trois autres méthodes plus bas permettant de demander à l'utilisateur d'entrer une valeur, puis de la lire et de l'enregistrer. Elles ne diffèrent qu'en fonction de la nature de l'entrée, selon que ce soit une chaîne de caractères, un integer, ou un double.
```java
private static String printString(String ouput)
private static int printInt(String ouput)
private static double printDouble(String ouput)
```

Par exemple, pour un integer, on a:
```java
private static int printInt(String ouput){
        int Int;
        compositeLogger.info("OUTPUT", ouput);
        Int = scan.nextInt();
        compositeLogger.info("INPUT", ""+Int);
        return Int;
    }
```

### BankTest

Cette classe implémente des tests sur certaines des méthodes définies afin de vérifier que le programme fonctionne.

## Module Testframework

Il ne contient qu'une seule et unique classe: `RunTest`. Cette dernière contient une fonction main qui exécutera la classe `BankTest`. Elle gère les exceptions, détermine le nombre de tests réussis et de tests échoués, et calcule leur pourcentage selon le nombre de tests total.


## Fichiers .sh

Nous avons créer 3 fichiers `.sh` permettant de compiler et d'exécuter notre projet:

* `compile.sh`: compile tous les fichiers `.java` de notre projetet les stocke dans un dossier `bin` unique à chaque module;
```java
rm -rf logger/bin
mkdir logger/bin
javac -d logger/bin $(find logger/src -name *.java)

rm -rf banking/bin
mkdir banking/bin
javac -classpath logger/bin -d banking/bin $(find banking/src -name *.java)

rm -rf testframework/bin
mkdir testframework/bin
javac -classpath logger/bin:banking/bin -d testframework/bin $(find testframework/src -name *.java)
```

* `launch_banking.sh`: exécute la classe main du module `Banking`;
```java
java -classpath logger/bin:banking/bin banking.Bank
````

* `test.sh`: exécute la classe main du module `Testframework`.
```java
java -classpath logger/bin:banking/bin:testframework/bin test.RunTest banking.BankTest
````

## Difficultés rencontrées

Les problèmes essentiels se sont surtout présentés au niveau de l'élaboration du `Logger` et du `Testframework`. La mauvaise compréhension du sujet et les mises à jour presque régulières de l'enseignant ont ralentit l'avancée du projet. Cependant, nous avons atteint nos objectifs et suggérons, pour l'amélioration du programme, d'intégrer une base de données pour sauvegarder les opérations des clients, ainsi qu'une interface graphique.

## Acteurs du Projet

### Enseignant : Loïc LeDoyen
### Binôme
    * TCHUENBOU KOMGUEP Gabrielle-Renée
    * FOGNO'O BOPDA Ulrich Ivanick
