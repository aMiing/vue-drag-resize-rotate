<template>
  <div id="app">
    <div id="menu" :style="style">
      <el-menu class="el-menu-dom" :default-active="active">
        <el-submenu v-for="(item, index) in menuData" :key="index" :index="index + 1 + ''">
          <template slot="title">
            <i :class="item.icon"></i>
            {{ item.label }}
          </template>
          <el-menu-item
            v-for="(val, key) in item.children"
            :key="key"
            :index="index + 1 + '-' + (key + 1)"
            @click="goto(val)"
          >
            {{ val.label }}
          </el-menu-item>
        </el-submenu>
      </el-menu>
    </div>
    <!-- 路由视图 -->
    <div id="view">
      <div class="drag-bar" ref="dragBar"></div>
      <router-view />
      <div class="markdown-render" id="write" v-html="markdownHTML"></div>
    </div>
    <github-badge url="https://github.com/gausszhou/vue-drag-resize-rotate"></github-badge>
  </div>
</template>

<script>
import MarkdownIt from "markdown-it";
import hljs from "highlight.js";
import 'highlight.js/styles/github.css';
import GithubBadge from "@/components/github-badge";
import menuData from "./menu";

export default {
  name: "app",
  components: {
    GithubBadge
  },
  data() {
    return {
      menuData: menuData,
      active: "1-1",
      isDrag: false,
      widthX: 275,
      markdownRender: {},
      markdownHTML: ""
    };
  },
  computed: {
    style() {
      return {
        width: this.widthX + "px"
      };
    }
  },
  watch: {
    $route(to, from) {
      this.getMarkdown(to.path);
    }
  },
  created() {
    let width = localStorage.getItem("width");
    if (width) this.widthX = width;
    this.processMenuData();
  },
  mounted() {
    this.handleDragBarEvent();
    this.createMarkdownRender();
  },
  methods: {
    goto(val) {
      if (this.$route.name != val.route) {
        this.$router.push({ name: val.route });
      }
    },
    processMenuData() {
      this.menuData.forEach((element, order) => {
        element.children.forEach((val, key) => {
          element.children[key].index = order + 1 + "-" + (key + 1);
        });
      });
    },
    handleDragBarEvent() {
      this.$refs.dragBar.onmousedown = e => {
        this.isDrag = true;
      };
      document.onmouseup = e => {
        this.isDrag = false;
      };
      document.onmousemove = e => {
        if (this.isDrag) {
          this.widthX = e.x;
          localStorage.setItem("width", this.widthX);
        }
      };
    },
    createMarkdownRender() {
      this.markdownRender = new MarkdownIt({
        html: true,
        linkify: true,
        typographer: true,
        highlight: function(str, lang) {
          if (lang && hljs.getLanguage(lang)) {
            try {
              let preCode = hljs.highlight(lang, str, true).value;
              return preCode;
            } catch (e) {}
          }
        }
      });
    },
    getMarkdown(path) {
      let params = path.slice(1);
      this.$http.getMarkdown(params).then(res => {
        this.markdownHTML = this.markdownRender.render(res.data);
      });
    }
  }
};
</script>

<style scoped>
.title {
  margin-top: 0.5rem;
}
.markdown-render {
  width: 100%;
}
</style>
