<template>
  <section class="images">
    <div class="images__selected-image">
      <img :src="images[selectedImageIndex]" />
    </div>
    <ul class="images__image-list">
      <li
        @click="setSelectedImageIndex(index)"
        v-for="(image, index) in images"
        :key="index"
        :class="{ active: selectedImageIndex === index }"
      >
        <img :src="image" alt="" />
      </li>
    </ul>
  </section>
</template>

<script>
export default {
  props: {
    images: {
      type: Array,
      required: true,
    },
    selectedImageIndex: {
      type: Number,
      required: true,
    },
  },
  emits: ["setSelectedImageIndex"],
  methods: {
    setSelectedImageIndex(index) {
      console.log("ye");
      this.$emit("setSelectedImageIndex", index);
    },
  },
};
</script>

<style lang="scss">
.images {
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  padding: 20px;
  &__selected-image {
    flex: 1;
    margin-bottom: 30px;
    img {
      width: 100%;
      height: 100%;
    }
  }
  &__image-list {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    list-style: none;
    li {
      width: 60px;
      height: 60px;
      border: 1px solid #eee;
      cursor: pointer;

      &.active {
        border: solid 2px #616161;
      }

      img {
        width: 100%;
        height: 100%;
      }
    }
  }
}
</style>
