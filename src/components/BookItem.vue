<script setup>
import { ref } from 'vue'

const props = defineProps({
  book: {
    type: Object,
    required: true
  },
  categories: {
    type: Array,
    required: true
  }
})

const emit = defineEmits(['delete', 'update'])

const isEditing = ref(false)
const editedBook = ref({ ...props.book })

function startEditing() {
  editedBook.value = { ...props.book }
  isEditing.value = true
}

function updateBook() {
  if (!editedBook.value.title.trim() || !editedBook.value.author.trim()) {
    alert('Book title and author are required.')
    return
  }

  emit('update', { ...editedBook.value })
  isEditing.value = false
}

function cancelEditing() {
  editedBook.value = { ...props.book }
  isEditing.value = false
}

function deleteBook() {
  emit('delete', props.book.id)
}
</script>

<template>
  <article class="book-item">
    <div v-if="!isEditing" class="book-content">
      <div class="book-icon">📖</div>

      <div class="book-info">
        <div class="book-title-row">
          <h3>{{ book.title }}</h3>
          <span class="category-badge">{{ book.category }}</span>
        </div>
        <p class="author">by {{ book.author }}</p>

        <div class="book-details">
          <span>💰 ₱{{ Number(book.price).toFixed(2) }}</span>
          <span>📦 {{ book.quantity }} available</span>
        </div>
      </div>

      <div class="item-actions">
        <button class="btn btn-edit" @click="startEditing">Edit</button>
        <button class="btn btn-delete" @click="deleteBook">Delete</button>
      </div>
    </div>

    <div v-else class="edit-form">
      <div class="edit-grid">
        <div class="form-group">
          <label>Book Title</label>
          <input v-model="editedBook.title" type="text" />
        </div>

        <div class="form-group">
          <label>Author</label>
          <input v-model="editedBook.author" type="text" />
        </div>

        <div class="form-group">
          <label>Category</label>
          <select v-model="editedBook.category">
            <option v-for="category in categories" :key="category" :value="category">
              {{ category }}
            </option>
          </select>
        </div>

        <div class="form-group">
          <label>Price (₱)</label>
          <input v-model="editedBook.price" type="number" min="0" step="0.01" />
        </div>

        <div class="form-group">
          <label>Quantity</label>
          <input v-model="editedBook.quantity" type="number" min="0" />
        </div>
      </div>

      <div class="edit-actions">
        <button class="btn btn-secondary" @click="cancelEditing">Cancel</button>
        <button class="btn btn-primary" @click="updateBook">Save Changes</button>
      </div>
    </div>
  </article>
</template>
