<template>
  <section class="product-info">
    <div class="product-info__title">
      {{ product.productTitle }}
    </div>

    <p v-if="showResetFilters" @click="resetFilters" class="reset-filters">
      Reset Filters
    </p>

    <div
      class="product-info__selectable-attributes"
      v-for="(attribute, attributeIndex) in product.selectableAttributes"
      :key="attributeIndex"
    >
      <p class="attribute-name">
        {{ attribute.name }}
      </p>

      <ul class="attribute-list">
        <li
          v-for="(attributeValue, valueIndex) in attribute.values"
          :key="valueIndex"
          :class="{
            active: selectedAttributes[attribute.name] === attributeValue,
            deactive: !okProducts.filter((product) => {
              return product.attributes.filter(
                (attr) => attributeValue === attr.value
              ).length;
            }).length,
          }"
          @click="setSelectAttributes(attribute.name, attributeValue)"
        >
          {{ attributeValue }}
        </li>
      </ul>
    </div>
    <div class="product-info__calculations">
      <div class="barem">
        <p class="barem-name">Toptan Fiyat (Adet)</p>
        <ul class="barem-list">
          <li
            v-for="(range, rangeIndex) in baremList"
            :key="rangeIndex"
            :class="{
              active:
                range.minimumQuantity <= amount &&
                range.maximumQuantity >= amount,
            }"
          >
            <p v-if="rangeIndex === baremList.length - 1">
              {{ range.minimumQuantity }}+
            </p>
            <p v-else>
              {{ `${range.minimumQuantity}-${range.maximumQuantity}` }}
            </p>
            <p>{{ range.price }}₺</p>
          </li>
        </ul>
      </div>
      <div class="amount-field">
        <p class="barem-name">Adet</p>
        <input type="text" v-model="amount" />
      </div>
    </div>

    <div class="price-area">
      <p class="total-price">{{ totalPrice }}</p>
      <button
        class="button button-add-to-cart"
        :class="{ disabled: addToCartDisabled }"
      >
        Sepete Ekle
      </button>
    </div>
  </section>
</template>

<script>
export default {
  props: {
    product: {
      type: Object,
      required: true,
    },

    okProducts: {
      type: Array,
      required: true,
    },

    baremList: {
      type: Array,
      required: true,
    },

    selectedAttributes: {
      type: Object,
      required: true,
    },
  },
  data: () => ({
    amount: null,
  }),

  mounted() {
    this.amount = this.baremList[0].minimumQuantity;
  },

  watch: {
    okProducts(newValue) {
      console.log(newValue);
    },
  },
  computed: {
    totalPrice() {
      if (this.amount) {
        return `Toplam ${(
          this.baremList.find(
            (range) =>
              range.minimumQuantity <= this.amount &&
              range.maximumQuantity >= this.amount
          ).price * this.amount
        ).toFixed(2)}₺`;
      } else {
        return `Bu alan en az ${this.baremList[0].minimumQuantity} olmalıdır`;
      }
    },
    addToCartDisabled() {
      return !(
        Object.keys(this.selectedAttributes).length ===
          this.product.selectableAttributes.length && this.amount
      );
    },
    showResetFilters() {
      return Object.keys(this.selectedAttributes).length > 0;
    },
  },

  methods: {
    setSelectAttributes(attributeName, attributeValue) {
      this.$emit("setSelectAttributes", attributeName, attributeValue);
    },
    resetFilters() {
      this.$emit("resetFilters");
    },
  },
};
</script>

<style lang="scss">
.product-info {
  width: 100%;
  height: 100%;
  &__title {
    margin: 20px 40px;
    font-size: 22px;
  }
  &__selectable-attributes {
    padding: 0 20px;
    margin-top: 15px;
    display: flex;
    .attribute-name {
      text-align: left !important;
      margin-bottom: 0;
      width: 70px;
      font-size: 12px;
      font-weight: 300;
      height: 36px;
      padding-top: 11px;
      color: #212121;
      text-align: right;
      font-weight: bold;
    }

    .attribute-list {
      display: flex;
      gap: 10px;
      margin-left: 15px;
      li {
        width: 120px;
        margin-bottom: 0;
        font-size: 13px;
        font-weight: 400;
        text-align: center;
        line-height: 31px;
        cursor: pointer;
        border: 2px solid #e0e0e0;
        border-radius: 2px;
        white-space: nowrap;
        overflow: hidden;
        -o-text-overflow: ellipsis;
        text-overflow: ellipsis;
        padding: 1px 5px 0;
        &.active {
          border-width: 2px;
          line-height: 31px;
          border-color: #147ec4;
          background-color: #edf4fb;
          font-weight: bold;
          padding-top: 1px;
        }
        &.deactive {
          border: 2px dashed #e0e0e0;
          line-height: 31px;
          background-color: #eee;
          font-weight: bold;
          padding-top: 1px;
          pointer-events: none;
        }
      }
    }
  }

  &__calculations {
    background: #f5f5f5;
    margin-top: 10px;
    font-size: 15px;
    font-weight: 300;
    padding-top: 11px;
    color: #212121;

    font-weight: bold;
    .barem {
      padding: 0 20px;
      display: flex;

      .barem-name {
        text-align: left !important;
        margin-bottom: 0;
        width: 70px;
        font-size: 12px;
        font-weight: 300;
        height: 36px;
        padding-top: 11px;
        color: #212121;
        text-align: right;
        font-weight: bold;
        margin-right: 15px;
      }
      &-list {
        display: flex;
        li {
          padding: 0 5px;
          border-left: 1px solid #eee;
          display: flex;
          flex-direction: column;
          justify-content: center;
          align-items: flex-start;
          width: 94px;
          border-left: 1px solid #e0e0e0;
          letter-spacing: -0.4px;
          min-height: 45px;
          position: relative;
          &.active {
            background: #ffedb9;
            font-weight: bold;
          }
        }
      }
    }
    .amount-field {
      display: flex;
      margin-top: 10px;
      align-items: center;
      padding: 10px 20px;
      p {
        width: 70px;
      }
      input {
        margin-left: 15px;
        width: 100px;
        height: 36px;
        border: 1px solid #e0e0e0;
        border-radius: 2px;
        font-size: 15px;
        font-weight: 300;
        color: #212121;
        text-align: center;
        line-height: 31px;
      }
    }
  }
  .price-area {
    display: flex;
    flex-direction: column;

    padding: 10px 20px;
    .total-price {
      font-weight: bold;
      font-size: 25px;
    }
    .button-add-to-cart {
      margin-top: 10px;
      width: 30%;
      height: 40px;
      border: none;
      border-radius: 2px;
      background-color: #147ec4;
      color: #fff;
      font-size: 15px;
      font-weight: bold;
      cursor: pointer;
      &.disabled {
        background-color: #e0e0e0;
        color: #212121;
        cursor: not-allowed;
      }
    }
  }

  .reset-filters {
    color: #147ec4;
    text-align: right;
    margin-right: 15px;
    cursor: pointer;
  }
}
</style>
