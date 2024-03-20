## Introduction
- Le jeton:
	- Les jetons ERC-20 sont des jetons fongibles. Ils sont tous identiques. Chaque jeton est assigné à un propriétaire qui peut choisir de le transférer à une tierce partie ou autoriser une tierce partie à le recevoir à n'importe quel moment.

- Le contrat:
	- Il s'occupe de s'assurer de savoir quel utilisateur possède combien de jetons. Il contrôle les transferts en vérifiant les quantités détenues par chaque utilisateur avant un transfert. Il stocke les adresses des détenteurs de ses jetons. Pour des raisons de simplification et de généralisation, un modèle standard a été créé pour quiconque souhaiterait créer son propre jeton. Cela permet les interactions entre les différents contrats intelligents de manière fluide, notamment pour les DEX (bourses décentralisées).

## Creation d'un jeton
- Tout jeton possède ces trois attributs :
	- Nom
	- Symbole
	- Offre Totale (Total Supply) 
	Ces trois variables étant déclarées publiques, leurs getters seront automatiquement créés par Solidity.
	```solidity
	string public constant name = "Doubloon"; 
	string public constant symbol = "DBL"; 
	uint256 public totalSupply;
	```
## Balance et Transfer
Le contrat du jeton étant responsable de maintenir le compte des soldes de chaque utilisateur et de faciliter les transferts, on peut le considérer comme un registre comptable (Ledger) des jetons entre les utilisateurs.
#### Soldes des Jetons :
- Fonction balanceOf : Cette fonction interroge le nombre de jetons détenus par une adresse. Le tout est stocké dans une map déclarée en public. Encore une fois, il n'est pas nécessaire de créer un getter.
```Solidity
	mapping(address => uint256) public balanceOf;
```
### Token Transfer
##### Direct Transfer
-   Cette fonction va d'abord diminuer le solde de l'expéditeur du montant envoyé, puis augmenter celui du destinataire. Il est important de procéder dans cet ordre car sinon il risquerait d'y avoir un problème de double dépense.
- La fonction est censée renvoyer une erreur si jamais le montant du transfert dépasse le solde détenu par l'expéditeur.
```Solidity
function transfer(address _to, uint256 _amount) external returns (bool){

	require(balanceOf[msg.sender] >= _amount, "Insufficient funds");

	balanceOf[msg.sender] -= _amount;
	balanceOf[_to] += _amount;
	
	emit Transfer(msg.sender, _to, _amount);

return true;
}
```
##### Approved Token
- La deuxième façon d'envoyer des jetons est un peu différente mais très utile. L'expéditeur va d'abord déléguer ses droits à une autre adresse pour dépenser en son nom une certaine quantité de jetons, définie par lui-même. Ensuite, cette adresse pré-approuvée effectuera le transfert vers le destinataire.
- Cette méthode est très utilisée pour permettre à des contrats intelligents de dépenser des jetons au nom des utilisateurs, notamment pour les échangeurs décentralisés.
- Lorsque cette solution est choisie, il est nécessaire que l'utilisateur approuve au préalable l'adresse de dépense. Ensuite, on n'utilise pas la fonction _`transfer`_, mais _`transferFrom`_.
- Bien entendu, _`transferFrom`_ vérifie également que l'adresse de l'expéditeur possède bien les fonds requis et les droits de dépense.
```Solidity
function approve(address _spender, uint256 _amount) external returns (bool){

	allowance[msg.sender][_spender] = 0;
	allowance[msg.sender][_spender] = _amount;
	
	emit Approval(msg.sender, _spender, _amount);

	return true;
}

  

function transferFrom(address _from, address _to, uint256 _amount) external returns (bool){

	require(balanceOf[_from] >= _amount, "Insufficient funds");
	require(allowance[_from][msg.sender] >= _amount, "Insufficient allowance");

	allowance[_from][msg.sender] -= _amount;
	balanceOf[_from] -= _amount;
	balanceOf[_to] += _amount;
	
	emit Transfer(_from, _to, _amount);
	
	return true;
}
```

## Events
- Un event en Solidity est un message log stocké dans la blockchain quand une fonction prédéterminée d'un contrat est exécutée. Souvent des changements dans des variables.

- Les événements sont ajoutés par les développeurs pour permettre à des entités extérieures de récupérer des informations qui ne seraient pas stockées sur la blockchain.

- Pour ajouter un event, il faut utiliser cette syntaxe :
	```Solidity
	event myEvent(uint number);
	```
- Puis pour l'appeler on utilise:
	```Solidity
	emit myEvent(10);
	```