<template>
  <v-card>
    <!--表格头部-->
    <v-toolbar class="elevation-0">
      <v-btn color="primary" @click="addSpu">新增</v-btn>
      <v-spacer/>
      <v-flex xs3>状态:
        <v-btn-toggle mandatory v-model="queryParams.saleable">
          <v-btn flat >全部</v-btn>
          <v-btn flat :value="true">上架</v-btn>
          <v-btn flat :value="false">下架</v-btn>
        </v-btn-toggle>
      </v-flex>
      <v-flex xs3>
        <v-text-field append-icon="search" label="搜索" v-model="queryParams.key"></v-text-field>
      </v-flex>
    </v-toolbar>

    <!--商品列表-->
    <div>
      <v-data-table
        :headers="headers"
        :items="tableData"
        :pagination.sync="pagination"
        :total-items="totalData"
        :loading="loading"
        class="elevation-1"
      >
        <template slot="items" slot-scope="props">
          <td class="text-xs-center">{{ props.item.title }}</td>
          <td class="text-xs-center">{{ props.item.cname }}</td>
          <td class="text-xs-center">{{ props.item.bname }}</td>
          <td class="text-xs-center">{{ props.item.saleable ? "上架" :"下架" }}</td>
          <td class="text-xs-center">{{ props.item.valid ? "可用" : "不可用" }}</td>
        </template>
      </v-data-table>
    </div>

    <!--弹出层-->
    <v-dialog v-model="dialog" width="1000px" height="1000px" hide-overlay transition="dialog-bottom-transition">
      <v-card>
        <v-toolbar dark color="primary">
          <v-btn icon dark @click="dialog = false">
            <v-icon>close</v-icon>
          </v-btn>
          <v-toolbar-title>新增商品</v-toolbar-title>
          <v-spacer></v-spacer>
          <v-toolbar-items>
            <v-btn dark flat @click="dialog = false">提交</v-btn>
          </v-toolbar-items>
        </v-toolbar>


        <v-card-text style="height: 600px;">
          <!-- 表单 -->
       <editForm v-bind:step="step"></editForm>
        </v-card-text>
        <v-card-actions>
          <v-layout row justify-center>
            <v-flex xs2>
              <v-btn :disabled="step === 1" @click="lastStep">上一步</v-btn>
            </v-flex>
            <v-flex xs2>
              <v-btn :disabled="step === 4" color="primary" @click="nextStep">下一步</v-btn>
            </v-flex>
          </v-layout>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-card>
</template>

<script type="es6">
  import editForm from './edit.vue'
  export default {
    components:{
      editForm
    },
    data(){
      return {
        //弹出层相关
        dialog: false,
        notifications: false,
        sound: true,
        widgets: false,

        step: 1,

        queryParams: {}, //查询参数

        totalData: 0,
        tableData: [],
        loading: false,
        pagination: {},
        headers: [
          {text: '商品名称', align: 'center', sortable: false, value: 'title'},
          {text: '商品分类', align: 'center', sortable: false, value: 'cname'},
          {text: '商品品牌', align: 'center', sortable: false, value: 'bname'},
          {text: '上/下架', align: 'center', sortable: false, value: 'saleable'},
          {text: '状态', align: 'center', sortable: false, value: 'valid'},
        ]
      }
    },
    methods: {

      lastStep(){
        this.step --;
      },
      nextStep(){
        this.step ++;
      },

      //查询商品列表
      getTableData(){
        this.loading = true;
        this.$http.get("/item/spu/page",
          {
            params:{
              saleable: this.queryParams.saleable,
              page: this.pagination.page,
              row: this.pagination.rowsPerPage,
              key: this.queryParams.key
            }
          }
          ).then(res => {
          this.tableData = res.data.records;
          this.totalData = res.data.total;
          this.loading = false;
        }).catch(err => {
          this.$message.error("加载失败!")
        });
      },

      //添加商品表单显示
      addSpu(){
        this.dialog = true;
      },

    },
    watch:{
      pagination:{
        deep:true,
        handler(){
          this.getTableData();
        }
      },

      queryParams:{
        deep:true,
        handler(){
          this.getTableData();
        }
      }
    },
    mounted(){
      this.getTableData();
    }
  }

</script>
