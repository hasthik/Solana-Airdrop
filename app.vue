<template>
  <div style="display: flex; justify-content: center; align-items: center;height: 90vh;">
  <div style="width: 400px;">
    <h1 style="text-align: center;">SOLANA AIR DROP</h1>
    <div>
      <label>Wallet Address:</label>
      <input type="text" v-model="walletAddress"  style="width: 400px;"/>
    </div>
    <div>
      <label>Amount of Coins:[Max 2 Coins per request]</label>
      <div style="display: flex;">
        <input type="text" v-model="amount"  style="width: 50%;"/>
        <button @click="display()" style="width: 50%; height:41px">{{displayText}}</button>
      </div>
    </div>
    <button  style="width: 100%;" @click="airDrop()">Claim Air Drop</button>
    <div style="text-align: center;">
      {{response}}
    </div>
  </div>

</div>
</template>

<script>
import { PublicKey, Connection, LAMPORTS_PER_SOL, clusterApiUrl, Keypair, SystemProgram, Transaction, sendAndConfirmTransaction } from '@solana/web3.js';

export default {
  data() {
    return {
      testnet: true,
      response: '',
      displayText: 'Devnet',
      networkText: 'devnet',
      networkLink: 'https://api.devnet.solana.com',
      network: null,
      connection: null,
      walletKeyPair: null,
      walletAddress: '',
      amount: 0,
    };
  },
  async mounted() {
    await this.connect();
  },
  methods: {
    display(){
      this.testnet = !this.testnet;
      if(this.testnet){
        this.displayText = 'Devnet';
        this.networkText = 'devnet',
        this.networkLink = 'https://api.devnet.solana.com"';
      }else{
        this.displayText = 'Testnet';
        this.networkText = 'testnet',
        this.networkLink = 'https://api.testnet.solana.com"';
      }
      this.connect();
    },
    async connect() {
      try {
        // console.log(clusterApiUrl(this.networkText));
        this.connection = new Connection(clusterApiUrl(this.networkText));
        const version = await this.connection.getVersion();
        this.network = `Connected to Solana v${version['solana-core']}`;
        this.walletKeyPair = Keypair.generate();
      } catch (err) {
        console.error(err);
      }
    },
    async airDrop() {
      try {
        const publicKey = new PublicKey(this.walletAddress);
        const connection = new Connection(clusterApiUrl(this.networkText), "confirmed");
        // const connection = new Connection("http://localhost:8899", "confirmed");
        const signature = await connection.requestAirdrop(publicKey, this.amount * LAMPORTS_PER_SOL)
        await connection.confirmTransaction(signature)
        this.response = 'Airdrop Sent';
      }
      catch (err) {
        console.log(err);
        this.response = 'Airdrop failed';
      }
    }
},
};
</script>



<style>
  @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@100;200;300;400;500;600;700&display=swap');
  @import url('https://fonts.googleapis.com/css2?family=Bodoni+Moda:opsz@6..96&display=swap');
    * {
    font-family: 'Poppins', sans-serif;
    /* font-family: 'Bodoni Moda', serif; */
    box-sizing: border-box;
  }
  h1 {
    /* font-family: 'Bodoni Moda', serif; */
    font-weight: 600;
  }
  label {
    display: block;
    margin-bottom: 5px;
  }
  input {
    display: block;
    margin-bottom: 20px;
    padding: 10px;
    border: 1px solid #ccc;
    width: 100%;
  }
  button {
    padding: 10px 20px;
    background: #000;
    color: #fff;
    border: none;
    cursor: pointer;
    width: 100%;
  }
</style>