# Blockchain-Simulation
import hashlib
import time
import json

class Block:
    def __init__(self, index, previous_hash, transactions, timestamp, nonce=0):
        self.index = index
        self.previous_hash = previous_hash
        self.transactions = transactions
        self.timestamp = timestamp
        self.nonce = nonce
        self.hash = self.calculate_hash()

    def calculate_hash(self):
        block_string = json.dumps(self.__dict__, sort_keys=True).encode()
        return hashlib.sha256(block_string).hexdigest()

    def mine_block(self, difficulty):
        target = '0' * difficulty
        while self.hash[:difficulty] != target:
            self.nonce += 1
            self.hash = self.calculate_hash()
        print(f"Block mined: {self.hash}")

class Blockchain:
    def __init__(self, difficulty=4):
        self.chain = [self.create_genesis_block()]
        self.difficulty = difficulty

    def create_genesis_block(self):
        return Block(0, "0", ["Genesis Block"], time.time())

    def get_latest_block(self):
        return self.chain[-1]

    def add_block(self, new_block):
        new_block.previous_hash = self.get_latest_block().hash
        new_block.mine_block(self.difficulty)
        self.chain.append(new_block)

    def is_chain_valid(self):
        for i in range(1, len(self.chain)):
            current_block = self.chain[i]
            previous_block = self.chain[i - 1]

            if current_block.hash != current_block.calculate_hash():
                print("Block has been tampered with!")
                return False

            if current_block.previous_hash != previous_block.hash:
                print("Blockchain integrity compromised!")
                return False
        return True

    def print_chain(self):
        for block in self.chain:
            print(f"Block {block.index} [Hash: {block.hash}, Previous Hash: {block.previous_hash}, Transactions: {block.transactions}, Nonce: {block.nonce}]")

# Create a blockchain
my_blockchain = Blockchain()

# Add blocks to the blockchain
my_blockchain.add_block(Block(1, "", ["Transaction 1"], time.time()))
my_blockchain.add_block(Block(2, "", ["Transaction 2", "Transaction 3"], time.time()))

# Print the blockchain
print("Blockchain:")
my_blockchain.print_chain()

# Validate the blockchain
print("\nBlockchain is valid:", my_blockchain.is_chain_valid())

# Tamper with the blockchain and validate again
print("\nTampering with the blockchain...")
my_blockchain.chain[1].transactions = ["Tampered Transaction"]
print("Blockchain is valid:", my_blockchain.is_chain_valid())
