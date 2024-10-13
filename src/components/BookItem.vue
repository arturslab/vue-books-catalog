<template>
    <v-hover v-slot="{ isHovering, props }">
        <v-card :height="isCol ? '100%' : 'auto'" @click="swapCard" :elevation="isHovering ? 12 : 2" v-bind="props">

            <v-row no-gutters v-if="!showDescription" class="fill-height">

                <v-col cols="12" md="6" :lg="isCol ? 4 : 2">
                    <v-img :src="bookImage(book)" aspect-ratio="8/12" :max-height="imageHeight"
                        class="ma-2 text-left"></v-img>

                    <div class="text-center">
                        <v-rating :model-value="calculateRating(book.rating)" :length="5" :size="25"
                            active-color="orange-lighten-1" half-increments color="grey-darken-2"></v-rating>
                    </div>
                </v-col>

                <v-col cols="12" md="6" :lg="isCol ? 8 : 10" class="d-flex flex-column">
                    <div class="fill-height">
                        <v-card-title class="text-h6">
                            {{ book.title }}
                        </v-card-title>

                        <v-card-subtitle>{{ book.author }}</v-card-subtitle>

                        <v-card-text class="pa-2">
                            <v-chip-group>
                                <v-chip v-for="category in book.categories" :key="category" class="ma-1" color="primary"
                                    density="comfortable" size="small">
                                    {{ category }}
                                </v-chip>
                            </v-chip-group>
                            <v-spacer></v-spacer>
                            <div class="ma-1 mt-4">
                                <v-tooltip text="Publish Date (First Publish Date)">
                                    <template v-slot:activator="{ props }">
                                        <span class="mr-4" v-bind="props"><v-icon icon="mdi-calendar"></v-icon> {{
                                            book.published_year }} ({{ book.first_published_year }})</span>
                                    </template>
                                </v-tooltip>

                                <v-tooltip text="Pages">
                                    <template v-slot:activator="{ props }">
                                        <span class="mr-4" v-bind="props"><v-icon
                                                icon="mdi-book-open-page-variant-outline"></v-icon> {{
                                                    book.pages }}</span>
                                    </template>
                                </v-tooltip>

                            </div>

                            <p v-if="!isCol" class="mt-4">{{ book.description }}</p>
                        </v-card-text>

                        <div class="align-self-center">
                            <v-card-actions>
                                <v-btn text :class="{ 'show-btns': isHovering }">{{ showDescription ? 'Hide' : 'Show' }}
                                    description</v-btn>
                            </v-card-actions>
                        </div>

                    </div>
                </v-col>

            </v-row>

            <v-row v-else no-gutters class="fill-height">
                <v-col cols="12" class="d-flex flex-column fill-height align-content-space-between">
                    <v-card-text v-if="showDescription">
                        {{ book.description }}
                    </v-card-text>
                    <v-card-actions>
                        <v-btn text :class="{ 'show-btns': isHovering }">{{ showDescription ? 'Hide' : 'Show' }}
                            description</v-btn>
                    </v-card-actions>
                </v-col>
            </v-row>

        </v-card>
    </v-hover>

</template>

<script>
export default {
    props: {
        book: {
            type: Object,
            required: true
        },
        isCol: {
            type: Boolean,
            default: true
        }
    },
    data() {
        return {
            showDescription: false,
        };
    },
    computed: {
        imageHeight() {
            return this.isCol ? 200 : 200;
        }
    },
    methods: {
        bookImage(book) {
            return new URL('/images/' + book.image, import.meta.url).href;
        },
        swapCard() {
            if (this.isCol)
                this.showDescription = !this.showDescription;
        },
        calculateRating(rating) {
            return rating / 2;
        },
    }
};
</script>

<style scoped>
.show-btns {
    color: rgb(255, 16, 147) !important;
}
</style>