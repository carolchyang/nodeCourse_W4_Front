<template>
  <div class="row g-3 mb-4">
    <div class="col-12 col-md-4">
      <select class="form-select form-select-sm" v-model="filter.sort">
        <option value="desc">由新到舊</option>
        <option value="asc">由舊到新</option>
      </select>
    </div>
    <div class="col-12 col-md-8">
      <div class="input-group input-group-sm">
        <input
          type="text"
          class="form-control"
          placeholder="搜尋貼文"
          aria-label="search"
          aria-describedby="search"
          v-model="filter.q"
        />
        <button
          class="effectBtn btn btn-primary py-0 px-3"
          type="button"
          id="search"
          @click.prevent="filterPost"
        >
          <i class="bi bi-search h3"></i>
        </button>
      </div>
    </div>
  </div>

  <ul>
    <li class="card" v-for="(item, key) in data" :key="key">
      <div class="d-flex align-items-center mb-4">
        <img
          :src="item.user?.photo"
          class="thumbnail thumbnail-xl"
          v-if="item.user.photo"
        />
        <img
          src="../../assets/images/user_default.png"
          class="thumbnail thumbnail-xl"
          v-else
        />
        <div class="fw-bold ms-4">
          <router-link to="/personalwall" class="link-dark">
            {{ item.user.name }}
          </router-link>
          <span class="d-block text-light fs-7 fw-normal">
            {{ $getTime(item.createdAt) }}
          </span>
        </div>
      </div>
      <div>
        <p class="mb-4">
          {{ item.content }}
        </p>
        <div
          class="bgCover mb-4"
          :style="{ backgroundImage: 'url(' + item.image + ')' }"
          v-if="item.image"
        ></div>
      </div>
    </li>
  </ul>

  <div
    class="bg-white border border-2 rounded shadow-sm"
    v-if="data.length == 0"
  >
    <div class="p-4 border-bottom border-2 border-dark">
      <ul class="d-flex">
        <li class="circle me-2"></li>
        <li class="circle circle-1 me-2"></li>
        <li class="circle circle-2"></li>
      </ul>
    </div>
    <div class="py-8 text-center text-light">
      目前尚無動態，新增一則貼文吧！
    </div>
  </div>
</template>

<script>
export default {
  name: "DynamicWallView",
  data() {
    return {
      data: [],
      filter: {
        sort: "desc",
        q: "",
      },
    };
  },
  methods: {
    getPosts(sort = "desc", q = "") {
      this.$emitter.emit("toggle-loading", true);
      this.$http
        .get(`${process.env.VUE_APP_API}/posts?sort=${sort}&q=${q}`)
        .then((res) => {
          this.data = res.data.data;
          this.$emitter.emit("toggle-loading", false);
        })
        .catch((err) => {
          this.$pushMessage({
            style: "danger",
            content: err.response.data.message || "取得貼文失敗",
          });
          this.$emitter.emit("toggle-loading", false);
        });
    },
    filterPost() {
      this.getPosts(this.filter.sort, this.filter.q);
    },
  },
  mounted() {
    this.getPosts();
  },
};
</script>
