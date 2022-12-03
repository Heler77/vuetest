<template>
    <div>
        <h1>Connect Wallet</h1>
        <!-- <button @click="" >Connect</button> -->
        <button class="button" @click="connectWallet" >Connect</button>
        <br>
        <br>
        <span>Address:  {{ walletAddress }} </span>
        <br>
        <span>SubAddress:  {{ walletAddress.substring(0, 4) + '...' }} </span>
        <br>
        <span> {{ networkMessage }} </span>
        <br>
        <br>
        <button class="button" @click="connectMetamaskMobile" >Connect on Mobile</button>
        <br>
        <br>
        <span>BNB Balance:  {{ walletBalance / 1000000000000000000 }} </span>
        <br>
        <span> {{needWallet}} </span>
        <span> BUSD Balance:  {{ BUSDBalance }} </span>
        <!-- <button @click="takeAddress"> Obterner Direccion de Wallet  </button> -->
        <!-- <span> Direccion de la wallet: {{ Address }}     </span> -->

<!-- Renderizado Condicional -->


    </div>
</template>

<script>

// Imports
//import connectWallet from '@/helpers/connectWallet.js'

import Web3 from "web3/dist/web3.min"
//import Web3 from 'web3' ==>> // Libreria incompatible con versiones de Webpack inferiores a la 5


export default {

    data() {
        return {
            walletAddress: "xxxxxxxxxxxxxxxxxxxxxx",
            needWallet: "",
            walletBalance: "",
            connected: false,
            networkMessage: "",
            BUSDBalance: 0
            // Address:  variable de la direccion de cartera
            // coneccted = false
        }
    },

    methods: {
        connectWallet() {
            //let web3Instance
            alert(window.navigator.vendor)
              const web3 = new Web3(window.ethereum)
              const NODE_URL = 'https://node.philcoin.io/97';
              const INTERFACE = require('../abi.json');

              const getMetamaskProvider  = async() =>  {
                  // check window ethereum provider
                  if (window.ethereum) {
                      try {
                          await window.ethereum.request({ method: "eth_requestAccounts" }); //window.ethereum.enable()

                          if(window.ethereum && window.ethereum.isMetamask ){alert(" alerta",typeof window.ethereum); alert("entrar en mobile"); }
                          //web3Instance = web3
                      } catch(error) {
                          console.log(error)
                      }
                      web3.eth.getAccounts( async(err, accounts) => {
                          //this.walletAddress;
                          if (err != null)
                          return console.log("NETWORK_ERROR")
                          if (accounts.length === 0) {
                          //this.walletAddress = "";
                          console.log("NO_LOGIN");
                          return;
                          }
                          this.walletAddress = await accounts[0];
                          this.walletBalance = await web3.eth.getBalance(accounts[0])
                          await getBUSDBalance(this.walletAddress)

                      });
                  }
                  else {
                  this.needWallet = "Please Install metamask"    //console.log('Please Install metamask')
                  }
              }

              const getNode = () => {
                  web3.eth.getNodeInfo(function(error, result){
                      if(error){
                      console.log( "error: " , error )
                      }
                      else{
                      console.log( "result: ", result )
                      }
                  })
              }
            // Init
                const chainRequire = async() => {
                    let chainId = await web3.eth.getChainId();

                        if (chainId == 56) {
                            //return true
                            console.log("This is the Binance MainNet")
                            this.networkMessage = "This is the Binance MainNet"
                        } else if (chainId == 97) {
                            //return true
                            console.log("This is the Binance TestNet")
                            this.networkMessage = "This is the Binance TestNet"
                        } else {
                            //return false;
                            console.log("This is not Binance Smart Chain")
                            this.networkMessage = "This is not Binance Smart Chain"
                        }
                }
            //end

              const chainTest = async() => {
              try {
                await window.ethereum.request({
                  method: 'wallet_switchEthereumChain',
                  params: [{ chainId: '0x61' }],
                });
              } catch (switchError) {
                // This error code indicates that the chain has not been added to MetaMask.
                if (switchError.code === 4902) {
                  try {
                    await window.ethereum.request({
                      method: 'wallet_addEthereumChain',
                      params: [
                        {
                          chainId: '0x61',
                          chainName: 'BSC Test',
                          rpcUrls: ['https://data-seed-prebsc-1-s1.binance.org:8545/'] /* ... */,
                        },
                      ],
                    });
                  } catch (addError) {
                    // handle "add" error
                  }
                }
                // handle other "switch" errors
              }
            }

          //const NODE_URL = 'https://node.philcoin.io/97';
          //const INTERFACE = require('../abi.json');

          const getBUSDBalance = async( accountAddress ) => {

            //const Web3 = require('web3');

                let web3NodeProvider = new Web3(new Web3.providers.HttpProvider(NODE_URL));

                //const contractAddress = '0x801Ea81b4d49c18800FAD8190C665ADcDE33c4F7'; // -->  Philcoin Contract Test Address
                const contractAddress = '0xeD24FC36d5Ee211Ea25A80239Fb8C4Cfd80f12Ee'; // --> BUSD Contract Test Address
                //const accountAddress =  '0x603731Df392e41144437611C11dE93949f3Dd94c';

                //async function getTokenBalance(accountAddress, contractAddress){}

                    const contract = await new web3NodeProvider.eth.Contract(INTERFACE, contractAddress);

                    const balance_wei = await contract.methods.balanceOf(accountAddress).call();

                    this.BUSDBalance = parseFloat(web3NodeProvider.utils.fromWei(balance_wei, 'ether'));

                /// Ejemplo

                //getTokenBalance(accountAddress, contractAddress).then(console.log).catch(console.log);

          }

    console.log('Implementacion del isConnected = ', window.ethereum.isConnected())



            getMetamaskProvider()
            chainTest()
            chainRequire()
            //getBUSDBalance(this.walletAddress)
            getNode()

        },

        connectMetamaskMobile() {
      if (navigator.userAgent.match(/Android/i) || navigator.userAgent.match(/webOS/i) || navigator.userAgent.match(/iPhone/i) || navigator.userAgent.match(/iPad/i) || navigator.userAgent.match(/iPod/i) || navigator.userAgent.match(/BlackBerry/i) || navigator.userAgent.match(/Windows Phone/i)) {
            console.log("Estás usando un dispositivo móvil!!");
            alert("Estás usando un dispositivo móvil!!")
            const dappUrl = window.location.href.split("//")[1].split("/")[0];
            const metamaskAppDeepLink = "https://metamask.app.link/dapp/" + dappUrl;
            window.open(metamaskAppDeepLink, "_parent");
            //this.connectWallet()
            alert("Fin del proceso")
        } else {
            console.log("No estás usando un móvil");
            alert("No estás usando un móvil")
        }
    }

    },

}

</script>

<style>
.button {
  background-color: #555555;
    border: none;
    color: white;
    padding: 15px 32px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
    border-radius: 18px;
}
</style>
