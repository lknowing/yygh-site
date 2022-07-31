<template>
  <div class="home page-component">
    <el-carousel indicator-position="outside">
      <el-carousel-item v-for="item in 2" :key="item">
        <img src="~assets/images/web-banner1.png" alt="" />
      </el-carousel-item>
    </el-carousel>
    <!-- 搜索 -->
    <div class="search-container">
      <div class="search-wrapper">
        <div class="hospital-search">
          <el-autocomplete
            class="search-input"
            prefix-icon="el-icon-search"
            v-model="hosname"
            :fetch-suggestions="querySearchAsync"
            placeholder="点击输入医院名称"
            @select="handleSelect"
          >
            <span
              slot="suffix"
              class="search-btn v-link highlight clickable selected"
              >搜索
            </span>
          </el-autocomplete>
        </div>
      </div>
    </div>
    <!-- bottom -->
    <div class="bottom">
      <div class="left">
        <div class="home-filter-wrapper">
          <div class="title">医院</div>
          <div>
            <div class="filter-wrapper">
              <span class="label">等级：</span>
              <div class="condition-wrapper">
                <span
                  class="item v-link clickable"
                  :class="hostypeActiveIndex == index ? 'selected' : ''"
                  v-for="(item, index) in hostypeList"
                  :key="item.id"
                  @click="hostypeSelect(item.value, index)"
                  >{{ item.name }}</span
                >
              </div>
            </div>
            <div class="filter-wrapper">
              <span class="label">地区：</span>
              <div class="condition-wrapper">
                <span
                  class="item v-link clickable"
                  :class="provinceActiveIndex == index ? 'selected' : ''"
                  v-for="(item, index) in districtList"
                  :key="item.id"
                  @click="districtSelect(item.value, index)"
                  >{{ item.name }}</span
                >
              </div>
            </div>
          </div>
        </div>
        <div class="v-scroll-list hospital-list">
          <div
            class="v-card clickable list-item"
            v-for="item in list"
            :key="item.id"
          >
            <div class="">
              <div
                class="hospital-list-item hos-item"
                index="0"
                @click="show(item.hoscode)"
              >
                <div class="wrapper">
                  <div class="hospital-title">{{ item.hosname }}</div>
                  <div class="bottom-container">
                    <div class="icon-wrapper">
                      <span class="iconfont"></span
                      >{{ item.param.hostypeString }}
                    </div>
                    <div class="icon-wrapper">
                      <span class="iconfont"></span>每天{{
                        item.bookingRule.releaseTime
                      }}放号
                    </div>
                  </div>
                </div>
                <img
                  :src="'data:image/jpeg;base64,' + item.logoData"
                  :alt="item.hosname"
                  class="hospital-img"
                />
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="right">
        <div class="common-dept">
          <div class="header-wrapper">
            <div class="title">常见科室</div>
            <div class="all-wrapper">
              <span>全部</span>
              <span class="iconfont icon"></span>
            </div>
          </div>
          <div class="content-wrapper">
            <span class="item v-link clickable dark">神经内科 </span>
            <span class="item v-link clickable dark">消化内科 </span>
            <span class="item v-link clickable dark">呼吸内科 </span>
            <span class="item v-link clickable dark">内科 </span>
            <span class="item v-link clickable dark">神经外科 </span>
            <span class="item v-link clickable dark">妇科 </span>
            <span class="item v-link clickable dark"> 产科 </span>
            <span class="item v-link clickable dark">儿科 </span>
          </div>
        </div>
        <div class="space">
          <div class="header-wrapper">
            <div class="title-wrapper">
              <div class="icon-wrapper">
                <span class="iconfont title-icon"></span>
              </div>
              <span class="title">平台公告</span>
            </div>
            <div class="all-wrapper">
              <span>全部</span>
              <span class="iconfont icon"></span>
            </div>
          </div>
          <div class="content-wrapper">
            <div class="notice-wrapper">
              <div class="point"></div>
              <span class="notice v-link clickable dark"
                >关于延长北京大学国际医院放假的通知
              </span>
            </div>
            <div class="notice-wrapper">
              <div class="point"></div>
              <span class="notice v-link clickable dark"
                >北京中医药大学东方医院部分科室医生门诊医
              </span>
            </div>
            <div class="notice-wrapper">
              <div class="point"></div>
              <span class="notice v-link clickable dark">
                武警总医院号源暂停更新通知
              </span>
            </div>
          </div>
        </div>
        <div class="suspend-notice-list space">
          <div class="header-wrapper">
            <div class="title-wrapper">
              <div class="icon-wrapper">
                <span class="iconfont title-icon"></span>
              </div>
              <span class="title">停诊公告</span>
            </div>
            <div class="all-wrapper">
              <span>全部</span>
              <span class="iconfont icon"></span>
            </div>
          </div>
          <div class="content-wrapper">
            <div class="notice-wrapper">
              <div class="point"></div>
              <span class="notice v-link clickable dark">
                中国人民解放军总医院第六医学中心(原海军总医院)呼吸内科门诊停诊公告
              </span>
            </div>
            <div class="notice-wrapper">
              <div class="point"></div>
              <span class="notice v-link clickable dark">
                首都医科大学附属北京潞河医院老年医学科门诊停诊公告
              </span>
            </div>
            <div class="notice-wrapper">
              <div class="point"></div>
              <span class="notice v-link clickable dark"
                >中日友好医院中西医结合心内科门诊停诊公告
              </span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import hospApi from "@/api/hospital";

