<!-- ./src/App.vue -->
<script setup lang="ts">
import { ref } from 'vue'
import { type CURRENCIES, shop100Pay } from '@100pay-hq/checkout'

type PayWith100PayType = {
  name: string
  email: string
  phone: string
  currency: string
  amount: number
  publicKey: string
}

const PUBLIC_KEY =
  'LIVE;PK;eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhcHBJZCI6IjY1ZjljZjVhZTZjNTdlMDAyZDc5NmM1NiIsInVzZXJJZCI6IjY1ZjljZjVhZTZjNTdlMDAyZDc5NmM1MiIsImlhdCI6MTcxMDg3MDM2Mn0.l-XuOcbxZWBdRAVt5Mzz1v97uRSB40K3xrrGaSHg4N0'

const name = ref('')
const email = ref('')
const phone = ref('')
const currency = ref<CURRENCIES>('USD')
const amount = ref(0)

const payWith100Pay = ({ name, email, phone, currency, amount, publicKey }: PayWith100PayType) => {
  shop100Pay.setup({
    ref_id: '' + Math.floor(Math.random() * 1000000000 + 1),
    api_key: publicKey,
    customer: {
      user_id: '1', // optional
      name: name,
      phone,
      email
    },
    billing: {
      amount: amount,
      currency: currency, // or any other currency supported by 100pay
      description: 'Test Payment',
      country: 'US',
      vat: 10, //optional
      pricing_type: 'fixed_price' // or partial
    },
    metadata: {
      is_approved: 'yes',
      order_id: 'OR2', // optional
      charge_ref: 'REF' // optionalm, you can add more fields
    },
    call_back_url: 'http://localhost:8000/verifyorder/',
    onClose: () => {
      alert('You just closed the crypto payment modal.')
    },
    onPayment: (reference) => {
      console.log(`New Payment detected with reference ${reference}`)
    },
    onError: (error) => {
      // handle your errors, mostly caused by a broken internet connection.
      console.log(error)
      alert('Sorry something went wrong please try again.')
    },
    callback: (response) => {
      console.log(response)
    }
  })
}

const handleSubmit = (e: Event) => {
  e.preventDefault()
  console.log('Submitting')

  // call the function to initialize the payment and show the popup
  payWith100Pay({
    name: name.value,
    email: email.value,
    phone: phone.value,
    currency: currency.value,
    amount: amount.value,
    publicKey: PUBLIC_KEY
  })
}
</script>
<template>
  <form @submit="handleSubmit">
    <div className="wrapper">
      <!-- name -->
      <div className="input-group">
        <label htmlFor="name">Name</label>
        <input v-model="name" type="text" id="name" name="name" required />
      </div>
      <!-- email -->
      <div className="input-group">
        <label htmlFor="email">Email</label>
        <input v-model="email" type="email" id="email" name="email" required />
      </div>
      <!-- phone -->
      <div className="input-group">
        <label htmlFor="phone">Phone</label>
        <input v-model="phone" type="tel" id="phone" name="phone" required />
      </div>
      <!-- currency -->
      <div className="input-group">
        <label htmlFor="currency">Currency</label>
        <select v-model="currency" name="currency" id="currency" required>
          <option value="USD">USD</option>
          <option value="EUR">EUR</option>
          <option value="GBP">GBP</option>
        </select>
      </div>
      <!-- amount -->
      <div className="input-group">
        <label htmlFor="amount">Amount</label>
        <input
          v-model="amount"
          type="number"
          id="amount"
          name="amount"
          min="0.1"
          step="0.1"
          required
        />
      </div>
      <!-- submit -->
      <div className="action-cont">
        <button type="submit">Submit</button>
      </div>
    </div>
  </form>
</template>
