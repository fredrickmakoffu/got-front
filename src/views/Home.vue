<template>
  <div class="container p-3" id="intro">
    <div class="card mb-4 bg-light rounded-3 intro-background">
      <div class="backdrop bg-dark position-absolute w-100 h-100" style="opacity: 0.4"></div>

      <div class="p-5" style="z-index: 2">
        <div class="container-fluid">
          <div class="row">
            <div class="col-md-6 col-sm-12">
              <p class="display-6 text-white mt-4 fw-bold">Topup Mama does Game of Thrones too!</p>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="container">
    <div class="row">
      <div class="col ps-4">
        <h4 class="text-dark fw-bold">All our Books</h4>
        <div class="bg-dark rounded-3" style="padding-top: 3.5px; width: 14.5%"></div>
      </div>
    </div>

    <div class="row" style="margin-bottom: 3rem;" v-if=!book_opened>
      <div class="col-md-6 col-sm-12 col-12" v-for="({name, isbn, released, authors, publisher, url}, index) in books" :key="index">

        <div class="card mt-4" style="border-color: #eee; box-shadow: 0 4px 8px #eee;">
          <div class="card-body pb-0">
            <p class="fs-6 mb-0">{{ name }}</p>
            <p class="fst-italic text-secondary" style="font-size: 11px">{{ released.substring(0, 10) }} | {{ authors[0] }}</p>

            <p class="fs-6 mb-1"><span class="fw-bold">ISBN</span>: {{ isbn }}</p>

            <p class="fs-6"><span class="fw-bold">Publisher</span>: {{ publisher }}</p>
          </div>

          <div class="card-footer bg-white border-0 pt-0">
            <button class="btn btn-dark btn-sm float-end" @click="getBook(url, $event)">
              Details <font-awesome-icon class="ms-2" :icon="['fa', 'arrow-right']"></font-awesome-icon>
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="row" style="margin-bottom: 3rem;" v-if=book_opened>
      <div class="col-md-12 col-sm-12 col-12">
        <div class="card mt-4" style="border-color: #eee; box-shadow: 0 4px 8px #eee;">
          <div class="card-header border-0 bg-white mt-1">
            <div class="col">
              <font-awesome-icon :icon="['fa', 'arrow-left']" style="cursor: pointer" @click="this.book_opened = false"></font-awesome-icon>
            </div>
          </div>
          <div class="card-body">
            <p class="fs-6 mb-0">{{ book.name }}</p>
            <p class="fst-italic text-secondary" style="font-size: 11px">{{ book.released.substring(0, 10) }} | {{ book.authors[0] }}</p>
            <div class="row">
              <div class="col-md-6 col-sm-12">
                <p class="fs-6 mb-1"><span class="fw-bold">ISBN</span>: {{ book.isbn }}</p>

                <p class="fs-6 mb-1"><span class="fw-bold">Country</span>: {{ book.country }}</p>
              </div>

              <div class="col-md-6 col-sm-12">
                <p class="fs-6 mb-1"><span class="fw-bold">Media Type</span>: {{ book.mediaType }}</p>

                <p class="fs-6 mb-1"><span class="fw-bold">Number of Pages</span>: {{ book.numberOfPages }}</p>
              </div>              
            </div>


            <div class="row mt-4">
              <h6 class="fst-italic fw-light">Comments</h6>

              <div class="fs-6 text-dark" v-for="({comment, ip_address}, index) in comments" :key="index"> 
                <p class="mb-0">&bull; <span class="ms-1">{{ comment }}</span></p>
                <p class="fst-italic text-muted ms-2" style="font-size: 12px;"> - {{ ip_address }}</p>
              </div>

              <form action="#" @submit.prevent="createComment()">
                  <div class="alert alert-success" role="alert" v-if="success">
                    {{ success_message }}
                  </div>

                  <div class="alert alert-warning" role="alert" v-if="error">
                    {{ error_message }}
                  </div>

                  <div class="form-row">
                    <textarea class="form-control mt-2" v-model="data.comment" rows="5"></textarea>

                    <button class="btn btn-dark btn-sm mt-3 ps-2 pe-2" type="submit">Submit</button>
                  </div>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>      
  </div>

</template>
<style type="text/css">
  .intro-background {
    background: url('https://images.unsplash.com/photo-1498931299472-f7a63a5a1cfa?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=873&q=80'); 
    background-size: cover; 
    background-position: center
  }

  body {
    font-family: 'Sentient', serif;
  }
</style>

<script>
  export default {
    name: 'Home',
    data() {
      return {
        books: [],
        book: {
          url: null, 
          name: null,
          isbn: null,
          authors: null, 
          numberOfPages: 694,
          publisher: null, 
          country: null,
          mediaType: null,
          released: null,
          characters: null, 
          povCharacters: null
        },
        book_opened: false,
        loading: false,
        data: {
          comment: null,
          ip_address: null, 
          comment_type: "books",
          name: null
        },
        success: false, 
        error: false, 
        error_message: "",
        success_message: "",
        comments: []
      }
    },
    mounted() {
      this.getBooks()
      // this.getComments()
    },
    methods: {
      getBooks() {
        this.axios.get('books').then(response => {
          this.books = response.data.data
          console.log(this.books)
        }).catch(errors => {
          this.errors = errors.data
          console.log(errors)
        })
      },
      getBook(url) {
        this.loading = true

        this.axios.get(url).then(response => {
          this.book = response.data
          this.loading = false
          this.book_opened = true

          this.getComments()
        }).catch(errors => {
          this.errors = errors.data
          this.loading = false
          this.book_opened = true
        })
      }, 
      getComments() {
        this.axios.get('comments/show?name=' + this.book.name).then(response => {
          this.comments = response.data.data
          console.log(this.comments)
        }).catch(errors => {
          this.errors = errors.response.data
        })
      },
      createComment() {
        this.data.name = this.book.name

        this.axios.post('comments', this.data).then(() => {
          this.loading = false
          this.error = false
          this.success = true

          this.success_message = 'Success!'
        }).catch(errors => {
          this.loading = false
          this.success = false
          this.error = true

          this.error_message = errors.response.data
        })
      }
    }
  }
</script>
