---
public: true 
Category: tutorial
#Tags: vhost, hashes, lfi, password-spraying, potato
---
Il est parfois utile pour faire des tests sur des challenges nécessitant des compétences en développement .NET de disposer d'un environnement de développement .NET

Plusieurs solutions existent sous Windows et sous Linux.

Nous mettrons en place ici un environnement de développement sous Windows


La documentation présente [ici](https://code.visualstudio.com/docs/languages/dotnet) explique comment faire :

Après avoir installé le Net 7.0 SDK et l'extension Microsoft C# pour VSCode
Lancer dans le dossier où l'on veut créer une application la commande suivante :
```shell
dotnet new console --framework net7.0
```

Si un message d'erreur indique que le .Net framework n'est pas installé, vérifier s'il n'y a pas 2 versions d'installées (une dans program files et une dans program files(x86)) et, le cas échéant, modifier l'ordre de recherche du PATH

Relancer les applications / terminaux pour que la modification prenne effet.
