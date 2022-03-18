<script>
import ImageArea from "./components/ImageArea";
import OptionsArea from "./components/OptionsArea";
import product from "./product";
export default {
  components: {
    ImageArea,
    OptionsArea,
  },
  data: () => ({
    product,
    selectedImageIndex: 0,
    selectedAttributes: {},
  }),

  computed: {
    okProducts() {
      return this.product.productVariants.filter((variant) => {
        return (
          variant.attributes.filter((attribute) => {
            if (
              attribute.name in this.selectedAttributes &&
              this.selectedAttributes[attribute.name] === attribute.value
            ) {
              return variant;
            } else {
              return false;
            }
          }).length === Object.keys(this.selectedAttributes).length
        );
      });
    },

    filteredImages() {
      if (Object.keys(this.selectedAttributes).length) {
        const images = [];

        this.okProducts.forEach((product) =>
          product.images.forEach((image) => images.push(image))
        );

        return images;
      } else {
        const images = [];
        this.product.productVariants.forEach((variant) =>
          variant.images.forEach((image) => images.push(image))
        );

        return images;
      }
    },
  },

  methods: {
    setSelectedImageIndex(index) {
      this.selectedImageIndex = index;
    },
    setSelectAttributes(attributeName, attributeValue) {
      this.$set(this.selectedAttributes, attributeName, attributeValue);
    },
    resetFilters() {
      this.selectedAttributes = {};
    },
  },
};
</script>

<template>
  <section class="detail-page">
    <div class="image-area">
      <ImageArea
        :images="filteredImages"
        :selectedImageIndex="selectedImageIndex"
        @setSelectedImageIndex="setSelectedImageIndex"
      />
    </div>
    <div class="options-area">
      <OptionsArea
        :product="product"
        :okProducts="okProducts"
        :selectedAttributes="selectedAttributes"
        :baremList="product.baremList"
        @setSelectAttributes="setSelectAttributes"
        @resetFilters="resetFilters"
      />
    </div>
  </section>
</template>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Roboto:wght@400;700&display=swap");
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Roboto", sans-serif;
  ul {
    list-style: none;
  }
}

body {
  background: #f5f5f5;
}

.detail-page {
  width: 60%;
  min-height: 450px;
  margin: 100px auto 0 auto;
  display: flex;
  justify-content: space-between;
  box-shadow: 0 1px 2px 0 rgb(0 0 0 / 23%);
  background: white;

  .image-area {
    flex: 2;
  }
  .options-area {
    flex: 3;
  }
}
</style>
