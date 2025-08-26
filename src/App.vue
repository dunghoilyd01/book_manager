<!-- src/App.vue -->
<script setup>
import { ref, computed } from "vue";
const books = ref([
  {
    id: 1,
    title: "The Pragmatic Programmer",
    author: "Andrew Hunt",
    year: 1999,
    genre: "Software Engineering"
  },
  {
    id: 2,
    title: "What is that?",
    author: "Jacky Lee",
    year: 2015,
    genre: "Horror"
  },
  {
    id: 3,
    title: "Warren the King",
    author: "Jack Ramadan",
    year: 2020,
    genre: "Thriller"
  }
]);
const newBook = ref({ title: "", author: "", year: "", genre: "" });
const appName = "📚 Book Manager";

function deleteBook(id) {
  books.value = books.value.filter(book => book.id != id);
}
function addBook(e){
  e.preventDefault();
  const id = Date.now();
  if (!newBook.value.author || !newBook.value.genre || !newBook.value.year || !newBook.value.title ) {
    alert("Please fill in all fields!");
    return
  }
  books.value.push({id, ...newBook.value});
  resetForm();
}
function formSubmitHandler(e) {
  if (editing.value){
    submitEditedBook(e);
    return;
  }
  if (!editing.value) 
  {
    addBook(e);
    return;
  }
}
function editBook(id){
  const book = books.value.find(b => b.id === id);
  editing.value = true;
  newBook.value = { ...book, id };
}
function submitEditedBook(e) {
  const idx = books.value.findIndex(b => b.id === newBook.value.id);
  books.value[idx] = { ...newBook.value };
  editing.value = false;
  resetForm();
}
const editing = ref(false);
const searchQuery = ref("");
const filteredBooks = computed(() => {
  if (!searchQuery.value) return books.value;
  const regexp = new RegExp(searchQuery.value, "i");
  return books.value.filter(book =>
    regexp.test(book.title) || regexp.test(book.author)
  );
});

function resetForm() {
  newBook.value = { title: "", author: "", year: "", genre: "" };
}

</script>
<template>
  <div class="container-fluid p-0">
    <nav class="navbar navbar-expand-sm navbar-dark bg-primary">
      <a class="navbar-brand ms-2" href="#">{{ appName }}</a>
      <div class="navbar-nav ms-auto me-2">
        <ul class="ms-auto navbar-nav">
          <li class="nav-item">
            <a class="nav-link text-white" href="#">About</a>
          </li>
          <li class = "nav-item">
            <a class="nav-link text-white" href="#">Contact</a>
          </li>
        </ul>
      </div>
    </nav>
    
    <div class="container">
      <div class="row">
        <div class="col-md-7 mb-3">
          <h4 class="mt-3 mb-3">Book List</h4>
          <div class="mb-2">
            <input
              type="text"
              class="form-control"
              placeholder="Enter Book name or Author to search"
              v-model="searchQuery"
              required
            />
          </div>
          <ul class="list-group">
            <li v-if="books.length === 0" class="list-group-item text-muted">
              No books yet. Add your first one!
            </li>
            <li v-for="book in filteredBooks" :key="book.id" class="list-group-item">
              <strong>{{ book.title }}</strong> - {{ book.author }} - ({{ book.year }} {{ book.genre }})
              <button class="btn btn-sm btn-danger me-1" @click="deleteBook(book.id)">Delete</button>
              <button class="btn btn-sm btn me-1 btn-secondary" @click="editBook(book.id)">Edit</button>
            </li>
          </ul>
        </div>

        <div class="col-md-5">
          <h4 class="mt-3">Add a New Book</h4>
          <div class="card shadow-sm">
            <div class="card-body">
              <form @submit.prevent="formSubmitHandler">
                <div class="mb-3">
                  <label class="form-label">Title</label>
                  <input
                    type="text"
                    class="form-control"
                    placeholder="Enter book title"
                    v-model="newBook.title"
                    required
                  />
                </div>
                <div class="mb-3">
                  <label class="form-label">Author</label>
                  <input
                    type="text"
                    class="form-control"
                    placeholder="Enter author name"
                    v-model="newBook.author"
                    required
                  />
                </div>
                <div class="mb-3">
                  <label class="form-label">Year</label>
                  <div class="input-group">
                    <span class="input-group-text">
                      <i class="bi bi-calendar3"></i>
                    </span>
                    <input
                      type="number"
                      class="form-control"
                      placeholder="e.g. 2020"
                      v-model.number="newBook.year"
                      required
                    />
                  </div>
                </div>
                <div class="mb-3">
                  <label class="form-label">Genre</label>
                  <input
                    type="text"
                    class="form-control"
                    placeholder="e.g. Science Fiction"
                    v-model="newBook.genre"
                    required
                  />
                </div>
                <button v-if="!editing" type="submit" class="btn btn-success w-100">
                  Add Book
                </button>
                <button v-if="editing" type="submit" class="btn btn-secondary w-100">
                  Save
                </button>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>