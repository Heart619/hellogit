<template>
  <div>
    <lite-header title="老人管理" tip="系统业务员通过此模块对老人数据进行维护" />
    <div class="flex spacebetween treelayout">
      <div class="rightbox">
        <!-- 上方统计栏区域 -->

        <!-- 右侧中部功能区域 -->
        <div class="whitebox flex marginb20 spacebetween">
          <div class="flex">
            <div style="margin-left:15px">签订年度：</div>
            <el-date-picker v-model="search.enSignYear" type="year" value-format="yyyy" placeholder="选择年度" style="width:6vw;" clearable></el-date-picker>
            <div style="margin-left:15px">企业名称：</div>
            <el-input size="small" v-model="search.enName" placeholder="" style="width:5vw" clearable />
            <el-button type="primary" icon="el-icon-search" size="small" @click="handleSearch" style="margin-left:10px">搜索</el-button>
          </div>
          <div class="flex"> <el-button type="primary" icon="el-icon-search" size="small" @click="pjexport()" style="margin-left:10px">导出</el-button></div>
        </div>

        <div class="whitebox">
          <!-- 右下方表格 -->
          <el-table v-loading="loading" stripe size="small" border :data="contractData" style="width: 100%">
            <el-table-column label="查询结果">
              <el-table-column prop="tinCompany" align="left" label="企业名称"></el-table-column>
            </el-table-column>
          </el-table>
          <div style="text-align:center">
            <el-pagination @current-change="change" :current-page="page" background layout="total, prev, pager, next" :total="total" style="margin-top:10px"></el-pagination>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
/**
 * 客户评价主页面
 * @author 王炳焱
 * @time 2021-08-04 11:05:00
 * @update [2021-08-04 11:05:00][王炳焱][手动生成]
 */
import fetch from "@/utils/fetch.js";
import { Message } from "element-ui";
export default {
  components: {},
  data() {
    return {
      departmentTreeData: [], //部门数据数组
      loading: false, //绑定v-loading
      //搜索区域相关变量
      search: {
        enName: '',
      },
      contractData: [],
      //分页区域相关变量
      page: 1, //页数
      total: 0, //总条数
      size: 10, //每页显示数
      dir: "desc", //排序方式
      sort: "tin_id", //排序字段
    }
  },
  mounted() {
    //初始加载页面方法
    this.loadData();
  },
  methods: {
    //初始加载页面方法
    async loadData() {
      //表格进入加载状态
      this.loading = true;
      //调取page接口获取数据
      const result = await fetch.post(
        '/api/twoinvestigate/page', {
        dir: this.dir,
        page: this.page,
        size: this.size,
        sort: this.sort,
        parameters: {
            enName: this.search.enName,
        }
      }, {
        headers: { post: { "Content-Type": "application/json" } }
      })
      //结束加载状态
      this.loading = false;
      //如果加载错误页面抛出异常提示
      if (result.code !== '0') this.$message.error(result.msg)
      //如果正常加载则对表格进行赋值
      if (result.code === '0') {
        this.contractData = result.data.content
        this.total = result.data.totalElements
      }
    },
    //页码按钮触发事件
    change(page) {
      this.page = page
      this.loadData()
    },
    //右侧中部搜索事件
    handleSearch() {
      this.page = 1;
      this.size = 10;
      this.loadData();
    },
    //时间戳转换
    shijian(time) {
      if (time != null) {
        return this.$moment(time).format("YYYY-MM-DD");
      } else {
        return "";
      }
    },
  }
}
</script>
