<template>
    <div>
        <h2 class="mt-3">Articles</h2>
        <form action="">
            <div class="form-group">
                <input type="text" class="form-control" placeholder="Title"
                v-model="article.title">
            </div>
        </form>
        <form @submit.prevent="addArticle()">
            <div class="form-group">
                <textarea class="form-control" 
                v-model="article.body" placeholder="Body"></textarea>                
            </div>
            <button class="btn btn-primary btn-block">Add</button>
        </form>
        <br>
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
            <hr>
            <button @click="editArticle(article)" 
                class="btn btn-warning mb-2">Edit</button>
            <button @click="deleteArticle(article.id)" 
                class="btn btn-danger">Delete</button>
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
                //if page_url is not empty get the page_url
                page_url = page_url || 'api/articles'
                fetch(page_url)
                .then(response => response.json())
                .then(response => {
                    this.articles = response.data;
                    this.makePagination(response.meta, response.links);
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
            },

            deleteArticle(id) {
                if(confirm('Are you sure?')) {
                    fetch(`api/article/${id}`, {
                        method: 'delete'
                    })
                    .then(response => response.json())
                    .then(data => {
                        alert('Article removed');
                        this.fetchArticles();
                    })
                    .catch(error => console.log(error));
                }
            },

            addArticle() {
                if(this.edit === false) {
                    fetch('api/article', {
                        method: 'post',
                        body: JSON.stringify(this.article),
                        headers: {
                            'content-type' : 'application/json'
                        }
                    })
                    .then(response => response.json())
                    .then(data => {
                        this.article.title = '';
                        this.article.body  = '';
                        alert('Article Updated');
                        this.fetchArticles();
                    })
                } else {
                    fetch('api/article', {
                        method: 'put',
                        body: JSON.stringify(this.article),
                        headers: {
                            'content-type' : 'application/json'
                        }
                    })
                    .then(response => response.json())
                    .then(data => {
                        this.article.title = '';
                        this.article.body  = '';
                        alert('Article Added');
                        this.fetchArticles();
                    })
                }
            },

            editArticle(article) {
                this.edit = true;
                this.article.id = article.id;
                this.article.article_id = article.id;
                this.article.title = article.title;
                this.article.body = article.body;
            }
        }
    } 
</script>