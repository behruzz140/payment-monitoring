<template>
  <div class="expense-list" :class="{ 'dark-mode': isDarkMode }">
    <h2>Xarajatlar Ro'yxati</h2>
    <div class="filters">
      <div class="filter-group">
        <div class="date-filter">
          <label>Sana oralig'i:</label>
          <VueDatePicker v-model="dateRange" range @update:model-value="filterExpenses" format="yyyy-MM-dd"
            placeholder="     Boshlanish va tugash sanasini tanlang" class="data-tanlash" />
        </div>
        <div class="category-filter">
          <label>Kategoriya bo'yicha:</label>
          <select v-model="selectedCategory" @change="filterExpenses">
            <option value="">Barchasi</option>
            <option value="buyum">Buyum xaridi</option>
            <option value="Transport">Transport</option>
            <option value="Oziq-ovqat">Oziq-ovqat</option>
            <option value="Kommunal">Kommunal</option>
            <option value="Ko'ngilochar">Ko'ngilochar</option>
            <option value="Boshqa">Boshqa</option>
          </select>
        </div>
        <button class="clear-filter" @click="clearFilters" v-if="dateRange || selectedCategory">
          Filtrlarni tozalash
        </button>
      </div>
    </div>
    <table v-if="visibleExpenses.length">
      <thead>
        <tr>
          <th>Sana</th>
          <th>Kategoriya</th>
          <th>Summa</th>
          <th>Amal</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="expense in visibleExpenses" :key="expense.id">
          <td>{{ formatDate(expense.date) }}</td>
          <td>{{ expense.category }}</td>
          <td>{{ formatAmount(expense.amount) }} so'm</td>
          <td>
            <button @click="deleteExpense(expense)" class="delete-btn">
              O'chirish
            </button>
          </td>
        </tr>
      </tbody>
      <tfoot>
        <tr>
          <td colspan="4">
            <div v-if="filteredExpenses.length > 7" class="pagination-buttons">
              <button v-if="visibleCount < filteredExpenses.length" @click="loadMore" class="load-more">Yana</button>
              <button v-if="visibleCount > 7" @click="loadLess" class="load-less">Kamroq</button>
            </div>
          </td>
        </tr>
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
import './list.css';
import VueDatePicker from '@vuepic/vue-datepicker';
import '@vuepic/vue-datepicker/dist/main.css';

export default {
  name: 'XarajatlarRoyxati',
  components: {
    VueDatePicker
  },
  props: {
    expenses: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      dateRange: null,
      selectedCategory: '',
      visibleCount: 7
    }
  },
  computed: {
    isDarkMode() {
      return JSON.parse(localStorage.getItem('isDarkMode')) || false;
    },
    filteredExpenses() {
      return this.expenses.filter(expense => {
        const expenseDate = new Date(expense.date).toISOString().split('T')[0];
        let startMatch = true, endMatch = true;
        if (this.dateRange && this.dateRange.length === 2) {
          const [startDate, endDate] = this.dateRange.map(date => new Date(date).toISOString().split('T')[0]);
          startMatch = !startDate || expenseDate >= startDate;
          endMatch = !endDate || expenseDate <= endDate;
        }
        const categoryMatch = !this.selectedCategory || expense.category === this.selectedCategory;
        return startMatch && endMatch && categoryMatch;
      });
    },
    visibleExpenses() {
      return this.filteredExpenses.slice(0, this.visibleCount);
    },
    totalAmount() {
      return this.filteredExpenses.reduce((sum, expense) => sum + Number(expense.amount), 0);
    }
  },
  methods: {
    formatDate(date) {
      return new Date(date).toLocaleDateString('uz-UZ');
    },
    formatAmount(amount) {
      return Number(amount).toLocaleString('uz-UZ');
    },
    deleteExpense(expense) {
      const index = this.expenses.findIndex(e => e.id === expense.id);
      if (index !== -1) {
        this.$emit('delete-expense', index);
      }
    },
    filterExpenses() {
      this.visibleCount = 7;
    },
    clearFilters() {
      this.dateRange = null;
      this.selectedCategory = '';
      this.visibleCount = 7;
    },
    loadMore() {
      this.visibleCount += 7;
    },
    loadLess() {
      this.visibleCount = Math.max(7, this.visibleCount - 7);
    }
  }
}
</script>
