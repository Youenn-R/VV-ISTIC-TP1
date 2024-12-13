# Practical Session #1: Introduction

1. Find in news sources a general public article reporting the discovery of a software bug. Describe the bug. If possible, say whether the bug is local or global and describe the failure that manifested its presence. Explain the repercussions of the bug for clients/consumers and the company or entity behind the faulty program. Speculate whether, in your opinion, testing the right scenario would have helped to discover the fault.

2. Apache Commons projects are known for the quality of their code and development practices. They use dedicated issue tracking systems to discuss and follow the evolution of bugs and new features. The following link https://issues.apache.org/jira/projects/COLLECTIONS/issues/COLLECTIONS-794?filter=doneissues points to the issues considered as solved for the Apache Commons Collections project. Among those issues find one that corresponds to a bug that has been solved. Classify the bug as local or global. Explain the bug and the solution. Did the contributors of the project add new tests to ensure that the bug is detected if it reappears in the future?

3. Netflix is famous, among other things we love, for the popularization of *Chaos Engineering*, a fault-tolerance verification technique. The company has implemented protocols to test their entire system in production by simulating faults such as a server shutdown. During these experiments they evaluate the system's capabilities of delivering content under different conditions. The technique was described in [a paper](https://arxiv.org/ftp/arxiv/papers/1702/1702.05843.pdf) published in 2016. Read the paper and briefly explain what are the concrete experiments they perform, what are the requirements for these experiments, what are the variables they observe and what are the main results they obtained. Is Netflix the only company performing these experiments? Speculate how these experiments could be carried in other organizations in terms of the kind of experiment that could be performed and the system variables to observe during the experiments.

