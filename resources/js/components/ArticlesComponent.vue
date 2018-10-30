<template>
    <div>
        <h2 class="mt-3">Articles</h2>
        <nav aria-label="Page navigation example">
            <ul class="pagination">
                <li v-bind:class="[{disabled: !pagination.prev_page_url}]" 
                    class="page-item"><a class="page-link"
                    @click="fetchArticles(pagination.prev_page_url)">Previous</a>
                </li>
                <li class="page-item disabled">
                    <a class="page-link">Page {{ pagination.current_page }} of {{ pagination.last_page }}</a>
                </li>
                <li v-bind:class="[{disabled: !pagination.next_page_url}]" 
                    class="page-item"><a class="page-link"
                    @click="fetchArticles(pagination.next_page_url)">Next</a>
                </li>
            </ul>
        </nav>
        <div class="card card-body mb-2" 
            v-for="article in articles"
            v-bind:key="article.id">
            <h3>{{ article.title }}</h3>
            <p>{{ article.body }}</p>
        </div>
    </div>
</template>

<script>
   export default {
       data() {
           return {
               articles: [],
               article: {
                   id: '',
                   title: '',
                   body: ''
               }, 
               article_id: '',
               pagination: '',
               edit: false
           }
       },

       created() {
           this.fetchArticles();
       },

       methods: {
           fetchArticles(page_url) {
               let vm = this;
               page_url = page_url || 'api/articles'
               fetch(page_url)
               .then(response => response.json())
               .then(response => {
                   this.articles = response.data;
                   vm.makePagination(response.meta, response.links);
               })
               .catch(error => console.log(error));
           },

           makePagination(meta, links) {
               let pagination = {
                   current_page : meta.current_page,
                   last_page    : meta.last_page,
                   next_page_url: links.next,
                   prev_page_url: links.prev
               }

               this.pagination = pagination;
           }
       }
   } 
</script>