# Trabalho sobre Blockchain - Aula se Segurança da Informação - Professor Bruno
Professor, Bruno Zolotareff dos Santos (https://www.linkedin.com/in/bzsantos/)

Luiz Antônio costa de Lima (luiz.antonio.c.lima@gmail.com)

Felipe de Queiroz de Souza (felpaojoj@gmail.com)

---------------------------------------------------------------------------------------------------------------------------------------------
Link do artigo: https://cse.sc.edu/~mgv/csce190f18/diPierro_mcs2017050092.pdf

---------------------------------------------------------------------------------------------------------------------------------------------
Explicação prévia/resumida:

Esta aplicação é uma implementação básica de uma blockchain em JavaScript. Ela demonstra os conceitos fundamentais de uma blockchain, incluindo:

1. Criação de blocos
2. Mineração de blocos (prova de trabalho)
3. Validação da cadeia
4. Adição de transações

A blockchain é representada por uma classe que contém uma cadeia de blocos e transações pendentes. Cada bloco contém um índice, timestamp, dados (transações), hash anterior e seu próprio hash. A prova de trabalho é implementada através de um algoritmo simples que busca um hash com um número específico de zeros no início.

Aqui está o código da aplicação:

```javascript
const crypto = require('crypto');

class Block {
    constructor(index, timestamp, data, previousHash = '') {
        this.index = index;
        this.timestamp = timestamp;
        this.data = data;
        this.previousHash = previousHash;
        this.hash = this.calculateHash();
        this.nonce = 0;
    }

    calculateHash() {
        return crypto.createHash('sha256').update(this.index + this.previousHash + this.timestamp + JSON.stringify(this.data) + this.nonce).digest('hex');
    }

    mineBlock(difficulty) {
        while (this.hash.substring(0, difficulty) !== Array(difficulty + 1).join("0")) {
            this.nonce++;
            this.hash = this.calculateHash();
        }

        console.log("Block mined: " + this.hash);
    }
}

class Blockchain {
    constructor() {
        this.chain = [this.createGenesisBlock()];
        this.difficulty = 4;
        this.pendingTransactions = [];
        this.miningReward = 100;
    }

    createGenesisBlock() {
        return new Block(0, "01/01/2023", "Genesis block", "0");
    }

    getLatestBlock() {
        return this.chain[this.chain.length - 1];
    }

    minePendingTransactions(miningRewardAddress) {
        const rewardTx = { from: null, to: miningRewardAddress, amount: this.miningReward };
        this.pendingTransactions.push(rewardTx);

        let block = new Block(this.chain.length, Date.now(), this.pendingTransactions, this.getLatestBlock().hash);
        block.mineBlock(this.difficulty);

        console.log('Block successfully mined!');
        this.chain.push(block);

        this.pendingTransactions = [];
    }

    addTransaction(transaction) {
        this.pendingTransactions.push(transaction);
    }

    getBalanceOfAddress(address) {
        let balance = 0;

        for (const block of this.chain) {
            for (const trans of block.data) {
                if (trans.from === address) {
                    balance -= trans.amount;
                }

                if (trans.to === address) {
                    balance += trans.amount;
                }
            }
        }

        return balance;
    }

    isChainValid() {
        for (let i = 1; i < this.chain.length; i++) {
            const currentBlock = this.chain[i];
            const previousBlock = this.chain[i - 1];

            if (currentBlock.hash !== currentBlock.calculateHash()) {
                return false;
            }

            if (currentBlock.previousHash !== previousBlock.hash) {
                return false;
            }
        }

        return true;
    }
}

// Exemplo de uso
let myCoin = new Blockchain();

console.log('Minerando bloco 1...');
myCoin.addTransaction({from: 'address1', to: 'address2', amount: 100});
myCoin.minePendingTransactions('miner-address');

console.log('Minerando bloco 2...');
myCoin.addTransaction({from: 'address2', to: 'address1', amount: 50});
myCoin.minePendingTransactions('miner-address');

console.log('Saldo de miner-address:', myCoin.getBalanceOfAddress('miner-address'));
console.log('Blockchain válida?', myCoin.isChainValid());

// Tentativa de adulteração
myCoin.chain[1].data[0].amount = 1000;
console.log('Blockchain válida?', myCoin.isChainValid());
```

Esta aplicação demonstra os conceitos básicos de uma blockchain, incluindo a criação e mineração de blocos, adição de transações, cálculo de saldos e validação da cadeia. É uma simplificação e não deve ser usada em produção, mas serve como um bom ponto de partida para entender os princípios fundamentais da tecnologia blockchain.
