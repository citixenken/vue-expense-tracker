<template>
  <h3>Add new transaction</h3>
  <form id="form" @submit.prevent="onSubmit">
    <div class="form-control">
      <label for="text">Text</label>
      <input type="text" id="text" v-model="text" placeholder="Enter text..." />
    </div>
    <div class="form-control">
      <label for="amount">
        Amount <br />(negative => expense, positive => income)
      </label>
      <input
        type="text"
        id="amount"
        v-model="amount"
        placeholder="Enter amount..."
      />
    </div>
    <button class="btn">Add transaction</button>
  </form>
</template>

<script setup>
import { ref } from "vue";
import { useToast } from "vue-toastification";

const emit = defineEmits(["transactionSubmitted"]);
const toast = useToast();

const text = ref("");
const amount = ref("");

const onSubmit = () => {
  if (!text.value || !amount.value) {
    toast.error("Both fields are required!");
    return;
  }

  const parsedAmount = parseFloat(amount.value);
  if (
    Number.isNaN(parsedAmount) ||
    !/^\s*(\-|\+)?(\d+(\.\d+)?|Infinity)\s*$/.test(amount.value)
  ) {
    toast.error("Amount must be a number!");
    return;
  }

  const transactionData = {
    text: text.value,
    amount: parseFloat(parsedAmount),
  };

  emit("transactionSubmitted", transactionData);

  text.value = "";
  amount.value = "";
};
</script>
