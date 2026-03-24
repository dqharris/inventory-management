<template>
  <div class="restocking">
    <div class="page-header">
      <h2>Restocking</h2>
      <p>Allocate budget and place restocking orders based on demand forecasts</p>
    </div>

    <!-- Budget slider card -->
    <div class="card">
      <div class="card-header">
        <h3 class="card-title">Budget Allocation</h3>
      </div>
      <div class="budget-controls">
        <label class="budget-label" for="budget-slider">Available Budget</label>
        <div class="budget-display">{{ formatCurrency(budget) }}</div>
        <input
          id="budget-slider"
          type="range"
          min="0"
          max="200000"
          step="1000"
          v-model.number="budget"
          class="budget-slider"
        />
        <div class="slider-range-labels">
          <span>$0</span>
          <span>$200,000</span>
        </div>
      </div>
    </div>

    <!-- Recommendations table card -->
    <div class="card">
      <div class="card-header">
        <h3 class="card-title">Recommended Items</h3>
        <span class="card-subtitle">Items sorted by demand gap — highest priority first</span>
      </div>

      <div v-if="fetchLoading" class="loading">Loading demand forecasts...</div>
      <div v-else-if="error && !successMessage" class="error">{{ error }}</div>
      <div v-else>
        <div v-if="candidateItems.length === 0" class="empty-state">
          No items require restocking at this time.
        </div>
        <div v-else class="table-container">
          <table>
            <thead>
              <tr>
                <th>SKU</th>
                <th>Item</th>
                <th>Gap (units)</th>
                <th>Unit Cost</th>
                <th>Total Cost</th>
                <th>Status</th>
              </tr>
            </thead>
            <tbody>
              <!-- Selected items — within budget -->
              <tr v-for="item in selectedItems" :key="item.item_sku">
                <td><strong>{{ item.item_sku }}</strong></td>
                <td>{{ item.item_name }}</td>
                <td>{{ item.gap.toLocaleString() }}</td>
                <td>{{ formatCurrency(item.unit_cost) }}</td>
                <td><strong>{{ formatCurrency(item.itemCost) }}</strong></td>
                <td><span class="badge info">Selected</span></td>
              </tr>
              <!-- Over-budget items — dimmed -->
              <tr v-for="item in overBudgetItems" :key="item.item_sku" class="dimmed">
                <td><strong>{{ item.item_sku }}</strong></td>
                <td>{{ item.item_name }}</td>
                <td>{{ item.gap.toLocaleString() }}</td>
                <td>{{ formatCurrency(item.unit_cost) }}</td>
                <td><strong>{{ formatCurrency(item.itemCost) }}</strong></td>
                <td><span class="badge warning">Over Budget</span></td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>

    <!-- Order summary + place order card -->
    <div class="card">
      <div class="card-header">
        <h3 class="card-title">Order Summary</h3>
      </div>

      <div class="summary-body">
        <!-- Budget usage progress bar -->
        <div class="budget-usage-row">
          <span class="usage-label">
            Budget used: <strong>{{ formatCurrency(totalCost) }}</strong> of <strong>{{ formatCurrency(budget) }}</strong>
          </span>
        </div>
        <div class="budget-bar-bg">
          <div
            class="budget-bar-fill"
            :style="{ width: budgetUsedPercent + '%' }"
          ></div>
        </div>

        <div class="items-selected-label">{{ selectedItems.length }} item{{ selectedItems.length !== 1 ? 's' : '' }} selected</div>

        <!-- Success message after placing order -->
        <div v-if="successMessage" class="success-box">
          {{ successMessage }}
        </div>

        <!-- Error message for order placement -->
        <div v-if="error && successMessage === null && !fetchLoading" class="error">{{ error }}</div>

        <button
          class="place-order-btn"
          :disabled="selectedItems.length === 0 || loading"
          @click="placeOrder"
        >
          {{ loading ? 'Placing...' : 'Place Order' }}
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, computed, onMounted } from 'vue'
import { api } from '../api'

