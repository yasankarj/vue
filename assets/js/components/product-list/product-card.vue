<template>
    <div class="col-xs-12 col-3 mb-2 pb-2">
        <div :class="$style['product-box']">
            <div :class="$style.image">
                <img
                    :alt="product.name"
                    :src="product.image"
                    class="d-block mb-2"
                >
                <h3 class="font-weight-bold mb-2 px-2">
                    {{ product.name }}
                </h3>
            </div>
            <div class="p-2 my-3 d-md-flex justify-content-between">
                <p class="p-0 d-inline">
                    <strong>${{ price }}</strong>
                </p>
                <button
                    class="btn btn-info btn-sm"
                >
                    View Product
                </button>
            </div>
        </div>
        <hr>
        <div class="px-2 pb-2">
            <small>brought to you by {{ product.brand }}</small>
        </div>
    </div>
</template>

<script>
export default {
    name: 'ProductCard',
    props: {
        product: {
            type: Object,
            required: true,
        },
    },
    computed: {
        /**
       * Returns a formatted price for the product
       * @returns {string}
       */
        price() {
            return (this.product.price / 100)
                .toLocaleString('en-US', { minimumFractionDigits: 2 });
        },
    },
};
</script>

<style lang="scss" module>
@import '~styles/components/light-component';
.product-box {
  border: 1px solid $light-component-border;
  box-shadow: 0 0 7px 4px #efefee;
  border-radius: 5px;
}
.image {
  img {
    width: 100%;
    height: auto;
    min-height: 312px;
    border-top-left-radius: 5px;
    border-top-right-radius: 5px;
  }
  h3 {
    font-size: 1.2rem;
  }
}
</style>
