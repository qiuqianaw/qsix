<template>
  <view>
    <view class="cu-bar bg-white search fixed">
      <view class="search-form round">
        <text class="cuIcon-search"></text>
        <input
          type="text"
          placeholder="请输入需要搜索的姓名"
          confirm-type="search"
          v-model="searchValue"
        />
      </view>
      <view class="action" v-if="btnShow">
        <button class="cu-btn text-grey" @click="orderBtn()">
          <text class="cuIcon-order" />{{ order[orderNum] }}
        </button>
      </view>
    </view>

    <view class="cu-list menu-avatar">
      <view class="cu-item" v-for="(item, index) in list" :key="index">
        <view class="cu-avatar round lg">{{ item.name[0] }}</view>
        <view class="content">
          <view class="text-grey">{{ item.name }} </view>
          <view
            class="text-red text-sm"
            v-if="item.name[item.name.length - 1] === '?'"
          >
            <text class="cuIcon-infofill text-red margin-right-xs"></text>
            (数据有误，请手动修改）</view
          >
        </view>
        <view class="action">
          <view class="text-grey text-df"> {{ item.money }}00 元</view>
        </view>
      </view>
    </view>
    <view class="padding-xl text-center text-grey"
      >共 {{ list.length }} 条数据
    </view>

    <!-- Modal框 -->
    <!-- <view class="cu-modal" :class="modalName == 'Modal' ? 'show' : ''">
      <view class="cu-dialog">
        <view class="cu-bar bg-white">
          <view class="action text-gray" @tap="cancelModal()">取消</view>
          <view class="action text-grey" @tap="updateRow()">确定</view>
        </view>
        <view>
          <form class="text-left text-grey">
            <view class="cu-form-group">
              <view class="title">姓名</view>
              <input name="input" v-model="row.name" />
            </view>
            <view class="cu-form-group">
              <view class="title">金额</view>
              <input name="input" v-model="row.money" />
              <view class="cu-capsule radius">
                <view class="cu-tag">00 元</view>
              </view>
            </view>
          </form>
        </view>
      </view>
    </view> -->
  </view>
</template>
 
<script>
import { data } from "../../server/api/data";
export default {
  data() {
    return {
      list: [],
      searchValue: "",
      order: ["原始排序", "姓名排序", "金额排序"],
      orderNum: 0,
      modalName: "",
      row: { id: null, name: null, money: null },
      initData: [],
      btnShow: true,
    };
  },
  watch: {
    searchValue(val) {
      if (val.length === 0) {
        this.btnShow = true;
        const _this = this;
        this.$nextTick(() => {
          _this.loadData();
        });
      } else {
        this.btnShow = false;
      }
      this.list = this.fuzzyQuery(this.list, val);
    },
  },
  onLoad() {
    this.loadData();
  },
  onReady() {},
  methods: {
    // updateRow() {
    //   updateData(this.row);
    // },
    // showModal(index) {
    //   this.modalName = "Modal";
    //   this.row = this.list.filter((item) => item.id === index)[0];
    //   getOldVal(this.row);
    // },
    // cancelModal() {
    //   this.modalName = "";
    //   this.row = { id: null, name: null, money: null };
    // },
    orderBtn() {
      this.searchValue = "";
      this.orderNum = (this.orderNum + 1) % 3;
      const num = this.orderNum;
      if (num === 0) {
        console.log("原始排序");
        this.list.sort((a, b) => {
          return a.id - b.id;
        });
      } else if (num === 1) {
        this.list.sort((a, b) => {
          return a.name.localeCompare(b.name, "zh-Hans-CN", {
            sensitivity: "accent",
          });
        });
      } else {
        this.list.sort((a, b) => {
          return b.money - a.money;
        });
      }
    },
    loadData() {
      this.list = data;
      this.initData = data;
    },
    /**
     * 使用test方法实现模糊查询
     * @param  {Array}  list     原数组
     * @param  {String} keyWord  查询的关键词
     * @return {Array}           查询的结果
     */
    fuzzyQuery(_list, keyWord) {
      let arr = [];
      for (let i = 0; i < _list.length; i++) {
        if (_list[i].name.indexOf(keyWord) >= 0) {
          arr.push(_list[i]);
        }
      }
      return arr;
    },
  },
};
</script>
 
 