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
      <view class="action">
        <button class="cu-btn text-grey" @click="orderBtn()">
          <text class="cuIcon-order" />{{ order[orderNum] }}
        </button>
      </view>
    </view>

    <view class="cu-list menu-avatar">
      <view
        class="cu-item"
        v-for="(item, index) in list"
        :key="index"
        @click="showModal(item.id)"
      >
        <view class="cu-avatar round lg">{{ item.name[0] }}</view>
        <view class="content">
          <view class="text-grey">{{ item.name }} </view>
          <view
            class="text-red text-sm"
            v-if="item.name[item.name.length - 1] === '?'"
          >
            <text class="cuIcon-infofill text-red margin-right-xs"></text>
            (数据错误，请手动修改）</view
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
    <view class="cu-modal" :class="modalName == 'Modal' ? 'show' : ''">
      <view class="cu-dialog">
        <view class="cu-bar bg-white">
          <view class="action text-gray" @tap="modalName = ''">取消</view>
          <view class="action text-grey" @tap="modalName = ''">确定</view>
        </view>
        <view>
          <form class="text-left text-grey">
            <view class="cu-form-group">
              <view class="title">姓名</view>
              <input name="input" />{{ modalSelect.name }}<input />
            </view>
            <view class="cu-form-group">
              <view class="title">金额</view>
              <input name="input" />{{ modalSelect.money }}00
              <input />
            </view>
          </form>
        </view>
      </view>
    </view>
  </view>
</template>
 
<script>
import data from "../../server/api/data";
export default {
  data() {
    return {
      list: [],
      searchValue: "",
      order: ["原始排序", "姓名排序", "金额排序"],
      orderNum: 0,
      modalName: "",
      modalSelect: {},
    };
  },
  watch: {
    searchValue(val, oldVal) {
      if (val.length === 0) {
        const _this = this;
        this.$nextTick(() => {
          _this.loadData();
        });
        console.log(this.list);
      }
      this.list = this.fuzzyQuery(this.list, val);
    },
  },
  onLoad() {
    this.loadData();
  },
  onReady() {},
  methods: {
    showModal(index) {
      this.modalName = "Modal";
      this.modalSelect = this.list.filter((item) => item.id === index)[0];
      console.log(this.modalSelect);
    },
    orderBtn() {
      console.log(this.orderNum);
      this.searchValue = "";
      this.orderNum = (this.orderNum + 1) % 3;
      const num = this.orderNum;
      if (num === 0) {
        this.list = this.initData;
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
 
 