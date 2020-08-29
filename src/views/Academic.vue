<template>
  <div>
    <parallax
      class="section page-header header-filter"
      :style="headerStyle"
    ></parallax>
    <el-backtop :right="40" :bottom="40" />
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
          <div class="wrapper">
            <hr />
            <div class="md-layout">
              <div class="md-layout-item md-size-20 md-small-size-100">
                <h3>Experience</h3>
              </div>
              <div class="md-layout-item md-size-80 md-small-size-100">
                <div class="wrapper">
                  <el-timeline :reverse="true">
                    <el-timeline-item v-for="(p, pid) in experience" :key="pid">
                      <el-card>
                        <span class="markdown-body" v-html="p.intro" />
                      </el-card>
                    </el-timeline-item>
                  </el-timeline>
                </div>
              </div>
            </div>
          </div>
          <div class="wrapper" style="margin-top: 0px; margin-bottom: 35px;">
            <hr />
            <div class="md-layout">
              <div class="md-layout-item md-size-20 md-small-size-100">
                <h3>Skills</h3>
              </div>
              <div class="md-layout-item md-size-80 md-small-size-100">
                <div class="wrapper">
                  <nav-tabs-card no-label tabs-plain>
                    <template slot="content">
                      <md-tabs class="md-info" md-alignment="left">
                        <md-tab
                          v-for="(s, sid) in skills"
                          :key="sid"
                          :id="s.name"
                          :md-label="s.name"
                        >
                          <div class="md-layout">
                            <div
                              v-for="(i, iid) in s.items"
                              :key="iid"
                              class="md-layout-item md-size-33 md-small-size-100"
                              style="margin-top: 12px;"
                            >
                              <h5>{{ i.name }}</h5>
                              <el-rate
                                :value="i.rate"
                                disabled
                                :colors="[
                                  '#ef5350',
                                  '#ef5350',
                                  '#ef5350',
                                  '#ef5350',
                                  '#ef5350'
                                ]"
                              />
                            </div>
                          </div>
                        </md-tab>
                      </md-tabs>
                    </template>
                  </nav-tabs-card>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "redaxios";
import MarkdownIt from "markdown-it";
import { NavTabsCard } from "@/components";
const markDownIt = new MarkdownIt({ html: true });
export default {
  components: {
    NavTabsCard
  },
  bodyClass: "academic-page",
  data() {
    return {
      user_name: "",
      user_title: "",
      user_intro: "",
      user_header: "doc/header.jpg",
      user_link: {},
      publications: [],
      experience: [],
      skills: []
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
        axios.get(data.data.intro).then(intro => {
          _this.user_intro = markDownIt.render(intro.data);
        });
      });
      axios.get("doc/publications.json").then(data => {
        data.data.forEach(element => {
          _this.publications.push(element);
          axios.get(element.intro).then(intro => {
            element.intro = markDownIt.render(intro.data);
          });
        });
      });
      axios.get("doc/experience.json").then(data => {
        data.data.forEach(element => {
          _this.experience.push(element);
          axios.get(element.intro).then(intro => {
            element.intro = markDownIt.render(intro.data);
          });
        });
      });
      axios.get("doc/skills.json").then(data => {
        _this.skills = data.data;
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
