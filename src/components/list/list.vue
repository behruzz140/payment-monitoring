<template>
  <div class="expense-list" :class="{ 'dark-mode': isDarkMode }">
    <h2>Xarajatlar Ro'yxati</h2>
    
    <div class="filters">
      <div class="filter-group">
        <div class="date-filter">
          <label>Sana bo'yicha:</label>
          <input 
            type="date" 
            v-model="selectedDate"
            @change="filterExpenses"
          >
        </div>
        <div class="category-filter">
          <label>Kategoriya bo'yicha:</label>
          <select v-model="selectedCategory" @change="filterExpenses">
            <option value="">Barchasi</option>
            <option value="Transport">Transport</option>
            <option value="Oziq-ovqat">Oziq-ovqat</option>
            <option value="Kommunal">Kommunal</option>
            <option value="Ko'ngilochar">Ko'ngilochar</option>
            
            <option value="Boshqa">Boshqa</option>
          </select>
        </div>
        <button 
          class="clear-filter" 
          @click="clearFilters"
          v-if="selectedDate || selectedCategory"
        >
          Filtrlarni tozalash
        </button>
      </div>
    </div>

    <table v-if="filteredExpenses.length">
      <thead>
        <tr>
          <th>Sana</th>
          <th>Kategoriya</th>
          <th>Summa</th>
          <th>Amal</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="expense in filteredExpenses" :key="expense.id">
          <td>{{ formatDate(expense.date) }}</td>
          <td>{{ expense.category }}</td>
          <td>{{ formatAmount(expense.amount) }} so'm</td>
          <td>
            <button 
              @click="deleteExpense(expense)"
              class="delete-btn"
            >
              O'chirish
            </button>
          </td>
        </tr>
      </tbody>
      <tfoot>
        <tr class="total-row">
          <td colspan="2"><strong>Jami:</strong></td>
          <td colspan="2"><strong>{{ formatAmount(totalAmount) }} so'm</strong></td>
        </tr>
      </tfoot>
    </table>
    <p v-else class="no-expenses">Xarajatlar mavjud emas</p>
  </div>
</template>

<script>
import './list.css'
export default {
  name: 'XarajatlarRoyxati',
  props: {
    expenses: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      selectedDate: '',
      selectedCategory: ''
    }
  },
  computed: {
    isDarkMode() {
      return JSON.parse(localStorage.getItem('isDarkMode')) || false
    },
    filteredExpenses() {
      return this.expenses.filter(expense => {
        const dateMatch = !this.selectedDate || expense.date === this.selectedDate
        const categoryMatch = !this.selectedCategory || expense.category === this.selectedCategory
        return dateMatch && categoryMatch
      })
    },
    totalAmount() {
      return this.filteredExpenses.reduce((sum, expense) => sum + Number(expense.amount), 0)
    }
  },
  methods: {
    formatDate(date) {
      return new Date(date).toLocaleDateString('uz-UZ')
    },
    formatAmount(amount) {
      return Number(amount).toLocaleString('uz-UZ')
    },
    deleteExpense(expense) {
      const index = this.expenses.findIndex(e => e.id === expense.id)
      if (index !== -1) {
        this.$emit('delete-expense', index)
      }
    },
    filterExpenses() {
    },
    clearFilters() {
      this.selectedDate = ''
      this.selectedCategory = ''
    }
  }
}
</script>

