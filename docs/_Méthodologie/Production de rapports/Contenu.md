---
public: true # set to true to make the article publishable
---

Un rapport est l'élément le plus important d'une prestation (pentest, évaluation de la sécurité ou autre).

Il doit être interprettable à la fois par des experts techniques mais également comportant les informations qui intéresseront les décideurs.

C'est grace aux éléments qu'il contiendra qu'un décideur de la direction (exécutif) pourra prendre conscience de la nécessité  de mettre en place les solutions préconisées malgré les dépenses que cela représentera.

## Eléments devant être présents

- Résumé à destination des exécutifs (Executive Summary)
- Vue d'ensemble de l'évaluation (Overview of Assessment)
- Périmètre (Scope)
- Vulnérabilités et Recommendations (Vulnerabilities and Recommendations)

### Executive Summary

Il contiendra des éléments synthétiques tel qu'un graphique qui pourra être de cette forme :

![[Pasted image 20230504215529.png]]

### Overview of Assessment

Inclu les méthodes utilisées durant le processus d'évaluation.

Il détaillera l'execution de l'évaluation durant la période de test.

Il présentera le processus, les outils utilisés, etc.

### Scope

Présentera le périmètre défini en collaboration avec le client :

- la liste des actifs autorisées à être évalués
- la période durant laquelle ces tests ont été autorisés

### Vulnerabilities and Recommentations

Détaillera tout ce qui a été découvert / identifié durant l'évaluation de vulnérabilités (après avoir élémininé les faux positifs éventuels).

Il est préférable de regrouper ces éléments par gravité et/ou par le lien qu'ils peuvent avoir.

Chaque vulnérabilité être détaillée avec les caractéristiques suivantes :

- Nom de la vulnérabilité
- CVE
- CVSS
- Description du problème
- Références
- Actions de remédiation
- Preuve de la présence de la vulnérabilité (méthode de détection)
- Systèmes affectés
