# lll-foundry
Contrato de muestra:

contracts/Bank.sol

solidity
pragma solidity ^0.8.0;

contract Bank {
    mapping(address => uint256) public balances;

    function deposit() public payable {
        balances[msg.sender] += msg.value;
    }

    function withdraw(uint256 amount) public {
        require(balances[msg.sender] >= amount, "Insufficient balance");
        balances[msg.sender] -= amount;
        payable(msg.sender).transfer(amount);
    }

    function getBalance() public view returns (uint256) {
        return balances[msg.sender];
    }
}

Pruebas:

test/Bank.t.sol

solidity
pragma solidity ^0.8.0;

import "foundry/Foundry.sol";
import "../contracts/Bank.sol";

contract BankTest is Foundry {
    Bank public bank;

    function setUp() public {
        bank = new Bank();
    }

    function testDeposit() public {
        bank.deposit{value: 1 ether}();
        assertEq(bank.getBalance(), 1 ether);
    }

    function testWithdraw() public {
        bank.deposit{value: 1 ether}();
        bank.withdraw(0.5 ether);
        assertEq(bank.getBalance(), 0.5 ether);
    }

    function testGetBalance() public {
        bank.deposit{value: 1 ether}();
        assertEq(bank.getBalance(), 1 ether);
    }

    // Pruebas fuzz
    function testFuzzDeposit(uint256 amount) public {
        bank.deposit{value: amount}();
        assertEq(bank.getBalance(), amount);
    }

    function testFuzzWithdraw(uint256 amount) public {
        bank.deposit{value: amount}();
        bank.withdraw(amount / 2);
        assertEq(bank.getBalance(), amount / 2);
    }
}

Cobertura:

foundry.config.js

module.exports = {
  // ...
  coverage: {
    enabled: true,
    reportDir: "coverage",
  },
};

Scripts de implementación:

scripts/deploy.js

const { ethers } = require("ethers");

const main = async () => {
  const network = process.argv[2];
  const provider = ethers.getDefaultProvider(network);
  const wallet = ethers.Wallet.fromMnemonic(process.env.MNEMONIC);
  const bank = await ethers.getContractFactory("Bank");
  const deployedBank = await bank.deploy();

  console.log(`Deployed Bank contract on ${network} at ${deployedBank.address}`);
};

main();

Clase para administrar redes:

lib/NetworkManager.sol

solidity
pragma solidity ^0.8.0;

contract NetworkManager {
    mapping(string => address) public networkToAddress;

    function deployContract(string memory network) public {
        // Implementación específica de cada red
    }

    function getAddress(string memory network) public view returns (address) {
        return networkToAddress[network];
    }
}

README:
)

La explicación sobre las pruebas fuzz y la configuración de los scripts de implementación se encuentra en el archivo README.

Cómo ejecutar:

1. Instale Foundry: npm install foundry
2. Compile el contrato: forge build
3. Ejecute las pruebas: forge test
4. Verifique la cobertura: forge coverage
5. Implemente el contrato en una red: node scripts/deploy.js <red>

Reemplaze <red> con la red deseada (por ejemplo, mainnet, ropsten, rinkeby, etc.).
