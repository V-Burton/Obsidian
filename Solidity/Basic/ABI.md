## Introduction
L'Interface Binaire d'Application (ABI) correspond au "langage" qui permet à deux programmes de communiquer entre eux. Un contrat Ethereum est compilé en bytecode (série de 0 et 1 interprétable par l'EVM). Les interfaces web, elles, ne "parlent" pas en bytecode. Elles utilisent des langages de haut niveau comme JavaScript. Ainsi, l'ABI fait office de traducteur, permettant aux interfaces web d'interagir avec le contrat.
## Fonctionnement
L'ABI se représente sous une description JSON de toutes les fonctions publiques disponibles dans un smart contract. On trouve ainsi :

- Le nom de la fonction.
- Les types de paramètres d'entrée que la fonction attend.
- Les types de retour que la fonction fournit.
- D'autres informations comme la possibilité de recevoir des Ethers pour la fonction...

## Memo
Pensez à l'ABI comme à une "carte de menu" de votre smart contract. L'ABI est donc la clé pour permettre aux applications de communiquer avec les smart contracts. Il s'agit du pont entre le bas niveau et le haut niveau.

## Exemple
```JSON
[
    {
        "constant": true,
        "inputs": [],
        "name": "getNumber",
        "outputs": [
            {
                "name": "",
                "type": "uint256"
            }
        ],
        "payable": false,
        "stateMutability": "view",
        "type": "function"
    },
    {
        "constant": false,
        "inputs": [
            {
                "name": "_num",
                "type": "uint256"
            }
        ],
        "name": "setNumber",
        "outputs": [],
        "payable": false,
        "stateMutability": "nonpayable",
        "type": "function"
    }
]
```