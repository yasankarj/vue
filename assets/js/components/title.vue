<template>
    <div :class="$style.component">
        <h1>
            {{ categoryName }}
        </h1>
    </div>
</template>

<script>
export default {
    name: 'Title',
    props: {
        currentCategoryId: {
            type: [String, null],
            default: null,
        },
        categories: {
            type: Array,
            required: true,
        },
    },
    computed: {
        categoryName() {
            if (this.currentCategoryId === null) {
                return 'All Products';
            }
            console.log(this.categories, this.currentCategoryId);
            const category = this.categories.find((cat) => (cat.id == this.currentCategoryId));
            return category ? category.name : 'N/A';
        },
    },
};
</script>

<style lang="scss" module>
.component{
    h1: {
        font-size: 1.7rem;
    }
}
</style>
