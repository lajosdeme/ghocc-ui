<template>
  <div id="app" class="container mt-5">
    <h1>GHOCC - Peg stability and cross-chain GHO Facilitator</h1>
    <div class="row">
      <div
        class="col-lg-4 col-md-6 mb-4"
        v-for="(column, index) in columns"
        :key="index"
      >
        <div class="card">
          <div class="card-body">
            <h5 class="card-title">{{ column.title }}</h5>
            <form @submit.prevent="handleSubmit(index)">
              <div class="form-group">
                <label>{{ column.label }}</label>
                <input
                  type="number"
                  v-model="column.amount"
                  class="form-control"
                  placeholder="Amount"
                />
              </div>
              <button type="submit" class="btn btn-primary">
                {{ column.buttonTitle }}
              </button>
            </form>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<!-- Include Vue.js -->
<script>
export default {
  name: "App",
  data() {
    return {
      columns: [
        {
          title: "Swap USDC to GHO",
          label: "Swap USDC to GHO",
          amount: "",
          buttonTitle: "Swap",
        },
        {
          title: "Swap GHO for USDC",
          label: "Swap GHO for USDC",
          amount: "",
          buttonTitle: "Swap",
        },
        {
          title: "Send GHO to Polygon Mumbai",
          label: "Send GHO to Polygon Mumbai",
          amount: "",
          buttonTitle: "Send",
        },
      ],
    };
  },
  methods: {
    handleSubmit(index) {
      // Handle form submission for each column
      console.log(`Form submitted for column ${index + 1}`);
      console.log("Amount:", this.columns[index].amount);
    },
  },
};
</script>


