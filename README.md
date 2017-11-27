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

### Installing

A step by step series of examples that tell you have to get a development env running

Say what the step will be

```
Give the example
```

And repeat

```
until finished
```

End with an example of getting some data out of the system or using it for a little demo

## Running the tests

Explain how to run the automated tests for this system

### Break down into end to end tests

Explain what these tests test and why

```
Give an example
```

### And coding style tests

Explain what these tests test and why

```
Give an example
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
