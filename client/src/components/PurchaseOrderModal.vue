<template>
  <Teleport to="body">
    <Transition name="modal">
      <div v-if="isOpen" class="modal-overlay" @click="close">
        <div class="modal-container" @click.stop>
          <div class="modal-header">
            <h3 class="modal-title">
              {{ mode === 'create' ? t('purchaseOrder.titleCreate') : t('purchaseOrder.titleView') }}
            </h3>
            <button class="close-button" @click="close">
              <svg width="20" height="20" viewBox="0 0 20 20" fill="none">
                <path d="M15 5L5 15M5 5L15 15" stroke="currentColor" stroke-width="2" stroke-linecap="round"/>
              </svg>
            </button>
          </div>

          <div class="modal-body">

            <!-- CREATE MODE -->
            <template v-if="mode === 'create' && backlogItem">

              <!-- Read-only item context -->
              <div class="item-context">
                <div class="context-heading">{{ t('purchaseOrder.itemContext') }}</div>
                <div class="context-grid">
                  <div class="context-item">
                    <span class="context-key">{{ t('purchaseOrder.itemName') }}</span>
                    <span class="context-value">{{ backlogItem.item_name }}</span>
                  </div>
                  <div class="context-item">
                    <span class="context-key">{{ t('purchaseOrder.itemSku') }}</span>
                    <span class="context-value sku">{{ backlogItem.item_sku }}</span>
                  </div>
                  <div class="context-item">
                    <span class="context-key">{{ t('purchaseOrder.quantityNeeded') }}</span>
                    <span class="context-value">{{ backlogItem.quantity_needed }}</span>
                  </div>
                </div>
              </div>

              <!-- Error message -->
              <div v-if="error" class="form-error">{{ error }}</div>

              <!-- Form fields -->
              <div class="po-form">
                <div class="form-group">
                  <label for="po-supplier">{{ t('purchaseOrder.supplierName') }} *</label>
                  <input
                    id="po-supplier"
                    v-model="form.supplierName"
                    type="text"
                    :placeholder="t('purchaseOrder.supplierNamePlaceholder')"
                    class="form-input"
                  />
                </div>

                <div class="form-row">
                  <div class="form-group">
                    <label for="po-quantity">{{ t('purchaseOrder.quantity') }} *</label>
                    <input
                      id="po-quantity"
                      v-model.number="form.quantity"
                      type="number"
                      min="1"
                      class="form-input"
                    />
                  </div>

                  <div class="form-group">
                    <label for="po-unit-cost">{{ t('purchaseOrder.unitCost') }} *</label>
                    <input
                      id="po-unit-cost"
                      v-model.number="form.unitCost"
                      type="number"
                      min="0"
                      step="0.01"
                      class="form-input"
                    />
                  </div>
                </div>

                <div class="form-group">
                  <label for="po-delivery">{{ t('purchaseOrder.expectedDelivery') }} *</label>
                  <input
                    id="po-delivery"
                    v-model="form.expectedDeliveryDate"
                    type="date"
                    class="form-input"
                  />
                </div>

                <div class="form-group">
                  <label for="po-notes">{{ t('purchaseOrder.notes') }}</label>
                  <textarea
                    id="po-notes"
                    v-model="form.notes"
                    :placeholder="t('purchaseOrder.notesPlaceholder')"
                    class="form-textarea"
                    rows="3"
                  ></textarea>
                </div>
              </div>
            </template>

            <!-- VIEW MODE -->
            <template v-else-if="mode === 'view'">
              <template v-if="backlogItem && backlogItem.purchase_order">
                <div class="po-view">
                  <div class="view-grid">
                    <div class="view-item">
                      <div class="view-label">{{ t('purchaseOrder.poId') }}</div>
                      <div class="view-value po-id">{{ backlogItem.purchase_order.id }}</div>
                    </div>
                    <div class="view-item">
                      <div class="view-label">{{ t('purchaseOrder.supplier') }}</div>
                      <div class="view-value">{{ backlogItem.purchase_order.supplier_name }}</div>
                    </div>
                    <div class="view-item">
                      <div class="view-label">{{ t('purchaseOrder.quantity') }}</div>
                      <div class="view-value">{{ backlogItem.purchase_order.quantity }}</div>
                    </div>
                    <div class="view-item">
                      <div class="view-label">{{ t('purchaseOrder.unitCost') }}</div>
                      <div class="view-value">{{ formatCurrency(backlogItem.purchase_order.unit_cost, currentCurrency) }}</div>
                    </div>
                    <div class="view-item view-item-highlight">
                      <div class="view-label">{{ t('purchaseOrder.total') }}</div>
                      <div class="view-value total">
                        {{ formatCurrency(backlogItem.purchase_order.quantity * backlogItem.purchase_order.unit_cost, currentCurrency) }}
                      </div>
                    </div>
                    <div class="view-item">
                      <div class="view-label">{{ t('purchaseOrder.expectedDelivery') }}</div>
                      <div class="view-value">{{ formatDate(backlogItem.purchase_order.expected_delivery_date) }}</div>
                    </div>
                    <div class="view-item">
                      <div class="view-label">{{ t('purchaseOrder.status') }}</div>
                      <div class="view-value">
                        <span class="status-badge" :class="getStatusClass(backlogItem.purchase_order.status)">
                          {{ backlogItem.purchase_order.status }}
                        </span>
                      </div>
                    </div>
                    <div class="view-item">
                      <div class="view-label">{{ t('purchaseOrder.createdDate') }}</div>
                      <div class="view-value">{{ formatDate(backlogItem.purchase_order.created_date) }}</div>
                    </div>
                    <div v-if="backlogItem.purchase_order.notes" class="view-item view-item-full">
                      <div class="view-label">{{ t('purchaseOrder.notes') }}</div>
                      <div class="view-value">{{ backlogItem.purchase_order.notes }}</div>
                    </div>
                  </div>
                </div>
              </template>
              <div v-else class="empty-state">
                {{ t('purchaseOrder.noData') }}
              </div>
            </template>

          </div>

          <div class="modal-footer">
            <template v-if="mode === 'create'">
              <button class="btn-secondary" @click="close" :disabled="submitting">
                {{ t('purchaseOrder.cancel') }}
              </button>
              <button
                class="btn-primary"
                @click="handleSubmit"
                :disabled="submitting || !isFormValid"
              >
                {{ submitting ? t('common.loading') : t('purchaseOrder.submit') }}
              </button>
            </template>
            <template v-else>
              <button class="btn-secondary" @click="close">
                {{ t('purchaseOrder.close') }}
              </button>
            </template>
          </div>
        </div>
      </div>
    </Transition>
  </Teleport>
