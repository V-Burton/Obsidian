
## Types
- uint:
- address: 
- string:
- mapping:
- bool:

## Visibility
- Public:
	- Une variable définie comme public signifie qu'elle est accessible par tous les utilisateurs et autres contrats intelligents. 
	- Un getter est automatiquement créé pour elle à la compilation. 
	- Un appel direct à celle-ci est possible au sein du même contrat intelligent. 
	- Un appel externe peut être réalisé avec la syntaxe this.x. Dans les deux cas d'appel, c'est le getter qui est appelé et lit à partir du stockage.
- Internal:
	- Les variables internes ne sont accessibles uniquement depuis le smart contract dans lequel elles sont définies. 
	- C'est la visibilité par défaut des variables dans un smart contract.
- Private:
	- Les fonctions privées sont comme les fonctions internes, non visibles depuis l'extérieur. Mais en plus, elles ne sont pas accessibles au contrat dérivé de l'original.