import dictApi from "@/api/dict";

export default {
  //服务端渲染异步，显示医院列表
  asyncData({ params, error }) {
    return hospApi.getPageList(1, 10, null).then((response) => {
      return {
        list: response.data.pageModel.content,
        pages: response.data.pageModel.totalPages,
      };
    });
  },
  data() {
    return {
      searchObj: {}, //查询条件
      page: 1,
      limit: 10,

      hosname: "", //医院名称
      hostypeList: [], //医院等级集合
      districtList: [], //地区集合

      hostypeActiveIndex: 0,
      provinceActiveIndex: 0,
    };
  },
  created() {
    //调用初始化方法
    this.init();
  },
  methods: {
    //初始化方法
    init() {
      //查询医院等级信息
      dictApi.findByDictCode("Hostype").then((response) => {
        //1.清空hostypeList集合数据
        this.hostypeList = [];
        //2.存入“全部”数据
        this.hostypeList.push({ name: "全部", value: "" });
        //3.遍历返回结果，存入hostypeList
        for (let i = 0; i < response.data.list.length; i++) {
          this.hostypeList.push(response.data.list[i]);
        }
      });
      //查询地区数据
      dictApi.findByDictCode("Beijing").then((response) => {
        //1.清空districtList集合数据
        this.districtList = [];
        //2.存入“全部”数据
        this.districtList.push({ name: "全部", value: "" });
        //3.遍历返回结果，存入hostypeList
        for (let i in response.data.list) {
          this.districtList.push(response.data.list[i]);
        }
      });
    },
    //根据医院等级查询医院信息
    hostypeSelect(value, index) {
      //1.准备参数
      this.list = [];
      this.page = 1;
      this.searchObj.hostype = value;
      //2.样式选中
      this.hostypeActiveIndex = index;
      //3.调用方法查询
      this.getList();
    },
    //根据地区查询医院信息
    districtSelect(value, index) {
      //1.准备参数
      this.list = [];
      this.page = 1;
      this.searchObj.districtCode = value;
      //2.样式选中
      this.provinceActiveIndex = index;
      //3.调用方法查询
      this.getList();
    },
    //根据条件查询医院信息方法
    getList() {
      hospApi
        .getPageList(this.page, this.limit, this.searchObj)
        .then((response) => {
          //this.list = response.data.pageModel.content;
          for (let i in response.data.pageModel.content) {
            this.list.push(response.data.pageModel.content[i]);
          }
          this.page = response.data.pageModel.totalPages;
        });
    },
    //自动搜索方法，在输入框输入值，弹出下拉框，显示相关内容
    querySearchAsync(queryString, cb) {
      //1.非空校验
      if (!queryString) return;
      //2.调用接口查询数据
      hospApi.getByHosname(queryString).then((response) => {
        //3.遍历集合添加value字段为hosname
        for (let i in response.data.list) {
          response.data.list[i].value = response.data.list[i].hosname;
        }
        //4.调用cb加载集合数据
        cb(response.data.list);
      });
    },
    //在下拉框选择某一个内容，执行下面方法，跳转到详情页面中
    handleSelect(item) {
      window.location.href = "/hospital/" + item.hoscode;
    },

    //点击某个医院名称，跳转到详情页面中
    show(hoscode) {
      window.location.href = "/hospital/" + hoscode;
    },
  },
};
</script>