export default {
  name: 'Restocking',
  setup() {
    const forecasts = ref([])
    const loading = ref(false)      // order placement loading
    const fetchLoading = ref(true)  // initial data fetch loading
    const error = ref(null)
    const budget = ref(50000)
    const successMessage = ref(null)

    const loadForecasts = async () => {
      fetchLoading.value = true
      error.value = null
      try {
        forecasts.value = await api.getDemandForecasts()
      } catch (err) {
        error.value = 'Failed to load demand forecasts.'
        console.error('Load forecasts error:', err)
      } finally {
        fetchLoading.value = false
      }
    }

    // candidateItems: items with a positive demand gap, sorted highest gap first.
    // gap = forecasted_demand - current_demand; items with gap <= 0 are excluded.
    // itemCost = gap * unit_cost (total cost to cover the gap for this item).
    const candidateItems = computed(() => {
      return forecasts.value
        .map(item => ({
          ...item,
          gap: item.forecasted_demand - item.current_demand,
          itemCost: (item.forecasted_demand - item.current_demand) * item.unit_cost
        }))
        .filter(item => item.gap > 0)
        .sort((a, b) => b.gap - a.gap)
    })

    // selectedItems: greedy selection — accumulate items in priority order until
    // adding the next item would exceed the available budget.
    const selectedItems = computed(() => {
      let runningTotal = 0
      const selected = []
      for (const item of candidateItems.value) {
        if (runningTotal + item.itemCost <= budget.value) {
          runningTotal += item.itemCost
          selected.push(item)
        }
      }
      return selected
    })

    // overBudgetItems: candidates not included because they exceeded the remaining budget.
    const overBudgetItems = computed(() => {
      const selectedSkus = new Set(selectedItems.value.map(i => i.item_sku))
      return candidateItems.value.filter(item => !selectedSkus.has(item.item_sku))
    })

    // totalCost: sum of all selected items' total costs.
    const totalCost = computed(() =>
      selectedItems.value.reduce((sum, item) => sum + item.itemCost, 0)
    )

    // budgetUsedPercent: capped at 100% for the progress bar width.
    const budgetUsedPercent = computed(() =>
      budget.value === 0 ? 0 : Math.min(100, (totalCost.value / budget.value) * 100)
    )

    const formatCurrency = (value) =>
      value.toLocaleString('en-US', { style: 'currency', currency: 'USD' })

    const placeOrder = async () => {
      loading.value = true
      error.value = null
      successMessage.value = null
      try {
        const now = new Date()
        const delivery = new Date(now)
        delivery.setDate(delivery.getDate() + 14) // 14-day lead time

        const orderData = {
          customer: 'Internal Restocking',
          status: 'Processing',
          source: 'restocking',
          order_date: now.toISOString(),
          expected_delivery: delivery.toISOString(),
          total_value: totalCost.value,
          items: selectedItems.value.map(item => ({
            sku: item.item_sku,
            name: item.item_name,
            quantity: item.gap,
            unit_price: item.unit_cost
          }))
        }

        const result = await api.createRestockingOrder(orderData)
        successMessage.value = `Order ${result.order_number} placed successfully. Expected delivery in 14 days.`
      } catch (err) {
        error.value = 'Failed to place order. Please try again.'
        console.error('Place order error:', err)
      } finally {
        loading.value = false
      }
    }

    onMounted(() => loadForecasts())

    return {
      forecasts,
      loading,
      fetchLoading,
      error,
      budget,
      successMessage,
      candidateItems,
      selectedItems,
      overBudgetItems,
      totalCost,
      budgetUsedPercent,
      formatCurrency,
      placeOrder
    }
  }
}
</script>

<style scoped>
/* Budget controls layout */
.budget-controls {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
  max-width: 560px;
}

.budget-label {
  font-size: 0.875rem;
  font-weight: 600;
  color: #64748b;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.budget-display {
  font-size: 2rem;
  font-weight: 700;
  color: #0f172a;
  letter-spacing: -0.025em;
  line-height: 1;
  margin-bottom: 0.25rem;
}

/* Range slider — cross-browser clean look */
.budget-slider {
  -webkit-appearance: none;
  appearance: none;
  width: 100%;
  height: 6px;
  border-radius: 3px;
  background: #e2e8f0;
  outline: none;
  cursor: pointer;
  transition: background 0.2s;
}

.budget-slider::-webkit-slider-thumb {
  -webkit-appearance: none;
  appearance: none;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: #2563eb;
  cursor: pointer;
  border: 2px solid white;
  box-shadow: 0 1px 4px rgba(37, 99, 235, 0.4);
  transition: box-shadow 0.2s;
}

.budget-slider::-webkit-slider-thumb:hover {
  box-shadow: 0 0 0 4px rgba(37, 99, 235, 0.15);
}

.budget-slider::-moz-range-thumb {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: #2563eb;
  cursor: pointer;
  border: 2px solid white;
  box-shadow: 0 1px 4px rgba(37, 99, 235, 0.4);
}

.slider-range-labels {
  display: flex;
  justify-content: space-between;
  font-size: 0.75rem;
  color: #94a3b8;
  margin-top: 0.125rem;
}

/* Card subtitle beside the card title */
.card-subtitle {
  font-size: 0.813rem;
  color: #64748b;
}

/* Empty state */
.empty-state {
  text-align: center;
  padding: 2.5rem;
  color: #64748b;
  font-size: 0.938rem;
}

/* Dimmed rows for over-budget items */
.dimmed {
  opacity: 0.45;
}

/* Order summary section */
.summary-body {
  display: flex;
  flex-direction: column;
  gap: 0.875rem;
  max-width: 560px;
}

.budget-usage-row {
  font-size: 0.938rem;
  color: #334155;
}

/* Budget progress bar */
.budget-bar-bg {
  height: 8px;
  background: #e2e8f0;
  border-radius: 4px;
  overflow: hidden;
}

.budget-bar-fill {
  height: 8px;
  background: #2563eb;
  border-radius: 4px;
  transition: width 0.3s ease;
}

.items-selected-label {
  font-size: 0.875rem;
  color: #64748b;
}

/* Success message box */
.success-box {
  background: #d1fae5;
  color: #065f46;
  border-radius: 8px;
  padding: 1rem;
  font-size: 0.938rem;
  font-weight: 500;
}

/* Place order button */
.place-order-btn {
  display: inline-block;
  background: #2563eb;
  color: white;
  padding: 0.625rem 1.5rem;
  border-radius: 6px;
  font-weight: 600;
  font-size: 0.938rem;
  border: none;
  cursor: pointer;
  transition: background 0.2s, opacity 0.2s;
  align-self: flex-start;
}

.place-order-btn:hover:not(:disabled) {
  background: #1d4ed8;
}

.place-order-btn:disabled {
  background: #94a3b8;
  cursor: not-allowed;
  opacity: 0.7;
}
</style>