</template>

<script setup>
import { ref, reactive, computed, watch } from 'vue'
import { useI18n } from '../composables/useI18n'
import { api } from '../api'
import { formatCurrency } from '../utils/currency'

const { t, currentCurrency } = useI18n()

const props = defineProps({
  isOpen: {
    type: Boolean,
    required: true
  },
  backlogItem: {
    type: Object,
    default: null
  },
  mode: {
    type: String,
    default: 'create'
  }
})

const emit = defineEmits(['close', 'po-created'])

const submitting = ref(false)
const error = ref(null)

const form = reactive({
  supplierName: '',
  quantity: '',
  unitCost: '',
  expectedDeliveryDate: '',
  notes: ''
})

// A form is valid when all required fields are filled with sensible values.
const isFormValid = computed(() => {
  return (
    form.supplierName.trim() !== '' &&
    Number(form.quantity) > 0 &&
    Number(form.unitCost) > 0 &&
    form.expectedDeliveryDate !== ''
  )
})

const resetForm = () => {
  form.supplierName = ''
  // Pre-populate quantity from the backlog item so the buyer has a sensible default.
  form.quantity = props.backlogItem?.quantity_needed ?? ''
  form.unitCost = ''
  form.expectedDeliveryDate = ''
  form.notes = ''
  error.value = null
  submitting.value = false
}

