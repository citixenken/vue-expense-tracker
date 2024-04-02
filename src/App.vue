<template>
  <Header />
  <div class="container">
    <!-- + => make them numbers -->
    <Balance :total="+total" />
    <IncomeExpenses :income="+income" :expenses="+expenses" />

    <TransactionList
      :transactions="transactions"
      @transactionDeleted="handleTransactionDeleted"
    />
    <AddTransaction @transactionSubmitted="handleTransactionSubmitted" />
  </div>
</template>

<script setup>
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpenses from "./components/IncomeExpenses.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";

import { useToast } from "vue-toastification";
const toast = useToast();

import { ref, computed, onMounted } from "vue";

const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem("transactions"));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

// get total
const total = computed(() => {
  return transactions.value
    .reduce((acc, t) => {
      return acc + t.amount;
    }, 0)
    .toFixed(2);
});

// get income
const income = computed(() => {
  return transactions.value
    .filter((t) => t.amount > 0)
    .reduce((acc, t) => {
      return acc + t.amount;
    }, 0)
    .toFixed(2);
});
// get expenses
const expenses = computed(() => {
  return transactions.value
    .filter((t) => t.amount < 0)
    .reduce((acc, t) => {
      return acc + t.amount;
    }, 0)
    .toFixed(2);
});

// add transaction
const handleTransactionSubmitted = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  });

  saveTransactionsToLocalStorage();
  toast.success("Transaction added successfully");
};

const generateUniqueId = () => {
  return Math.floor(Math.random() * 1_000_000);
};

// delete transaction
const handleTransactionDeleted = (id) => {
  transactions.value = transactions.value.filter((t) => t.id !== id);

  saveTransactionsToLocalStorage();
  toast.success("Transaction deleted successfully");
};

// save (added/deleted) transactions to localStorage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};
</script>
