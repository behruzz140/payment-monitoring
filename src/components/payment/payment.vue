<template>
  <div class="expense-form" :class="{ 'dark-mode': isDarkMode }">
    <h2>Yangi Xarajat Qo'shish</h2>
    <form @submit.prevent="submitExpense">
      <div class="form-group">
        <label>Summa:</label>
        <input 
          type="number" 
          v-model="expense.amount" 
          required
          min="0"
        >
      </div>
      
      <div class="form-group">
        <label>Kategoriya:</label>
        <select v-model="expense.category" required>
          <option value="Oziq-ovqat">Oziq-ovqat</option>
          <option value="Transport">Transport</option>
          <option value="Ko'ngilochar">Ko'ngilochar</option>
          <option value="Kommunal">Kommunal</option>
          <option value="Boshqa">Boshqa</option>
        </select>
      </div>
      
      <div class="form-group">
        <label>Sana:</label>
        <input 
          type="date" 
          v-model="expense.date" 
          :max="maxDate"
          required
        >
      </div>
      
      <button type="submit">Qo'shish</button>
    </form>
  </div>
</template>

<script>
import './payment.css'
export default {
  name: 'YangiXarajat',
  data() {
    const today = new Date().toISOString().split('T')[0]
    return {
      expense: {
        amount: '',
        category: '',
        date: today
      },
      maxDate: today
    }
  },
  computed: {
    isDarkMode() {
      return JSON.parse(localStorage.getItem('isDarkMode')) || false
    }
  },
  methods: {
    submitExpense() {
      this.$emit('add-expense', {
        ...this.expense,
        id: Date.now()
      })
      this.expense = {
        amount: '',
        category: '',
        date: this.maxDate
      }
    }
  }
}
</script>

