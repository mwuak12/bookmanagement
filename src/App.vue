<script setup>
import { ref, computed, onMounted } from 'vue'
import BookItem from './components/BookItem.vue'

const books = ref([])

const newBook = ref({
  title: '',
  author: '',
  category: 'Fiction',
  price: '',
  quantity: ''
})

const searchBook = ref('')

const categories = ['Fiction', 'Non-Fiction', 'Fantasy', 'Romance', 'Science', 'History', 'Self-Help']

const filteredBooks = computed(() => {
  const search = searchBook.value.trim().toLowerCase()

  if (!search) return books.value

  return books.value.filter(book =>
    book.title.toLowerCase().includes(search) ||
    book.author.toLowerCase().includes(search) ||
    book.category.toLowerCase().includes(search)
  )
})

function addBook() {
  if (
    !newBook.value.title.trim() ||
    !newBook.value.author.trim() ||
    newBook.value.price === '' ||
    newBook.value.quantity === ''
  ) {
    alert('Please fill in all required fields.')
    return
  }

  books.value.push({
    id: Date.now(),
    title: newBook.value.title.trim(),
    author: newBook.value.author.trim(),
    category: newBook.value.category,
    price: Number(newBook.value.price),
    quantity: Number(newBook.value.quantity)
  })

  resetForm()
  saveBooks()
}

function deleteBook(id) {
  if (!confirm('Are you sure you want to delete this book?')) return

  books.value = books.value.filter(book => book.id !== id)
  saveBooks()
}

function updateBook(updatedBook) {
  const index = books.value.findIndex(book => book.id === updatedBook.id)

  if (index !== -1) {
    books.value[index] = {
      ...updatedBook,
      price: Number(updatedBook.price),
      quantity: Number(updatedBook.quantity)
    }
  }

  saveBooks()
}

function resetForm() {
  newBook.value = {
    title: '',
    author: '',
    category: 'Fiction',
    price: '',
    quantity: ''
  }
}

function saveBooks() {
  localStorage.setItem('books', JSON.stringify(books.value))
}

onMounted(() => {
  const savedBooks = localStorage.getItem('books')

  if (savedBooks) {
    books.value = JSON.parse(savedBooks)
  } else {
    books.value = [
      {
        id: 1,
        title: 'The Alchemist',
        author: 'Paulo Coelho',
        category: 'Fiction',
        price: 280,
        quantity: 8
      },
      {
        id: 2,
        title: 'Atomic Habits',
        author: 'James Clear',
        category: 'Self-Help',
        price: 450,
        quantity: 15
      },
      {
        id: 3,
        title: 'Harry Potter',
        author: 'J.K. Rowling',
        category: 'Fantasy',
        price: 350,
        quantity: 10
      }
    ]
    saveBooks()
  }
})
</script>

<template>
  <div class="app-wrapper">
    <header class="header">
      <div class="brand">
        <div class="brand-icon">📚</div>
        <div>
          <h1>Book Management System</h1>
          <p>Manage your book collection easily</p>
        </div>
      </div>
    </header>

    <main class="container">
      <section class="form-card">
        <div class="section-heading">
          <div>
            <h2>Add New Book</h2>
            <p>Create a new book record.</p>
          </div>
          <span class="crud-badge">CREATE</span>
        </div>

        <form class="book-form" @submit.prevent="addBook">
          <div class="form-group">
            <label for="title">Book Title</label>
            <input id="title" v-model="newBook.title" type="text" placeholder="Enter book title" />
          </div>

          <div class="form-group">
            <label for="author">Author</label>
            <input id="author" v-model="newBook.author" type="text" placeholder="Enter author name" />
          </div>

          <div class="form-group">
            <label for="category">Category</label>
            <select id="category" v-model="newBook.category">
              <option v-for="category in categories" :key="category" :value="category">
                {{ category }}
              </option>
            </select>
          </div>

          <div class="form-group">
            <label for="price">Price (₱)</label>
            <input id="price" v-model="newBook.price" type="number" min="0" step="0.01" placeholder="0.00" />
          </div>

          <div class="form-group">
            <label for="quantity">Quantity</label>
            <input id="quantity" v-model="newBook.quantity" type="number" min="0" placeholder="0" />
          </div>

          <div class="form-actions">
            <button type="button" class="btn btn-secondary" @click="resetForm">Clear</button>
            <button type="submit" class="btn btn-primary">＋ Add Book</button>
          </div>
        </form>
      </section>

      <section class="list-card">
        <div class="section-heading list-heading">
          <div>
            <h2>Book Collection</h2>
            <p>View, update, and delete your books.</p>
          </div>
          <span class="count-badge">{{ books.length }} {{ books.length === 1 ? 'Book' : 'Books' }}</span>
        </div>

        <div class="search-box">
          <span>🔎</span>
          <input v-model="searchBook" type="text" placeholder="Search by title, author, or category..." />
        </div>

        <div v-if="filteredBooks.length" class="book-list">
          <BookItem
            v-for="book in filteredBooks"
            :key="book.id"
            :book="book"
            :categories="categories"
            @delete="deleteBook"
            @update="updateBook"
          />
        </div>

        <div v-else class="empty-state">
          <div class="empty-icon">📖</div>
          <h3>No books found</h3>
          <p>Add a new book or try a different search.</p>
        </div>
      </section>
    </main>

    <footer>
      <p>Vue.js CRUD Activity • Book Management System</p>
    </footer>
  </div>
</template>
