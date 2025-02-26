<template>
  <div class="expense-report-container" :class="{ 'dark-mode': isDarkMode }">
    <div class="expense-report">
      <h2>Hisobot</h2>
      <div class="total">
        <h3>Jami Xarajat:</h3>
        <p>{{ formatAmount(totalExpense) }} so'm</p>
      </div>

      <div class="category-summary">
        <h3>Kategoriyalar bo'yicha:</h3>
        <div v-for="(amount, category) in categoryTotals" :key="category" class="category-item">
          <span>{{ category }}:</span>
          <span>{{ formatAmount(amount) }} so'm</span>
        </div>
      </div>
    </div>

    <div class="chart-container">
      <canvas ref="chartCanvas"></canvas>
    </div>
  </div>
</template>

<script>
import Chart from 'chart.js/auto'
import './monitoring.css'

export default {
  name: 'XarajatlarHisoboti',
  props: {
    expenses: {
      type: Array,
      required: true
    }
  },
  computed: {
    isDarkMode() {
      return JSON.parse(localStorage.getItem('isDarkMode')) || false
    },
    totalExpense() {
      return this.expenses.reduce((sum, expense) => sum + Number(expense.amount), 0)
    },
    categoryTotals() {
      return this.expenses.reduce((totals, expense) => {
        if (!totals[expense.category]) {
          totals[expense.category] = 0
        }
        totals[expense.category] += Number(expense.amount)
        return totals
      }, {})
    }
  },
  methods: {
    formatAmount(amount) {
      return Number(amount).toLocaleString('uz-UZ')
    },
    renderChart() {
      if (this.chartInstance) {
        this.chartInstance.destroy()
      }
      const ctx = this.$refs.chartCanvas.getContext('2d')
      this.chartInstance = new Chart(ctx, {
        type: 'pie',
        data: {
          labels: Object.keys(this.categoryTotals),
          datasets: [{
            data: Object.values(this.categoryTotals),
            backgroundColor: ['#ff6384', '#36a2eb', '#ffce56', '#4bc0c0', '#9966ff', '#ff9f40']
          }]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false
        }
      })
    }
  },
  watch: {
    expenses: {
      handler() {
        this.renderChart()
      },
      deep: true
    }
  },
  mounted() {
    this.renderChart()
  }
}
</script>


