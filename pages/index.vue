<template>
  <div class="container">
    <nav>Welcome</nav>
    <el-row
      :gutter="8"
      type="flex"
      align="center"
      justify="center"
      v-loading="loading"
      element-loading-text="Searching..."
    >
      <el-col>
        <el-input
          placeholder="Please input product keyword"
          v-model="keyword"
        ></el-input>
      </el-col>
      <el-col>
        <el-button @click="search" type="primary"> Search </el-button>
      </el-col>
    </el-row>
    <el-row type="" :gutter="8">
      <el-col :span="8" v-for="(list, index) in lists" :key="index">
        <el-card style="margin-top: 0.2rem">
          <img
            :src="getImageThumbnail(list.images)"
            class="image"
            @click="productChanged(list)"
          />
          <div style="padding: 14px">
            <span>{{ list.title }}</span>
          </div>
        </el-card>
      </el-col>
    </el-row>
    <el-empty
      :description="'No product found for keyword: ' + keyword"
      v-if="notFound"
    ></el-empty>
    <el-dialog :visible.sync="dialogVisible">
      <img width="100%" :src="currentImage" alt="" />
    </el-dialog>
  </div>
</template>

<script>
export default {
  name: "IndexPage",
  data() {
    return {
      loading: false,
      keyword: "",
      lists: [],
      currentImage: "",
      dialogVisible: false,
      notFound: false,
    };
  },
  methods: {
    productChanged(product) {
      console.log(product);
      this.currentImage = product.images[0]?.thumbnail;
      this.dialogVisible = true;
    },
    search() {
      const that = this;
      this.loading = true;
      that.notFound = false;
      this.$axios
        .get("/user/products", {
          params: {
            keyword: this.keyword,
          },
        })
        .then((response) => {
          that.lists = response.data.data.products;
          if (!that.lists.length) {
            that.notFound = true;
          }
        })
        .finally(() => {
          that.loading = false;
        });
    },
    getImageThumbnail(images = [{ thumbnail: "" }]) {
      return images[0]?.thumbnail;
    },
  },
};
</script>

<style>
:root {
  --pink: #7633f9;
}
.container {
  padding: 0.8rem;
}
nav {
  background-color: var(--pink);
  box-shadow: 0 0.5rem 1rem rgba(0, 0, 0, 0.15),
    inset 0 -1px 0 rgba(0, 0, 0, 0.15);
  padding: 0.75rem 0.2rem;
  color: #fff;
  margin-bottom: 1rem;
  font-size: 1.5rem;
}
a,
a:focus,
a:hover {
  color: #fff;
}

.image {
  width: 18rem;
  height: 24rem;
  display: block;
}
.el-button--primary {
  background-color: var(--pink);
}
</style>