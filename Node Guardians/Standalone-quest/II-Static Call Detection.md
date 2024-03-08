The goal og this quest is to show us how to detect if a call is read-only (staticcall) or not.
It is possible to detect so uisng a try/catch pattern. We can try to deliberate chango to state. If the try except then it was a static call, other wise it is a regular call.

```Solidity
pragma solidity ^0.8.19;

contract Guard {
	event detector();

	function checkNotStaticCall() external {
		emit detector();
	}
	/// @notice Checks if the current call is a static call.
	/// @return isNotStatic true if this is not a static call, false otherwise.
	function activate() public returns (bool isNotStatic) {
		try this.checkNotStaticCall() {
			return true;
		} catch {
			return false;
		}
	}
}
```

-> In the end, detection of static call is not recommended and almost never used! Just a fun part of the EVM.