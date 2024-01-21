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
import ccFacilitatorAbi from "./abis/ccFacilitator.json";
import ccReceiverAbi from "./abis/ccReceiver.json";
import erc20abi from "./abis/erc20.json";

import {ethers} from "ethers";

export default {
  name: "App",
  data() {
    return {
      facilitator: Object,
      receiver: Object,
      address: String,
      provider: Object,
      signer: Object,
      gho: Object,
      usdc: Object,
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
  async mounted() {
    await this.connect();
  },
  methods: {
    async connect() {
        const provider = new ethers.providers.Web3Provider(window.ethereum);
        this.provider = provider
        let signer = provider.getSigner();
        await provider.send("eth_requestAccounts", []);
        this.address = await signer.getAddress();

        console.log(this.address)
        this.facilitator = new ethers.Contract("0x6eF0D6F8714C570eBc575B39137662bfD8a913CA", ccFacilitatorAbi, signer);
        this.receiver = new ethers.Contract("0x16ffa647315b5c83234c1e3ff0814cf9ea3a7ef9", ccReceiverAbi, signer);
        this.gho = new ethers.Contract("0x57F324D62E8fCfCD64c3dc254a04f7a84363F9ef", erc20abi, signer);
        this.usdc = new ethers.Contract("0xd702baC2f43eB7B9aAD73b965210b241d873154C", erc20abi, signer);
    },
    async usdcToGho() {
      const tx = await this.usdc.approve("0x6eF0D6F8714C570eBc575B39137662bfD8a913CA", 10000000);
      await tx.wait()
      await this.facilitator.mintGHOForUSDC("10000000000000000000",this.address);
      alert("Success!")
    },
    async ghoToUsdc() {
      const tx = await this.gho.approve("0x6eF0D6F8714C570eBc575B39137662bfD8a913CA", "10000000000000000000");
      await tx.wait();
      await this.facilitator.redeemUSDCForGHO("10000000000000000000", this.address);
      alert("Success!")
    },
    async sendGhoToMumbai() {
      const tx = await this.gho.approve("0x6eF0D6F8714C570eBc575B39137662bfD8a913CA", "100000000000000000000");
      await tx.wait();
      const transferFee = await this.facilitator.calcTransferFee("10000000000000000000");
      console.log("transfer fee:", transferFee.toString())
      const fee = await this.facilitator.getRouterFee("12532609583862916517", "10000000000000000000",this.address);
      console.log(fee.toString())
      await this.facilitator.sendGHOCrossChain("12532609583862916517", "10000000000000000000",this.address, {value: fee.toString()});
      alert("Success!")
    },
    async handleSubmit(index) {
      console.log(index);
      if (index == 0) {
        await this.usdcToGho();
      } else if (index == 1) {
        await this.ghoToUsdc();
      } else {
        await this.sendGhoToMumbai();
      }
    },
  },
};
</script>


