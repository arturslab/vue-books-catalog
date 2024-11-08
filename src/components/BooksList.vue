<template>
    <v-container>
        <v-row>
            <v-col cols="12" md="5" lg="4">
                <v-text-field v-model="search" label="Search" outlined dense
                    hide-details single-line clearable @click:clear="clearSearchText"
                    @input="filterBooksByPhrase">
                    <template v-slot:prepend-inner>
                        <v-icon>{{ icon.magnify }}</v-icon>
                    </template>
                </v-text-field>
            </v-col>

            <v-col cols="12" md="5" lg="4">
                <v-combobox v-model="categories" :items="allCategories" label="Categories" outlined chips dense
                    hide-details clearable multiple single-line></v-combobox>
            </v-col>

            <v-col cols="12" md="2" lg="4" class="d-flex justify-end align-self-center">

                <v-btn-group variant="outlined" divided>
                    <v-tooltip text="Switch to Vertical Cards">
                        <template v-slot:activator="{ props }">
                            <v-btn v-bind="props" @click="isCol = true" class="p-0" :class="{ actived: isCol }"
                                :color="isCol ? highlightColor : inactiveColor">
                                <v-icon size="x-large">{{ icon.viewGrid }}</v-icon>
                            </v-btn>
                        </template>
                    </v-tooltip>

                    <v-tooltip text="Switch to Horizontal Cards">
                        <template v-slot:activator="{ props }">
                            <v-btn v-bind="props" @click="isCol = false"
                                :color="!isCol ? highlightColor : inactiveColor">
                                <v-icon size="x-large">{{ icon.viewSequential }}</v-icon>
                            </v-btn>
                        </template>
                    </v-tooltip>

                </v-btn-group>

            </v-col>
        </v-row>

        <div class="books-list mt-4">

            <v-row v-if="filteredBooks.length">
                <v-col cols="12" :md="isCol ? 6 : 12" :lg="isCol ? 4 : 12" v-for="book in filteredBooks" :key="book.id"
                    class="d-flex flex-column">
                    <BookItem :book="book" :is-col="isCol" :star-rating-size="starRatingSize" />
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
import { mdiMagnify, mdiViewGrid, mdiViewSequential } from '@mdi/js'
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
            highlightColor: '#ff1093',
            inactiveColor: 'grey-lighten-1',
            icon: {
                magnify: mdiMagnify,
                viewGrid: mdiViewGrid,
                viewSequential: mdiViewSequential
            }
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
        },
        starRatingSize() {
            if(this.$vuetify.display.sm) return "medium";
            if(this.$vuetify.display.md) return "medium";
            if(this.$vuetify.display.xlAndUp) return "medium";
            return "x-small";
        },
    },
    watch: {
        categories() {
            this.search = '';
            this.filterBooksByCategories();
        }
    }
};
</script>
