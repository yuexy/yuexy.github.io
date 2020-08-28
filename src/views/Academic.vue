<template>
  <div>
    <parallax
      class="section page-header header-filter"
      :style="headerStyle"
    ></parallax>
    <div class="main main-raised">
      <div class="section profile-content">
        <div class="container">
          <div class="md-layout">
            <div class="md-layout-item md-size-50 mx-auto">
              <div class="profile">
                <div class="avatar">
                  <img
                    :src="user_header"
                    alt="Circle Image"
                    class="img-raised rounded-circle img-fluid"
                  />
                </div>
                <div class="name">
                  <h3 class="title">{{ user_name }}</h3>
                  <h6>{{ user_title }}</h6>
                  <md-button
                    :href="user_link.github"
                    class="md-just-icon md-simple"
                    ><i class="fab fa-github-square"></i
                  ></md-button>
                  <md-button
                    :href="user_link.google_scholar"
                    class="md-just-icon md-simple"
                    ><i class="ai ai-google-scholar-square"></i
                  ></md-button>
                  <md-button
                    :href="user_link.linkedin"
                    class="md-just-icon md-simple"
                    ><i class="fab fa-linkedin"></i
                  ></md-button>
                </div>
              </div>
            </div>
          </div>
          <div class="description text-center">
            <span class="markdown-body" v-html="user_intro" />
          </div>
          <div class="wrapper">
            <hr />
            <div class="md-layout">
              <div class="md-layout-item md-size-20 md-small-size-100">
                <h3>Publications</h3>
              </div>
              <div class="md-layout-item md-size-80 md-small-size-100">
                <div
                  v-for="(p, pid) in publications"
                  :key="pid"
                  class="md-layout post"
                >
                  <div class="md-layout-item md-size-35 md-small-size-100">
                    <img :src="p.image" class="img-raised rounded pic" />
                  </div>
                  <div class="md-layout-item md-size-65 md-small-size-100">
                    <span class="markdown-body" v-html="p.intro" />
                  </div>
                </div>
              </div>
            </div>
          </div>
          <!-- <div class="wrapper">
            <hr />
            <div class="md-layout">
              <div class="md-layout-item md-size-20 md-small-size-100">
                <h3>Contact</h3>
              </div>
              <div class="md-layout-item md-size-65 md-small-size-100 wrapper">
                <div class="md-layout">
                  <div class="md-layout-item md-size-10 md-small-size-100">
                    <md-icon class="big-icon">email</md-icon>
                  </div>
                  <div class="md-layout-item md-size-35 md-small-size-100">
                    <h4>yuexiaoyu002@gmail.com</h4>
                  </div>
                </div>
              </div>
            </div>
          </div> -->
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "redaxios";
import MarkdownIt from "markdown-it";
const markDownIt = new MarkdownIt({ html: true });
export default {
  components: {},
  bodyClass: "academic-page",
  data() {
    return {
      user_name: "",
      user_title: "",
      user_intro: "",
      user_header: "doc/header.jpg",
      user_link: {},
      publications: []
    };
  },
  props: {
    header: {
      type: String,
      default: require("@/assets/img/image.jpg")
    },
    img: {
      type: String,
      default: require("@/assets/img/faces/christian.jpg")
    }
  },
  computed: {
    headerStyle() {
      return {
        backgroundImage: `url(${this.header})`
      };
    }
  },
  mounted() {
    this.loadData();
  },
  methods: {
    loadData() {
      const _this = this;
      _this.user_name = "222";
      axios.get("doc/academic.json").then(data => {
        _this.user_name = data.data.name;
        _this.user_title = data.data.title;
        _this.user_header = data.data.header;
        _this.user_link = data.data.link;
        const intro_md = axios.get(data.data.intro).then(intro => {
          _this.user_intro = markDownIt.render(intro.data);
        });
      });
      axios.get("doc/publications.json").then(data => {
        data.data.forEach((element, element_index) => {
          _this.publications.push(element);
          const intro_md = axios.get(element.intro).then(intro => {
            element.intro = markDownIt.render(intro.data);
          });
        });
      });
    }
  }
};
</script>

<style lang="scss" scoped>
.section {
  padding: 0;
}

.profile-tabs::v-deep {
  .md-card-tabs .md-list {
    justify-content: center;
  }

  [class*="tab-pane-"] {
    margin-top: 3.213rem;
    padding-bottom: 50px;

    img {
      margin-bottom: 2.142rem;
    }
  }
}
</style>
