<template>
  <v-app>
    <v-app-bar app>
      <v-toolbar-title class="headline text-uppercase">
        <span>Vuetify</span>
        <span class="font-weight-light">MATERIAL DESIGN</span>
      </v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn
        text
        href="https://github.com/vuetifyjs/vuetify/releases/latest"
        target="_blank"
      >
        <span class="mr-2">Latest Release</span>
      </v-btn>
    </v-app-bar>

    <v-content>
      <v-container>
         <v-btn>
          <PlaidLink
            env="sandbox"
              publicKey="e18f7143ef846667a84bb797570052"
              clientName="Test App"
              product="transactions"
              v-bind="{ onSuccess }">
            Open Plaid Link
        </PlaidLink>
      </v-btn>

      <h3>Public Token: {{token}}</h3>
      <h3>Access Token {{accessToken}}</h3>
      <h3>Item Id {{itemId}}</h3>

      <v-btn
        @click="getTransactions"
      >
        get Transactions
      </v-btn>
      <v-btn @click="getAccounts">
        get Accounts
      </v-btn>
      <v-btn @click="getBalance">
        get Balance
      </v-btn>

      <v-list>
        <v-list-item v-for="item in transactions">
          <v-list-item-title>
            {{item.name}}
          </v-list-item-title>
          <v-list-item-action>
            ${{ item.amount}}
          </v-list-item-action>
        </v-list-item>
        
      </v-list>
      </v-container>
     
      
    </v-content>
  </v-app>
</template>

<script>
import PlaidLink from './components/PlaidLink';
import axios from 'axios'
import plaid from 'plaid'



export default {
  name: 'App',
  components: {
    PlaidLink,
  },
  data: () => ({
    token: null,
    accessToken: null,
    itemId: null,
    transactions: [],
    accounts: [],
    balance: null

  }),
  mounted(){
    axios.get('http://localhost:3000/').then(response => {
      console.log(response)
    })
  },
  methods: {
     onSuccess (token) {
       let vm = this
      console.log(token)
      this.token = token
      axios.post('http://localhost:3000/get_access_token', {
        headers: {
          Accept: 'application/json',
          'Content-Type': 'application/vnd.example.v1+json',
        },
        public_token: token
      }).then(response => {
        vm.accessToken = response.data.access_token
        vm.itemId = response.data.item_id
        console.log(response)
      }).catch(e => {
        console.log(e)
      })
      
    },
    getTransactions(){
      let vm = this
      
      axios.get('http://localhost:3000/transactions').then(response => {
        console.log(response)
        this.transactions = response.data.transactions.transactions
        console.log(response)
      }).catch(e => {
        console.log(e)
      })
    },
    getAccounts(){
      let vm = this
      axios.get('http://localhost:3000/accounts').then(response => {
        console.log(response)
        this.accounts = response
        console.log(response)
      }).catch(e => {
        console.log(e)
      })
    },
    getBalance(){
      let vm = this
      axios.get('http://localhost:3000/balance').then(response => {
        console.log(response)
        this.balance = response.data
        console.log(response)
      }).catch(e => {
        console.log(e)
      })
    },

  }
 
};
</script>
