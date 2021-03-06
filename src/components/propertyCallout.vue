<template>
  <div class="wrapper grid-y">
    <div class="callout columns small-24">
      <div class="columns small-24 medium-6 flex-div">
        <h4>Balance Due</h4>
        <h2 class="header-no-margin">
          <b>{{ transform.currency.transform(propertyBalance) }}</b>
        </h2>
        <p class="p-margin">
          Includes Payments Through: {{ transform.date.transform(tipsData.lastPaymentPostedDate) }}
        </p>
      </div>
      <div class="columns small-24 medium-6 flex-div div-padding-and-margin">
        <e-pay-form
          :options="ePayOptions"
          :slots="ePaySlots"
        />
        <p
          v-if="propertyBalance > 0"
          class="p-margin"
        >
          Or pay by phone (877) 309-3710.
        </p>
      </div>
      <div class="columns small-24 medium-6 flex-div div-padding-and-margin">
        <a
          id="plans-button"
          href="#"
          class="button"
          @click.prevent="plansButtonAction"
        >
          Payment Plans
        </a>
        <p class="p-margin">
          Need help with your bill? We offer payment options and assistance plans.
        </p>
      </div>
      <div class="columns small-24 medium-6 flex-div div-padding-and-margin">
        <a
          id="plans-button"
          href="#"
          class="button"
          @click.prevent="couponButtonAction"
        >
          Print Payment Coupon
        </a>
        <!-- <p class="p-margin">Print a payment coupon.</p> -->
      </div>
    </div>
  </div>
</template>

<script>
import transforms from '../general/transforms.js';
import generateBillingXml from '../util/generate-billing-xml';

export default {
  computed: {
    propertyBalance() {
      return this.$store.state.appData.propertyBalance;
    },
    tipsData() {
      if (this.$store.state.sources.tips.data.data) {
        return this.$store.state.sources.tips.data.data;
      }
      return {};

    },
    ePayOptions() {
      const options = {
        height: 50,
        width: 160,
        fontSize: 25,
        // fontColor: 'rgb(68, 68, 68)',
        actionAddress: 'https://epay.phila.gov/PaymentCenter/Gateway1/InitiatePurchase.aspx',
      };
      return options;
    },
    ePaySlots() {
      const slots = {
        buttonAction: function() {
          const data = {
            'accountNum': this.tipsData.accountNum,
            'totalDue': 0,
            'balances': this.tipsData,
            'address': {
              'streetAddress': this.tipsData.property.address,
              'zipCode': this.$store.state.geocode.data.properties.zip_code,
            },
          };
          return generateBillingXml(data);
        },
        text:'Pay Now',
      };
      return slots;
    },
    transform() {
      return transforms;
    },
  },
  methods: {
    plansButtonAction() {
      window.open('https://www.phila.gov/services/payments-assistance-taxes/payment-plans/', '_blank');
    },
    couponButtonAction() {
      window.open('https://ework.phila.gov/revenue/default?realestate=true', '_blank');
    },
  },
};
</script>

<style scoped>

.button {
  padding: 10px;
  font-weight: bold !important;
}

.callout {
  margin-top: 1rem;
  position: inherit;
  height: auto;
}

.flex-div {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.div-padding-and-margin {
  padding-top: 15px;
  margin-bottom: 10px;
}

.header-no-margin {
  margin-bottom: 0px;
  margin-top: 0px;
}

.p-margin {
  margin: auto;
  max-width: 100%;
}

</style>
