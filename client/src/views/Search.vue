<template>
  <div class="search-wrapper">

    <div class="search-book-container">

      <h1>Books CRM</h1>

      <div class="books">
          <label for="title"></label>
          <input
            type="text"
            id="title"
            v-model="title"
            @keyup.enter="searchBooks"
            placeholder="Enter Book's title...">

        <p class="error" v-if="resultDB">We dont find anything. Try later</p>
      </div>

      <div
        class="books-container"
        v-if="ourBooks.length"
      >

        <h2>Books in our base </h2>
        <div class="single-book-container"
             v-for="(ourBook) in ourBooks"
             :item="ourBook"
             :key="ourBook.edition_key[0]">
          <img
            class="book-cover-img"
            v-if="ourBook.coverSrc"
            :src="ourBook.coverSrc"
            :alt="`Cover ${ourBook.title}`">

          <p class="authors"
             v-for="(oneAuthor , index) in ourBook.authors"
             :key="index"
          >
            Author: {{oneAuthor}}</p>

          <p>Title: {{ ourBook.title }}</p>

          <div class="bookButtons">
            <Details
              class="details"
              :showAddBtn="true"
              :src="ourBook.coverSrc"
              :book="ourBook"
              :authors="ourBook.authors"
            />

            <addToFav
            v-if="isLogin !== null"
            :src="`${ourBook.coverSrc}`"
            :alt="`Cover ${ourBook.title}`"
            :book="ourBook"
            :authors="ourBook.authors"
            :fromBase="true"
          />
            </div>
          </div>

      </div>

      <div
        class="books-container"
        v-if="books.length"
      >
          <h2>Book in Openlibrary</h2>
        <div class="single-book-container"
             v-for="(book) in books"
             :item="book"
             :key="book.key"
        >
          <img
            class="book-cover-img"
            v-if="book.cover_i"
               :src="`https://covers.openlibrary.org/b/id/${book.cover_i}-L.jpg`"
               :alt="`Cover ${book.title}`">
          <img
            class="book-cover-img"
            v-if="!book.cover_i"
            :src="defC"
            alt="book cover">
          <p>{{book.cover}}</p>

          <p class="authors"
             v-for="(author , index) in book.author_name"
             :key="index"
          >
            Author: {{author}}</p>

          <p>Title: {{ book.title }}</p>

        <div class="bookButtons">
          <Details
            class="details"
            :showAddBtn="true"
            :src="`https://covers.openlibrary.org/b/id/${book.cover_i}-L.jpg`"
            :book="book"
            :authors="book.author_name"
          />

          <addToFav
            v-if="isLogin !== null"
            :src="`https://covers.openlibrary.org/b/id/${book.cover_i}-L.jpg`"
            :alt="`Cover ${book.title}`"
            :book="book"
            :authors="book.author_name"
            :fromBase="false"
          />
        </div>

        </div>
      </div>

    </div>
  </div>
</template>

<script>
import connectWithApi from '../utils/connectWithApi';
import AddToFav from '../components/AddToFav.vue';
import Details from '../components/Details.vue';
import defultCover from '../assets/img/defultCover.png';
import ConnectWithServ from '../utils/connectWithServ';

export default {
  name: 'Search',
  components: {
    AddToFav,
    Details,
  },

  data() {
    return {
      result: false,
      resultDB: false,
      defC: defultCover,
      books: [],
      ourBooks: [],
      title: '',
      error: '',
      errDetails: '',
      imgSrc: '',
      author: '',
      isLogin: this.$cookies.get('auth'),
    };
  },
  methods: {
    async searchBooks() {
      this.resultDB = false;
      const ourBooks = await ConnectWithServ.getBooksFromServ(this.title);
      if (Array.isArray(ourBooks)) {
        this.ourBooks = ourBooks;
      }
      const result = await connectWithApi.getBooks(this.title);
      if (Array.isArray(result)) {
        this.books = result;
      }
      if (!result.length && !ourBooks.length) {
        this.resultDB = true;
      }
      console.log(!result.length && !ourBooks.length, this.resultDB);
      this.title = '';
    },
  },
};
</script>

<style scoped>

.search-wrapper {
  display: flex;
  flex-direction: column;
  margin: 7vh auto 0 2.5vw;
  width: 65vw
}

.books-container {
  display: flex;
  justify-content: space-around;
  flex-wrap: wrap;
  margin-top: 50px;
}

.single-book-container {
  min-width: 200px;
  height: 70vh;
  font-size: 3vh;
  margin:50px 0;
  width: 19vw;
}

.book-cover-img {
  width: 60%;
  height: 50%;
}

input{
  width: 30vw;
  display: block;
  margin: 15px auto;
  border-radius: 15px;
  border: 1px solid #e7b1b1;
  color: #e7b1b1;
  padding: 6px 10px;
  text-align: center;
  background-color: rgba(0,0,0, .4);
}

@media (min-width: 700px){
  h1{
    font-size: 3em;
  }
  .book-cover-img{
    height: 35%;
  }
  h2{
    width: 100%;
  }

}

</style>
