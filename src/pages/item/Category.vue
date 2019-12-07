<template>
  <v-card>
      <v-flex xs12 sm10>
        <v-tree url="/item/category/list"
                :isEdit="isEdit"
                @handleAdd="handleAdd"
                @handleEdit="handleEdit"
                @handleDelete="handleDelete"
                @handleClick="handleClick"
        />
      </v-flex>
  </v-card>
</template>

<script>
  export default {
    name: "category",
    data() {
      return {
        isEdit:true,
      }
    },
    methods: {
      handleAdd(node) {
        //这里写ajax请求
        this.$http.post("/item/category/add",{
            id:node.id,
            name:node.name,
            parentId:node.parentId,
            isParent:node.isParent,
            sort:node.sort


        }).then(function (success) {
          
        }).catch(function (error) {
          
        })

        console.log("add .... ");
        console.log(node);
      },
      handleEdit(id, name) {
        console.log("这里打印测试一下顺序？")
        this.$http.get("/item/category/update",
          {
            params:{
          id:id,
          name:name}
        }).then(function (success) {
            console.log("成功")
        }).catch(function (error) {
            console.log("失败")
        })
        console.log("edit... id: " + id + ", name: " + name)
      },
      handleDelete(id) {
        this.$http.delete("/item/category/delete",
          {
            params:{
              id:id
            }

        }).then(function (success) {
          console.log("成功")
        }).catch(function (error) {
          console.log("失败")
        })
        console.log("delete ... " + id)
      },
      handleClick(node) {
        console.log(node)
      }
    }
  };
</script>

<style scoped>

</style>