4. [WebAssembly](https://webassembly.org/) has become the fourth official language supported by web browsers. The language was born from a joint effort of the major players in the Web. Its creators presented their design decisions and the formal specification in [a scientific paper](https://people.mpi-sws.org/~rossberg/papers/Haas,%20Rossberg,%20Schuff,%20Titzer,%20Gohman,%20Wagner,%20Zakai,%20Bastien,%20Holman%20-%20Bringing%20the%20Web%20up%20to%20Speed%20with%20WebAssembly.pdf) published in 2018. The goal of the language is to be a low level, safe and portable compilation target for the Web and other embedding environments. The authors say that it is the first industrial strength language designed with formal semantics from the start. This evidences the feasibility of constructive approaches in this area. Read the paper and explain what are the main advantages of having a formal specification for WebAssembly. In your opinion, does this mean that WebAssembly implementations should not be tested? 

5.  Shortly after the appearance of WebAssembly another paper proposed a mechanized specification of the language using Isabelle. The paper can be consulted here: https://www.cl.cam.ac.uk/~caw77/papers/mechanising-and-verifying-the-webassembly-specification.pdf. This mechanized specification complements the first formalization attempt from the paper. According to the author of this second paper, what are the main advantages of the mechanized specification? Did it help improving the original formal specification of the language? What other artifacts were derived from this mechanized specification? How did the author verify the specification? Does this new specification removes the need for testing?

## Answers

1.
Description
Un fichier de driver mal formaté a été inclus dans une mise à jour de sécurité pour CrowdStrike Falcon Sensor sur les services Azure. Le fichier contenait 21 entrées, alors que l’agent attendait seulement 20 entrées. Cela a provoqué une lecture hors limite, entraînant un "écran bleu de la mort" (BSOD).Le bug était global.

Impact sur les clients :
Le bug a affecter de nombreuses entreprises dépendantes des services Azure. Les systèmes des clients ont été arrêtés, obligeant les équipes à redémarrer les machines manuellement. Cela a causé des interruptions importantes des activités, entraînant des pertes.

Conséquences pour CrowdStrike et Azure :
Pour CrowdStrike et Microsoft, ce bug a probablement entraîné une perte de confiance chez les clients, des coûts de support technique pour résoudre le problème, et des critiques sur la qualité des tests réalisés avant la publication de la mise à jour.

Prévention possible :
Étant donné que le problème provenait d’un dépassement de limite causé par une modification dans les données fournies, des tests de dépassement de limites ou de compatibilité sur les mises à jour des drivers auraient probablement permis de détecter l’anomalie avant son déploiement.

2.
Description :
Le bug choisi est le ticket [COLLECTIONS-802](https://issues.apache.org/jira/browse/COLLECTIONS-802). Nous trouvons plus de détails de sa résolution sur la [pull request github](https://github.com/apache/commons-collections/pull/300).
Il s'agit d'un bug local. Il est reporté un mauvais comportement
de la fonction hasNext, qui, lorsqu'elle retourne false, elle met à jour la valeur currentValue qui devrait pourtant rester à la valeur précédente (puisqu'il s'agit de la fonction hasNext et non next.) Nous nous retrouvons donc avec une valeur currentValue à null. 

Solution adoptée : 
Pour corriger le bug, les lignes donnant la valeur null aux valeurs courantes ont été retirés. De plus, ils ont aussi corrigés leurs dépendances du pom. Cependant, ces modifications ont fait apparaître des échecs dans les tests unitaires que les contributeurs sont venus corriger. 
Il n'y a pas eu de nouveaux tests à ajouter, les simples tests de non régression déjà existants et corrigés suffisent à vérifier si ce problème viendrait à réapparaître.

3.
Chaos Monkey : Termine aléatoirement des instances de machines virtuelles hébergeant des services Netflix pour tester si l'arrêt d'une instance unique ne compromet pas le service. Ce test fonctionne de manière continue durant les jours de la semaine.
Chaos Kong : Simule la défaillance d'une région Amazon EC2 entière. Ce test est effectué une fois par mois.
Failure Injection Testing (FIT) : Des exercices où les requêtes entre les services de Netflix échouent volontairement afin de vérifier que, malgré ces pannes, le système se dégrade de manière maîtrisée (graceful degradation).
Pour rendre ces expériences pertinentes, il est important d'utiliser des données de test réalistes, voire de vraies données.
Netflix se base principalement sur la métrique de "Stream Starts per Second" (SPS), qui mesure le nombre de flux démarrés par seconde, pour vérifier l'état stable du système. Cette métrique est prévisible à une heure donnée. D'autres indicateurs, comme la latence des requêtes ou l'utilisation du processeur, peuvent révéler une dégradation des services, même si, du point de vue utilisateur, le système semble fonctionner correctement.
Ces expériences ont conduit les ingénieurs de Netflix à concevoir des services plus résilients. Ils adaptent leurs méthodes de développement en intégrant ces tests pour anticiper et gérer les éventuels problèmes réels.
Par exemple, ces expériences pourraient être appliquées chez Amazon. De manière similaire, ils pourraient tester la résilience en éteignant des instances de services ou en simulant des défaillances de régions AWS. Ils pourraient également simuler des erreurs avec les intermédiaires de paiement. Les métriques à observer pourraient être le nombre d'achats par minute ou le nombre d'ajouts au panier, pour regarder si le systeme reste stable.

4. La structure formelle de WebAssembly lui permet d'avoir une compilation rapide, une execution proche du code natif, mais aussi d'avoir une sémantique simple et facile à comprendre pour le développeur. Elle lui permet de faciliter la validation et la vérification du code et fait agir le code de manière déterministe. Elle permet aussi d'avoir une sécurité renforcée et rigoureuse. Ce point est notamment très important pour un langage web. Cependant, il reste très important pour WebAssembly d'être testé. Notamment, dans la conclusion de cet article, il est discuté de pouvoir ajouter de nouvelles fonctions au langage. Cela nécessite des tests afin de valider que le langage ne fait aucune régression et qu'aucun bug n'y est implenté accidentellement. De plus, WebAssembly étant un langage web, il est important d'être verifié et testé, afin de garder une fiabilité dans la sécurité ou dans toutes les fonctions mises à dispositions. L'aspect formel facilite seulement la vérification de notre langage mais ne l'en dispose pas. Les fonctions devront êtres vérifiées et validées comme pour les classes en programmation orientée objet. 

   
5.La spécification mécanisée offre une preuve complète et rigoureuse de la solidité du système.

Ce travail a permis de détecter des erreurs dans la spécification officielle, notamment des incohérences dans les règles de typage. Ces observations ont reconnus et résolu par le groupe de travail officiel.
Les 3 principales sont :
Les règles de propagation des exceptions ont été revues pour éviter des blocages imprévus
L’opération Return a été retravaillée pour corriger des erreurs dans son typage
Les invariants définissant les fonctions hôtes ont été renforcés pour garantir qu’ils n’entraînent pas de comportements imprévus ou d’erreurs à l’exécution.

La spécification a permis de développer des outils exécutables fiables : 
Un vérificateur de types vérifié, qui garantit que les programmes WebAssembly respectent les règles de typage 
Un interpréteur vérifié, qui assure une exécution fidèle aux sémantiques spécifiées.

La vérification a été réalisée à l’aide de l’outil formel Isabelle. Il y a eu un test de fait de l'interpreteur executable contre des moteurs majeur de WebAssembly. Pour à la fois validé l'interpreteur et de verrifier qu'il n'y ai pas de bug dans les outils commerciaux.  
