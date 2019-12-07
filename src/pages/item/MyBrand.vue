<template>
  <v-card>
    <v-card-title>
      <v-btn color="primary" @click="addBrand">新增品牌</v-btn>
      <v-spacer/>
      <v-text-field label="输入关键字搜索" v-model.lazy="search" single-line append-icon="search" hide-details/>
    </v-card-title>
    <v-divider/>
    <v-data-table :headers="headers" :items="brands" :pagination.sync="pagination" :total-items="totalBrands" :loading="loading" class="elevation-1">
      <template slot="items" slot-scope="props">
        <td>{{ props.item.id }}</td>
        <td class="text-xs-center">{{ props.item.name }}</td>
        <td class="text-xs-center"><img :src="props.item.image"></td>
        <td class="text-xs-center">{{ props.item.letter }}</td>
        <td class="justify-center layout">
          <v-btn color="info" @click="editBrand(props.item)">编辑</v-btn>
          <v-btn color="warning">删除</v-btn>
        </td>
      </template>

    </v-data-table>

    <v-dialog v-model="show" max-width="600" scrollable v-if="show">
      <v-card>
        <v-toolbar dark dense color="primary">
          <v-toolbar-title>{{isEdit ? '修改品牌' : '新增品牌'}}</v-toolbar-title>
          <v-spacer/>
          <v-btn icon @click="show = false">
            <v-icon>close</v-icon>
          </v-btn>
        </v-toolbar>
        <v-card-text class="px-5 py-2">
          <!-- 表单 -->
          <my-brand-form @close="closeWindow" :oldBrand="oldBrand" :isEdit="isEdit" :show="show" />
        </v-card-text>
      </v-card>
    </v-dialog>

  </v-card>
</template>

<script>
  import MyBrandForm from './MyBrandForm'
  export default {
    name: "my-brand",
    components: {
      MyBrandForm
    },
    data() {
      return {
        search: '', // 搜索过滤字段
        totalBrands: 0, // 总条数
        brands: [], // 当前页品牌数据
        loading: true, // 是否在加载中
        pagination: {}, // 分页信息
        headers: [
          {text: 'id', align: 'center', value: 'id'},
          {text: '名称', align: 'center', sortable: false, value: 'name'},
          {text: 'LOGO', align: 'center', sortable: false, value: 'image'},
          {text: '首字母', align: 'center', value: 'letter', sortable: true,},
          {text: '操作', align: 'center', value: 'id', sortable: false}
        ],
        show: false,// 是否弹出窗口
        //brand: {}, // 品牌信息
        isEdit: false, // 判断是编辑还是新增
        oldBrand:{}   //即将被编辑的品牌信息
      }
    },
    mounted() { // 渲染后执行
      // 查询数据
      this.getDataFromServer();
    },
    watch: {
      pagination: { // 监视pagination属性的变化
        deep: true, // deep为true，会监视pagination的属性及属性中的对象属性变化
        handler() {
          // 变化后的回调函数，这里我们再次调用getDataFromServer即可
          this.getDataFromServer();
        }
      },
      search: { // 监视搜索字段
        handler() {
          this.getDataFromServer();
        }
      },
      show(val, oldVal) {
        // 表单关闭后重新加载数据
        if (oldVal) {
          this.getDataFromServer();
        }
      }
    },
    methods: {
      addBrand() {
        this.oldBrand = {};
        this.isEdit = false;
        this.show = true;
      },
      editBrand(oldBrand){
        this.$http.get("/item/category/bid/" + oldBrand.id)
          .then(({data}) => {
            // 控制弹窗可见：
            console.log(data);
            this.show = true;
            this.isEdit=true;
            // 获取要编辑的brand
            this.oldBrand = oldBrand;
            // 回显商品分类

            this.oldBrand.categories = data;
          })
      },
      getDataFromServer() { // 从服务的加载数的方法。
        // 发起请求
        this.$http.get("/item/brand/page", {
          params: {
            key: this.search, // 搜索条件
            page: this.pagination.page,// 当前页
            rows: this.pagination.rowsPerPage,// 每页大小
            sortBy: this.pagination.sortBy,// 排序字段
            desc: this.pagination.descending// 是否降序
          }
        }).then(resp => { // 这里使用箭头函数
          this.brands = resp.data.items;
          this.totalBrands = resp.data.total;
          // 完成赋值后，把加载状态赋值为false
          this.loading = false;
        })
      },
      closeWindow(){
        this.show=false;
        this.getDataFromServer()
      }
    }
  }
</script>

<style scoped>

</style>
