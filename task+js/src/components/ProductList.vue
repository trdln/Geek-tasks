<template>
  <div class="products-list">
    <div class="product-form">
      <h2>New product form</h2>
      <input
        type="text"
        v-model="itemTitle"
        class="form-input"
        placeholder="New title"
      />
      <input
        type="text"
        v-model="itemAvatar"
        class="form-input"
        placeholder="New avatar"
      />
      <textarea
        type="text"
        v-model="itemDescription"
        class="form-textarea"
        placeholder="New description"
      />
      <button @click="generateNewItem">Generate</button>
    </div>

    <div class="products-list-wrap" v-if="isNotEmpty">
      <div class="product-list" v-for="product in products" :key="product.id">
        <product-item
          :product="product"
          @edited="editProduct"
          @deleted="removeProduct"
        ></product-item>
      </div>
    </div>
    <div class="info" v-else>No products</div>
  </div>
</template>

<!-- ? Composition <=> Service -->
<script>
import { computed, ref } from "vue";
import ProductItem from "./ProductItem";
import apiProduct from "../services/products.api";

export default {
  name: "ProductList",
  component: {
    ProductItem,
  },
  setup() {
    let products = ref({ name: "Test" });

    const reLoad = () => {
      apiProduct.fetchProducts().then((data) => {
        products.value = data;
      });
    };

    reLoad();

    const editProduct = (id, editedObject) => {
      apiProduct.editProduct(id, editedObject).then(() => reLoad());
    };

    const removeProduct = (id) => {
      apiProduct.removeProduct(id).then(() => reLoad());
    };

    console.log(products.value);
    let itemTitle = ref("New Generated Product");
    let itemAvatar = ref("http://loremflickr.com/640/480/technics");
    let itemDescription = ref("The description of the new generated product");

    const generateNewItem = () => {
      apiProduct
        .addProduct({
          createdAt: new Date(),
          name: itemTitle.value,
          avatar: itemAvatar.value,
          description: itemDescription.value,
          id: 0,
        })
        .then(() => reLoad());
    };
    return {
      products,
      itemTitle,
      itemAvatar,
      itemDescription,
      isNotEmpty: computed(() => !!products.value),
      generateNewItem,
      editProduct,
      removeProduct,
      reLoad,
    };
  },
};
</script>

<!-- ? Composition + Store -->
<!-- <script>
import { useStore } from "vuex";
import { computed, ref } from "vue";
import ProductItem from "./ProductItem";
export default {
  components: {
    ProductItem,
  },
  setup() {
    const store = useStore();

    let itemTitle = ref("New Generated Product");
    let itemAvatar = ref("http://loremflickr.com/640/480/technics");
    let itemDescription = ref("The description of the new generated product");

    store.dispatch("fetchProducts");

    function generateNewItem() {
      store.dispatch("addProduct", {
        createdAt: new Date(),
        name: itemTitle.value,
        avatar: itemAvatar.value,
        description: itemDescription.value,
        id: 0,
      });
    }

    return {
      products: computed(() => store.getters.products),
      itemTitle,
      itemAvatar,
      itemDescription,
      generateNewItem,
      isNotEmpty: computed(() => Object.values(store.getters.products).length),
    };
  },
};
</script> -->

<style scoped>
.product-form {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.form-input {
  margin-bottom: 20px;
  width: 20%;
}

.form-textarea {
  margin-bottom: 20px;
  width: 20%;
  height: 100px;
}
</style>
