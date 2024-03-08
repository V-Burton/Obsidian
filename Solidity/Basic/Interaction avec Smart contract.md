## Interaction de base

- Balance et Transfert
    - Utilisez '.Balance' pour consulter la balance d'une adresse.
    - Utilisez '.transfer' pour envoyer des ethers (en wei) à une adresse payable.

## Appels de Fonctions :

- '**send**' : Version bas niveau de 'transfer'. Retourne False si l'appel échoue. À manier avec prudence et toujours vérifier le résultat de retour.
- '**call**' : Permet d'interagir avec des contrats ne respectant pas l'ABI standard ou pour un contrôle plus direct sur l'encodage [[exemples| Exemple]]. Peut ajuster le gaz et les ethers envoyés.
- '**delegatecall**' : Exécute le code d'un autre smart contract en utilisant le stockage du contrat appelant. Utile pour des bibliothèques ou du code partagé.
- '**staticcall**' : Similaire à '**call**' mais revient en arrière si l'état est modifié pendant l'appel. De plus, il ne peut appeler que des fonctions pure et view.

## Sécurité

- Rester conscient que des appels à '**delegatecall**' et '**call**' peuvent modifier le contrat appelant de manière inattendue et peuvent être à l'origine de vulnérabilités.
- Utiliser donc des modificateurs de contrôle d'accès pour protéger les fonctions critiques.
- Vérifier toujours le succès des appels '**send**' et '**call**'.

### Contrôle du Gaz :

- Avec `call`, `delegatecall`, et `staticcall`, vous pouvez spécifier le montant de gaz et les ethers à envoyer.
- Utilisez `{gas: X, value: Y}` pour contrôler le gaz et la valeur envoyée.