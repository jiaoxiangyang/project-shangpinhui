<template>
  <!-- 商品分类导航 -->
  <div class="type-nav">
    <div class="container">
      <!-- 事件委派|事件委托 -->
      <div @mouseleave="leaveIndex">
        <h2 class="all">全部商品分类</h2>
        <!-- 三级联动 -->
        <div class="sort">
          <!-- 利用事件委派+编程式导航实现路由的跳转与传递参数 -->
          <div class="all-sort-list2" @click="goSearch">
            <div
              class="item"
              v-for="(c1, index) in categoryList"
              :key="c1.categoryId"
              :class="{ cur: currentIndex === index }"
            >
              <h3 @mouseenter="changeIndex(index)">
                <a
                  :data-categoryName="c1.categoryName"
                  :data-category1Id="c1.categoryId"
                  >{{ c1.categoryName }}</a
                >
              </h3>
              <!-- 二级，三级分类 -->
              <div
                class="item-list clearfix"
                :style="{ display: currentIndex === index ? 'block' : 'none' }"
              >
                <div
                  class="subitem"
                  v-for="c2 in c1.categoryChild"
                  :key="c2.categoryId"
                >
                  <dl class="fore">
                    <dt>
                      <a
                        :data-categoryName="c2.categoryName"
                        :data-category2Id="c2.categoryId"
                        >{{ c2.categoryName }}</a
                      >
                    </dt>
                    <dd>
                      <em v-for="c3 in c2.categoryChild" :key="c3.categoryId">
                        <a
                          :data-categoryName="c3.categoryName"
                          :data-category3Id="c3.categoryId"
                          >{{ c3.categoryName }}</a
                        >
                      </em>
                    </dd>
                  </dl>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <nav class="nav">
        <a href="###">服装城</a>
        <a href="###">美妆馆</a>
        <a href="###">尚品汇超市</a>
        <a href="###">全球购</a>
        <a href="###">闪购</a>
        <a href="###">团购</a>
        <a href="###">有趣</a>
        <a href="###">秒杀</a>
      </nav>
    </div>
  </div>
</template>

<script>
import { mapState } from "vuex";
//引入方式：全部引入
// import _ from "lodash";
//最好的引入方式：按需加载
import throttle from "lodash/throttle";
export default {
  name: "TypeNav",
  //组件挂载完毕，向服务器请求数据
  mounted() {
    //通知Vuex发请求，获取数据，存储于仓库中
    this.$store.dispatch("categoryList");
  },
  computed: {
    ...mapState({
      //右侧需要一个函数，当使用这个计算属性的时候，右侧函数会立即执行一次
      //注入一个参数state，即为大仓库中的数据
      categoryList: (state) => state.home.categoryList,
    }),
  },
  data() {
    return {
      //存储用户鼠标移上哪一个一级分类
      currentIndex: -1,
    };
  },
  methods: {
    //鼠标进入修改响应式数据currentIndex属性
    //throttle回调函数别用箭头函数，可能会出现上下文this
    changeIndex: throttle(function (index) {
      //节流
      //正常情况（用户慢慢操作）：鼠标进入，每一个一级分类h3，都会触发鼠标进入事件
      //非正常情况（用户操作很快）：本身全部一级分类都应该触发鼠标进入事件，但是经过测试，只有部分h3触发了,浏览器没有反应过来
      //如果当前回调函数中有大量业务，有可能出现卡顿现象。
      this.currentIndex = index;
    }, 50),
    //一级分类鼠标移出的事件回调
    leaveIndex() {
      this.currentIndex = -1;
    },
    //进行路由跳转的方法
    goSearch(event) {
      // console.log(event);
      //最好的解决方案：编程式导航+事件委派
      //利用事件委派存在一些问题：1.点击的不一定是a标签 2.如何获取参数
      let element = event.target;
      // console.log(element.dataset);
      let { categoryname, category1id, category2id, category3id } =
        element.dataset;
      //如果标签上拥有categoryname一定是a标签
      if (categoryname) {
        //整理路由跳转的参数
        let location = { name: "search" };
        let query = { categoryName: categoryname };
        //一级分类，二级分类，三级分类
        if (category1id) {
          query.category1id = category1id;
        } else if (category2id) {
          query.category2id = category2id;
        } else {
          query.category3id = category3id;
        }
        //整理完参数
        location.query = query;
        //路由的跳转
        this.$router.push(location);
      }
    },
  },
};
</script>

<style lang="less" scoped>
.type-nav {
  border-bottom: 2px solid #e1251b;

  .container {
    width: 1200px;
    margin: 0 auto;
    display: flex;
    position: relative;

    .all {
      width: 210px;
      height: 45px;
      background-color: #e1251b;
      line-height: 45px;
      text-align: center;
      color: #fff;
      font-size: 14px;
      font-weight: bold;
    }

    .nav {
      a {
        height: 45px;
        margin: 0 22px;
        line-height: 45px;
        font-size: 16px;
        color: #333;
      }
    }

    .sort {
      position: absolute;
      left: 0;
      top: 45px;
      width: 210px;
      height: 461px;
      position: absolute;
      background: #fafafa;
      z-index: 999;

      .all-sort-list2 {
        .item {
          h3 {
            line-height: 30px;
            font-size: 14px;
            font-weight: 400;
            overflow: hidden;
            padding: 0 20px;
            margin: 0;

            a {
              color: #333;
            }
          }

          .item-list {
            display: none;
            position: absolute;
            width: 734px;
            min-height: 460px;
            background: #f7f7f7;
            left: 210px;
            border: 1px solid #ddd;
            top: 0;
            z-index: 9999 !important;

            .subitem {
              float: left;
              width: 650px;
              padding: 0 4px 0 8px;

              dl {
                border-top: 1px solid #eee;
                padding: 6px 0;
                overflow: hidden;
                zoom: 1;

                &.fore {
                  border-top: 0;
                }

                dt {
                  float: left;
                  width: 54px;
                  line-height: 22px;
                  text-align: right;
                  padding: 3px 6px 0 0;
                  font-weight: 700;
                }

                dd {
                  float: left;
                  width: 415px;
                  padding: 3px 0 0;
                  overflow: hidden;

                  em {
                    float: left;
                    height: 14px;
                    line-height: 14px;
                    padding: 0 8px;
                    margin-top: 5px;
                    border-left: 1px solid #ccc;
                  }
                }
              }
            }
          }
        }

        .cur {
          background-color: aquamarine;
        }
      }
    }
  }
}
</style>