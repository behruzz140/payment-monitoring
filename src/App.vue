<template>
  <div class="app-container" :class="{ 'dark-mode': isDarkMode }">
    <div class="theme-toggle">
      <div class="jurnal-dark">
        <h1>Shaxsiy Xarajatlar Jurnali</h1>
        <button class="dark-btn" @click="toggleTheme">
          {{ isDarkMode ? '☀️' : '🌙' }}
        </button>
      </div>
    </div>
    <Payment @add-expense="addExpense" />
    <List :expenses="expenses" @delete-expense="deleteExpense" />
    <Monitoring :expenses="expenses" />
  </div>
</template>

<script>
import Payment from './components/payment/payment.vue'
import List from './components/list/list.vue'
import Monitoring from './components/monitoring/monitoring.vue'

export default {
  name: 'App',
  components: {
    Payment,
    List,
    Monitoring
  },
  data() {
    return {
      expenses: JSON.parse(localStorage.getItem('expenses')) || [],
      isDarkMode: JSON.parse(localStorage.getItem('isDarkMode')) || false
    }
  },
  methods: {
    addExpense(expense) {
      this.expenses.push(expense)
      this.saveToLocalStorage()
    },
    deleteExpense(index) {
      this.expenses.splice(index, 1)
      this.saveToLocalStorage()
    },
    saveToLocalStorage() {
      localStorage.setItem('expenses', JSON.stringify(this.expenses))
    },
    toggleTheme() {
      this.isDarkMode = !this.isDarkMode
      localStorage.setItem('isDarkMode', JSON.stringify(this.isDarkMode))
    }
  }
}
</script>

<style>
.dark-btn {
  width: 50px;
  height: 50px;
}

.jurnal-dark {
  display: flex;
  justify-content: space-around;
  align-items: center;
}

.app-container {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  transition: all 0.3s ease;
}

.app-container.dark-mode {
  background-color: #1a1a1a;
  color: #ffffff;
}

.dark-mode h1,
.dark-mode h2,
.dark-mode h3 {
  color: #ffffff;
}

.theme-toggle {
  text-align: right;
  margin-bottom: 20px;
}

.theme-toggle button {
  background: transparent;
  border: 1px solid #42b983;
  color: #42b983;
  padding: 8px 16px;
  border-radius: 4px;
  cursor: pointer;
}

.dark-mode .theme-toggle button {
  border-color: #42b983;
  color: #42b983;
}

h1 {
  text-align: center;
  color: #2c3e50;
  margin-bottom: 30px;
}
</style>