// Reset the form each time the modal opens so stale data is never shown.
watch(() => props.isOpen, (newVal) => {
  if (newVal) {
    resetForm()
  }
})

const close = () => {
  emit('close')
}

const handleSubmit = async () => {
  if (!isFormValid.value || !props.backlogItem) return

  submitting.value = true
  error.value = null

  try {
    const createdPO = await api.createPurchaseOrder({
      backlog_item_id: props.backlogItem.id,
      supplier_name: form.supplierName.trim(),
      quantity: Number(form.quantity),
      unit_cost: Number(form.unitCost),
      expected_delivery_date: form.expectedDeliveryDate,
      notes: form.notes.trim() || null
    })
    emit('po-created', createdPO)
    emit('close')
  } catch (err) {
    error.value = err.response?.data?.detail || 'Failed to create purchase order'
    console.error('PurchaseOrderModal: create failed', err)
  } finally {
    submitting.value = false
  }
}

const formatDate = (dateString) => {
  if (!dateString) return 'N/A'
  const date = new Date(dateString)
  if (isNaN(date.getTime())) return 'N/A'
  return date.toLocaleDateString('en-US', {
    year: 'numeric',
    month: 'long',
    day: 'numeric'
  })
}

// Map PO status values to design-token CSS classes.
const getStatusClass = (status) => {
  if (!status) return ''
  switch (status.toLowerCase()) {
    case 'pending':   return 'status-warning'
    case 'approved':  return 'status-info'
    case 'received':  return 'status-success'
    case 'cancelled': return 'status-danger'
    default:          return 'status-info'
  }
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 2000;
  padding: 1rem;
}

.modal-container {
  background: var(--surface);
  border-radius: 12px;
  box-shadow: 0 20px 50px rgba(0, 0, 0, 0.15);
  max-width: 600px;
  width: 100%;
  max-height: 90vh;
  overflow: hidden;
  display: flex;
  flex-direction: column;
}

.modal-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 1.5rem;
  border-bottom: 1px solid var(--border);
}

.modal-title {
  font-size: 1.25rem;
  font-weight: 700;
  color: var(--text);
  letter-spacing: -0.025em;
  margin: 0;
}

.close-button {
  background: none;
  border: none;
  color: var(--text-muted);
  cursor: pointer;
  padding: 0.5rem;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 6px;
  transition: all 0.15s ease;
}

.close-button:hover {
  background: var(--surface-2);
  color: var(--text);
}

.modal-body {
  flex: 1;
  overflow-y: auto;
  padding: 1.5rem;
}

.modal-footer {
  padding: 1.25rem 1.5rem;
  border-top: 1px solid var(--border);
  display: flex;
  justify-content: flex-end;
  gap: 0.75rem;
}

/* ── Item context (read-only) ─────────────────────────── */

.item-context {
  background: var(--bg);
  border: 1px solid var(--border);
  border-radius: 8px;
  padding: 1rem 1.25rem;
  margin-bottom: 1.5rem;
}

.context-heading {
  font-size: 0.75rem;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.06em;
  color: var(--text-muted);
  margin-bottom: 0.75rem;
}

.context-grid {
  display: flex;
  flex-wrap: wrap;
  gap: 1rem 2rem;
}

.context-item {
  display: flex;
  flex-direction: column;
  gap: 0.25rem;
}

.context-key {
  font-size: 0.75rem;
  font-weight: 600;
  color: var(--text-muted);
  text-transform: uppercase;
  letter-spacing: 0.04em;
}

.context-value {
  font-size: 0.938rem;
  font-weight: 500;
  color: var(--text);
}

.context-value.sku {
  font-family: 'Monaco', 'Courier New', monospace;
  color: var(--color-primary);
}

/* ── Form ─────────────────────────────────────────────── */

.form-error {
  background: var(--danger-soft);
  color: var(--danger-ink);
  border: 1px solid var(--danger);
  border-radius: 8px;
  padding: 0.75rem 1rem;
  font-size: 0.875rem;
  margin-bottom: 1.25rem;
}

