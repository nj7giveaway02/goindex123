<template>
  <nav class="navbar is-dark" role="navigation" aria-label="main navigation">
    <div class="container">
      <div class="navbar-brand">
        <a class="navbar-item" :href="currgd.id">
          <h3 class="title is-3 has-text-white">{{ siteName }}</h3>
        </a>
        <a
          role="button"
          :class="'navbar-burger burger ' + (isActive ? 'is-active' : '')"
          aria-label="menu"
          aria-expanded="false"
          data-target="navbarBasicExample"
          @click="burgerClick"
        >
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
          <span aria-hidden="true"></span>
        </a>
      </div>
      <div
        id="navbarBasicExample"
        :class="'navbar-menu ' + (isActive ? 'is-active' : '')"
      >
        <div class="navbar-start">
          <div
            class="navbar-item has-dropdown is-hoverable"
            v-if="gds.length > 0 && getCurrGD.length > 0"
          >
            <a class="navbar-link">{{ this.currgd.name }}</a>
            <div class="navbar-dropdown is-boxed">
              <a
                class="navbar-item"
                @click="changeItem(item)"
                v-for="(item, index) in getCurrGD"
                v-bind:key="index"
                >{{ item.name }}</a
              >
            </div>
          </div>
        </div>

        <div class="navbar-end">
          <!-- is-hidden-desktop -->
          <div class="navbar-item" v-show="showSearch">
            <div class="field is-grouped">
              <p class="control has-icons-left is-dark" style="width:100%;">
                <input
                  class="input is-rounded search-input"
                  @keyup.enter="query"
                  v-model="param"
                  type="search"
                  :placeholder="$t('search.placeholder')"
                  style="background-color: rgb(68, 66, 66);border-color: #272727;"
                />
                <span class="icon is-small is-left" style="padding:0 5px;">
                  <!-- <i class="fas fa-search"></i> -->
                  <img :src="eyes" />
                </span>
              </p>
            </div>
          </div>
          <header-setting />
          <a
            class="navbar-item is-hidden-desktop"
            @click.stop="$refs.viewMode.toggleMode"
          >
            <view-mode ref="viewMode" />
          </a>
        </div>
      </div>
    </div>
  </nav>
</template>

<script>
import headerSetting from "@/layout/header-aside/components/header-setting";
import ViewMode from "@/layout/viewmode";
export default {
  components: {
    headerSetting,
    ViewMode,
  },
  created() {
    this.siteName = document.getElementsByTagName("title")[0].innerText;
    if (window.gds && window.gds.length > 0) {
      this.gds = window.gds.map((item, index) => {
        return {
          name: item,
          id: "/" + index + ":/",
        };
      });
      this.chooseGD();
    }
    if (window.MODEL) {
      this.param = window.MODEL.q ? window.MODEL.q : "";
    }
  },
  data: function() {
    return {
      siteName: "",
      param: "",
      currgd: {},
      gds: [],
      isActive: false,
      eyes:
        "data:image/png;base64,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",
    };
  },
  methods: {
    chooseGD() {
      let index = this.$route.params.id;
      if (this.gds && this.gds.length >= index) {
        this.currgd = this.gds[index];
      }
    },
    changeItem(item) {
      this.currgd = item;
      this.$router.push({
        path: item.id,
      });
    },
    query() {
      if (this.param) {
        this.$router.push({
          path: this.currgd.id.match("/[0-9]+:") + "search?q=" + this.param,
        });
      }
    },
    burgerClick() {
      this.isActive = !this.isActive;
    },
  },
  computed: {
    getCurrGD() {
      return this.gds.filter((item) => item.name !== this.currgd.name);
    },
    showSearch() {
      // 文件夹不支持搜索
      return window.MODEL ? window.MODEL.root_type < 2 : true
    },
  },
  watch: {
    "$route.params.id": "chooseGD",
  },
};
</script>
