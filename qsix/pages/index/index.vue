<template>
  <view>
    <view class="cu-bar bg-white search fixed">
      <view class="search-form round">
        <text class="cuIcon-search"></text>
        <input
          type="text"
          placeholder="输入搜索的关键词"
          confirm-type="search"
          v-model="searchValue"
        />
      </view>
      <!-- <view class="action">
        <button
          class="cu-btn bg-gradual-green shadow-blur round"
          @click="search"
        >
          搜索
        </button>
      </view> -->
    </view>
    <view class="cu-list menu-avatar">
      <view class="cu-item" v-for="(item, index) in list" :key="index">
        <view class="cu-avatar round lg">{{ item.name[0] }}</view>
        <view class="content">
          <view class="text-grey">{{ item.name }}</view>
        </view>
        <view class="action">
          <view class="text-grey text-lg"> {{ item.money }}00 元</view>
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
    };
  },
  watch: {
    searchValue(val, oldVal) {
      console.log("watch => ", { val, oldVal });
      if (val.length === 0) {
        console.log("update view");
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
 
<style>
</style>