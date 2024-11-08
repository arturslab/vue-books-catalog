<template>
    <v-hover v-slot="{ isHovering, props }">
        <v-card :height="isCol ? '100%' : 'auto'" @click="swapCard" :elevation="isHovering ? 12 : 2" v-bind="props">

            <v-row no-gutters v-if="!showDescription" class="fill-height text-grey-lighten-1">
                <v-col v-if="isCol" cols="3" sm="4" lg="3">
                    <v-img :src="bookImage(book)" aspect-ratio="8/12" :max-height="imageHeight"
                        class="ma-2 text-left"></v-img>

                    <div class="text-center">
                        <v-rating :model-value="calculateRating(book.rating)" :length="5" :size="starRatingSize"
                            density="compact" active-color="orange-lighten-1" half-increments
                            color="grey-darken-2"></v-rating>
                    </div>
                </v-col>

                <v-col :cols="isCol ? 9 : 12" sm="8" :lg="isCol ? 8 : 9" class="d-flex flex-column">
                    <div class="fill-height">
                        <v-card-title class="card--title text-grey-lighten-2">
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
                                        <span class="mr-4" v-bind="props">
                                            <v-icon>{{ icon.calendar }}</v-icon>
                                            {{ book.published_year }} ({{ book.first_published_year }})
                                        </span>
                                    </template>
                                </v-tooltip>

                                <v-tooltip text="Pages">
                                    <template v-slot:activator="{ props }">
                                        <span class="mr-4" v-bind="props">
                                            <v-icon color="grey">{{ icon.openPage }}</v-icon>
                                            {{ book.pages }}
                                        </span>
                                    </template>
                                </v-tooltip>
                            </div>

                            <div class="ma-1 mt-4" v-if="book.store">
                                <v-tooltip text="Store">
                                    <template v-slot:activator="{ props }">
                                        <span class="mr-4" v-bind="props">
                                            <v-icon>{{ icon.store }}</v-icon>
                                            {{ book.store }}
                                        </span>
                                    </template>
                                </v-tooltip>
                            </div>

                        </v-card-text>

                        <div class="align-self-center">
                            <v-card-actions>
                                <v-btn text :class="{ 'show-btns': isHovering }" variant="tonal">
                                    {{ showDescription ? 'Hide' : 'Show' }} description
                                </v-btn>
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
                        <v-btn text :class="{ 'show-btns': isHovering }">
                            {{ showDescription ? 'Hide' : 'Show' }} description
                        </v-btn>
                    </v-card-actions>
                </v-col>
            </v-row>

        </v-card>
    </v-hover>

</template>

<script>
import { mdiAccount, mdiCalendar, mdiBookOpenPageVariantOutline, mdiStore } from '@mdi/js'

export default {
    props: {
        book: {
            type: Object,
            required: true
        },
        isCol: {
            type: Boolean,
            default: true
        },
        starRatingSize: {
            type: String,
            default: "x-smal"
        },
    },
    data() {
        return {
            showDescription: false,
            icon: {
                account: mdiAccount,
                calendar: mdiCalendar,
                openPage: mdiBookOpenPageVariantOutline,
                store: mdiStore,
            }
        };
    },
    computed: {
        imageHeight() {
            return this.isCol ? 200 : 200;
        },
    },
    methods: {
        bookImage(book) {
            return new URL('/images/' + book.image, import.meta.url).href;
        },
        swapCard() {
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

.card--title {
    font-size: 0.9rem;
    font-weight: 500;
}

@media (min-width: 600px) {
    .card--title {
        font-size: 1.2rem;
    }
}
</style>