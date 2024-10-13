<template>
    <v-container>

        <!-- show logo image  -->
         <!-- <v-img src="../assets/logo.png" max-height="100" contain></v-img> -->



        <v-row>
            <v-col cols="12" md="5" lg="4">
                <v-text-field prepend-inner-icon="mdi-magnify" v-model="search" label="Search" outlined dense
                    hide-details single-line clearable @click:clear="clearSearchText"
                    @input="filterBooksByPhrase"></v-text-field>
            </v-col>

            <v-col cols="12" md="5" lg="4">
                <v-combobox v-model="categories" :items="allCategories" label="Categories" outlined chips dense
                    hide-details clearable multiple single-line></v-combobox>
            </v-col>

            <v-col cols="12" md="2" lg="4" class="d-flex justify-end align-self-center">
                <span data-tooltip="Karty pionowe" class="icon grid-vertical" :class="{ actived: isCol }"
                    @click="isCol = true"></span>
                <span data-tooltip="Karty poziome" class="icon grid-horizontal" :class="{ actived: !isCol }"
                    @click="isCol = false"></span>
            </v-col>
        </v-row>

        <div class="books-list mt-4">

            <v-row v-if="filteredBooks.length">
                <v-col cols="12" :md="isCol ? 6 : 12" :lg="isCol ? 4 : 12" v-for="book in filteredBooks" :key="book.id"
                    class="d-flex flex-column">
                    <BookItem :book="book" :is-col="isCol" />
                </v-col>
            </v-row>

            <div v-else>
                <v-empty-state icon="mdi-magnify" text="No books available."
                    title="We couldn't find a match."></v-empty-state>
            </div>
        </div>
    </v-container>
</template>

<script>
import json from '@/data/json/books.json';
import BookItem from './BookItem.vue';

export default {
    name: 'BooksList',
    components: {
        BookItem
    },
    data() {
        return {
            allBooks: [],
            filteredBooks: [],
            search: '',
            drawer: false,
            isCol: true,
            categories: [],
        };
    },
    created() {
        this.fetchBooks();
    },
    methods: {

        fetchBooks() {
            this.allBooks = json.ebooks;
            this.filteredBooks = this.allBooks;
        },

        filterBooksByCategories() {
            if (this.categories.length === 0) {
                this.fetchBooks();
                return;
            }

            this.filteredBooks = this.allBooks.filter((book) => {
                return this.categories.some((category) => book.categories.includes(category));
            }
            );
        },

        filterBooksByPhrase() {
            if (this.search === '') {
                this.filterBooksByCategories();
                return;
            }

            if (this.categories.length > 0) {
                this.filteredBooks = this.allBooks.filter((book) => {
                    return this.categories.some((category) => book.categories.includes(category)) &&
                        (book.title.toLowerCase().includes(this.search.toLowerCase()) ||
                            book.author.toLowerCase().includes(this.search.toLowerCase()) ||
                            book.description.toLowerCase().includes(this.search.toLowerCase()));
                });
                return;
            }
            else {
                this.filteredBooks = this.allBooks.filter((book) => {
                    return book.title.toLowerCase().includes(this.search.toLowerCase()) ||
                        book.author.toLowerCase().includes(this.search.toLowerCase()) ||
                        book.description.toLowerCase().includes(this.search.toLowerCase());
                });
            }
        },

        clearSearchText() {
            this.search = '';
            this.filterBooksByCategories();
        },

        /*
        async fetchBooks() {
            try {
                const response = await axios.get('/src/json/books.json');
                this.books = response.data;
            } catch (error) {
                console.error('Error fetching books:', error);
            }
        }
        */
    },
    computed: {
        allCategories() {
            return this.allBooks.reduce((acc, book) => {
                book.categories.forEach(category => {
                    if (!acc.includes(category)) {
                        acc.push(category);
                    }
                });
                return acc;
            }, []);
        }
    },
    watch: {
        categories() {
            this.search = '';
            this.filterBooksByCategories();
        }
    }
};
</script>

<style scoped>
.icon {
    display: block;
    width: 24px;
    height: 24px;
    background-color: #fff;
    margin-right: 2px;
    cursor: pointer;
}

.icon.actived {
    background-color: rgb(255, 16, 147);
}

.icon.grid-vertical {
    -webkit-mask-image: url(data:image/svg+xml;base64,PHN2ZyB2aWV3Qm94PSIwIDAgMjQgMjUiIGZpbGw9Im5vbmUiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyI+PHBhdGggZD0iTTEwIDMuNzVIM3Y3aDd2LTd6bTExIDBoLTd2N2g3di03em0wIDExaC03djdoN3YtN3ptLTExIDBIM3Y3aDd2LTd6IiBzdHJva2U9IiMxRTFFMUUiIHN0cm9rZS1saW5lY2FwPSJyb3VuZCIgc3Ryb2tlLWxpbmVqb2luPSJyb3VuZCIvPjwvc3ZnPg==);
    mask-image: url(data:image/svg+xml;base64,PHN2ZyB2aWV3Qm94PSIwIDAgMjQgMjUiIGZpbGw9Im5vbmUiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyI+PHBhdGggZD0iTTEwIDMuNzVIM3Y3aDd2LTd6bTExIDBoLTd2N2g3di03em0wIDExaC03djdoN3YtN3ptLTExIDBIM3Y3aDd2LTd6IiBzdHJva2U9IiMxRTFFMUUiIHN0cm9rZS1saW5lY2FwPSJyb3VuZCIgc3Ryb2tlLWxpbmVqb2luPSJyb3VuZCIvPjwvc3ZnPg==);
}

.icon.grid-horizontal {
    -webkit-mask-image: url(data:image/svg+xml;base64,PHN2ZyB2aWV3Qm94PSIwIDAgMjQgMjUiIGZpbGw9Im5vbmUiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyI+PHBhdGggZD0iTTIxIDMuOTA1SDN2N2gxOHYtN3ptMCAxMUgzdjdoMTh2LTd6IiBzdHJva2U9IiM4MThDQTkiIHN0cm9rZS1saW5lY2FwPSJyb3VuZCIgc3Ryb2tlLWxpbmVqb2luPSJyb3VuZCIvPjwvc3ZnPg==);
    mask-image: url(data:image/svg+xml;base64,PHN2ZyB2aWV3Qm94PSIwIDAgMjQgMjUiIGZpbGw9Im5vbmUiIHhtbG5zPSJodHRwOi8vd3d3LnczLm9yZy8yMDAwL3N2ZyI+PHBhdGggZD0iTTIxIDMuOTA1SDN2N2gxOHYtN3ptMCAxMUgzdjdoMTh2LTd6IiBzdHJva2U9IiM4MThDQTkiIHN0cm9rZS1saW5lY2FwPSJyb3VuZCIgc3Ryb2tlLWxpbmVqb2luPSJyb3VuZCIvPjwvc3ZnPg==);
}
</style>