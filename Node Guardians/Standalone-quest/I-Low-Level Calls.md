## Part 1
### Calldata

- **Définition :** `calldata` est une zone de stockage spéciale pour les données d'entrée d'une transaction ou d'un appel de fonction. C'est un bytestring encodé qui représente les données envoyées avec l'appel de fonction. Ces données incluent la signature de la fonction (ou, plus précisément, les 4 premiers octets du hash Keccak-256 de la signature de la fonction) suivis des paramètres de la fonction encodés selon les spécifications de l'ABI.
- **Usage :** Lorsque vous appelez une fonction d'un contrat, que ce soit de l'extérieur de la blockchain ou d'un autre contrat, les données de cet appel doivent être encodées en `calldata`.

### Fonction `call()`

- **Définition :** La fonction `call()` est une méthode de bas niveau pour effectuer des appels de fonctions entre contrats. Elle prend comme argument le `calldata` nécessaire pour l'appel de fonction.
- **Usage :** `call()` est utilisée pour invoquer une fonction d'un autre contrat en passant le `calldata` comme paramètre. Elle retourne un booléen indiquant si l'appel a réussi ou non, ainsi que les données de retour éventuelles de l'appel de fonction, qui peuvent être décodées si nécessaire.

### Relation entre `calldata` et `call()`

- `calldata` est la représentation encodée de l'appel de fonction, y compris la fonction à appeler et les paramètres de cette fonction.
- La fonction `call()` utilise ce `calldata` pour effectuer l'appel de fonction effectif vers un autre contrat dans l'EVM (Ethereum Virtual Machine).
### Resume
En somme, `calldata` est la préparation des données d'appel sous une forme que l'EVM peut comprendre, tandis que `call()` est la méthode d'exécution de cet appel, utilisant le `calldata` préparé. Ils sont effectivement différents mais fonctionnent ensemble pour permettre des interactions contractuelles flexibles et dynamiques sur Ethereum.

## Part 2
  
La fonction `delegatecall()` en Solidity permet à un contrat d'emprunter et d'exécuter le code d'un autre contrat tout en opérant sur son propre stockage. C'est une fonctionnalité avancée utilisée principalement pour les bibliothèques de contrats ou les mises à jour de contrats permettant de conserver une logique contractuelle modulaire et upgradable. Voici un résumé de son fonctionnement et de ses caractéristiques principales :

### Fonctionnement de `delegatecall()`

- **Similitude avec `call()` :** `delegatecall()` fonctionne de manière similaire à `call()`, en ce sens qu'il est utilisé pour effectuer des appels entre contrats.
- **Différence Clé :** La différence principale est que le code appelé via `delegatecall()` s'exécute avec le stockage du contrat appelant, pas avec celui du contrat appelé. Cela signifie que toutes les modifications d'état effectuées par le code appelé sont appliquées au stockage du contrat appelant.

### Paramètres de `delegatecall()`

1. **Calldata :** Les données encodées de l'appel de fonction, incluant la signature de la fonction et les paramètres.
2. **Adresse :** L'adresse du contrat vers lequel l'appel est dirigé, c'est-à-dire l'adresse du contrat contenant le code à exécuter.

### Implications de `delegatecall()`

- **Code emprunté :** Le contrat appelant utilise le bytecode du contrat appelé comme s'il s'agissait de son propre code.
- **Stockage :** Toute modification de l'état réalisée par le code emprunté affecte le stockage du contrat appelant, pas celui du contrat d'où provient le code.
- **Variables inchangées :** Durant l'exécution du `delegatecall()`, les variables `msg.sender`, `msg.value`, et `address(this)` restent inchangées par rapport à leur contexte original dans le contrat appelant. Cela signifie que `msg.sender` reste l'adresse qui a initié l'appel original, `msg.value` est la quantité d'ether envoyée avec l'appel, et `address(this)` réfère toujours au contrat appelant, pas au contrat contenant le code exécuté.

### Usage et Considérations

- **Bibliothèques et Contrats Upgradables :** `delegatecall()` est souvent utilisé pour exécuter des bibliothèques de contrats ou permettre des mises à jour du code contractuel sans changer l'adresse du contrat principal.
- **Sécurité :** Étant donné ses implications sur le stockage et le comportement du contrat, `delegatecall()` doit être utilisé avec prudence. Il est crucial de s'assurer que le contrat appelé et le contrat appelant ont des layouts de stockage compatibles pour éviter des effets secondaires non désirés.
### Solution:
```Solidity
pragma solidity ^0.8.19;

interface IFarm2 {

	function driveTractor(address _tractor, bytes calldata _instructions) external;

}

contract Tractor {
 
	uint256 seedCount;
  
	function plantSeed() external {
		seedCount = 700007;
	}
}

contract Harvest{

	IFarm2 public farm;

	constructor(address _farm){
		farm = IFarm2(_farm);
	}

	function execute(address _tractor) public {
		farm.driveTractor(_tractor, abi.encodeWithSignature("plantSeed()"));
	}
}
```

### Resume
En résumé, `delegatecall()` offre une flexibilité puissante pour la réutilisation du code et la mise à jour des contrats, mais exige une compréhension approfondie de son impact sur le stockage et la gestion d'état des contrats pour éviter des vulnérabilités de sécurité.