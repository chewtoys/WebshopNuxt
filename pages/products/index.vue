<template>
  <div class="body">
    <Topbar />
    <section class="content">
      <ul class="productList">
        <li
          v-for="product in productList"
          :key="product.id"
        >
          <ProductGridView :product="product" />
        </li>
      </ul>
    </section>
  </div>
</template>

<script lang="ts">
import { createComponent } from '@vue/composition-api';
import ProductGridView from '../../components/ProductGridView.vue';
import ProductService from '../../services/product.service';
import Topbar from '../../components/Topbar.vue';
import Product from '../../models/Product';

export default createComponent({
  components: {
    ProductGridView,
    Topbar,
  },
  // Watch for $route.query.page to call Component methods
  watchQuery: ['category', 'search'],
  asyncData({ query }) {
    return ProductService.getProducts((query.category as string | null))
      .then((res) => {
        let productList = res.data;
        if (query.search) {
          const filteredList : Product[] = [];
          productList.forEach((product : Product) => {
            const queryString = (query.search as string).toLowerCase();
            if (product.productName.toLowerCase().includes(queryString)) {
              filteredList.push(product);
            }
            productList = filteredList;
          });
        }
        return {
          productList,
        };
      })
      .catch(() => ({
        productList: [],
      }));
  },
});
</script>

<style lang="scss" scoped>
.body {
  margin-top: 1rem;
}
</style>