.po-form {
  display: flex;
  flex-direction: column;
  gap: 1.25rem;
}

.form-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.form-group label {
  font-size: 0.875rem;
  font-weight: 600;
  color: var(--text-muted);
}

.form-input,
.form-textarea {
  padding: 0.625rem 0.875rem;
  border: 1px solid var(--border);
  border-radius: 8px;
  font-size: 0.938rem;
  font-family: inherit;
  color: var(--text);
  background: var(--surface);
  transition: border-color 0.15s ease;
}

.form-input:focus,
.form-textarea:focus {
  outline: none;
  border-color: var(--color-primary);
}

.form-textarea {
  resize: vertical;
  min-height: 72px;
}

/* ── View mode ────────────────────────────────────────── */

.po-view {
  /* no extra wrapper styling needed */
}

.view-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1.5rem;
}

.view-item {
  display: flex;
  flex-direction: column;
  gap: 0.375rem;
}

/* spans full row — used for notes */
.view-item-full {
  grid-column: 1 / -1;
}

/* subtle highlight for the total cost cell */
.view-item-highlight {
  background: var(--bg);
  border: 1px solid var(--border);
  border-radius: 8px;
  padding: 0.75rem 1rem;
}

.view-label {
  font-size: 0.75rem;
  font-weight: 700;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  color: var(--text-muted);
}

.view-value {
  font-size: 0.938rem;
  font-weight: 500;
  color: var(--text);
}

.view-value.po-id {
  font-family: 'Monaco', 'Courier New', monospace;
  color: var(--color-primary);
  font-size: 0.875rem;
}

.view-value.total {
  font-size: 1.25rem;
  font-weight: 700;
  color: var(--text);
}

/* ── Status badge ─────────────────────────────────────── */

.status-badge {
  display: inline-block;
  padding: 0.25rem 0.75rem;
  border-radius: 9999px;
  font-size: 0.813rem;
  font-weight: 600;
  text-transform: capitalize;
}

.status-badge.status-warning {
  background: var(--warning-soft);
  color: var(--warning-ink);
}

.status-badge.status-info {
  background: var(--color-primary-soft);
  color: var(--color-primary-hover);
}

.status-badge.status-success {
  background: var(--success-soft);
  color: var(--success-ink);
}

.status-badge.status-danger {
  background: var(--danger-soft);
  color: var(--danger-ink);
}

/* ── Empty state ──────────────────────────────────────── */

.empty-state {
  text-align: center;
  padding: 3rem 1rem;
  color: var(--text-muted);
  font-style: italic;
}

/* ── Buttons ──────────────────────────────────────────── */

.btn-secondary {
  padding: 0.625rem 1.25rem;
  background: var(--surface-2);
  border: 1px solid var(--border);
  border-radius: 8px;
  font-weight: 500;
  font-size: 0.875rem;
  color: #334155;
  cursor: pointer;
  transition: all 0.15s ease;
  font-family: inherit;
}

.btn-secondary:hover:not(:disabled) {
  background: var(--border);
  border-color: var(--border-strong);
}

.btn-secondary:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.btn-primary {
  padding: 0.625rem 1.25rem;
  background: var(--color-primary);
  border: none;
  border-radius: 8px;
  font-weight: 600;
  font-size: 0.875rem;
  color: #fff;
  cursor: pointer;
  transition: all 0.15s ease;
  font-family: inherit;
}

.btn-primary:hover:not(:disabled) {
  background: var(--color-primary-hover);
}

.btn-primary:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

/* ── Modal transition ─────────────────────────────────── */

.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.2s ease;
}

.modal-enter-from,
.modal-leave-to {
  opacity: 0;
}

.modal-enter-active .modal-container,
.modal-leave-active .modal-container {
  transition: transform 0.2s ease;
}

.modal-enter-from .modal-container,
.modal-leave-to .modal-container {
  transform: scale(0.95);
}
</style>
