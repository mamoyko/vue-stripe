<template>
  <div>
    <form @submit.prevent="submitPayment">
      <label for="card-element">
        Credit or debit card
      </label>
      <div id="card-element">
        <!-- A Stripe Element will be inserted here. -->
      </div>

      <!-- Used to display form errors. -->
      <div id="card-errors" role="alert"></div>

      <button type="submit">Submit Payment</button>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      stripe: null,
      card: null
    };
  },
  mounted() {
    // Load Stripe.js asynchronously
    const script = document.createElement('script');
    script.src = 'https://js.stripe.com/v3/';
    script.onload = () => {
      this.setupStripe();
    };
    document.body.appendChild(script);
  },
  methods: {
    setupStripe() {
      // Initialize Stripe.js
      this.stripe = Stripe('pk_test_51PBqOvRuNU3oIjnAsVmTyJG1gOlWPMvvFH5QoHWpfCrU8SldRa7Z5dqxLIeqOPIKqRlX3QcyWdpyu7N1OUY8ZjkJ00s3xOdPZz');
      
      // Create Elements
      const elements = this.stripe.elements();
      this.card = elements.create('card');
      this.card.mount('#card-element');

      // Handle validation errors
      this.card.addEventListener('change', (event) => {
        const displayError = document.getElementById('card-errors');
        if (event.error) {
          displayError.textContent = event.error.message;
        } else {
          displayError.textContent = '';
        }
      });
    },
    async submitPayment() {
      // Use Stripe.js to handle payment submission and process the payment
      const { paymentMethod, error } = await this.stripe.createPaymentMethod({
        type: 'card',
        card: this.card,
      });

      if (error) {
        // Display error to your customer
        console.error(error);
      } else {
        // Payment successful, process the paymentMethod.id
        console.log(paymentMethod);
      }
    },
  },
};
</script>
