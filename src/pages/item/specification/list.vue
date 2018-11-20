<template>
    <v-layout row wrap>

      <v-flex  xs3>
        <v-card  color="secondary">
          <v-tree url="/item/category"

                  :isEdit="false"
                  @handleClick="handleClick"
          />
        </v-card>
      </v-flex>
      <!--规格参数组
      -->
      <v-flex  xs9>
        <v-card  color="secondary">
          <v-toolbar flat color="white">
            <v-toolbar-title>规格参数组列表</v-toolbar-title>
            <v-divider
              class="mx-2"
              inset
              vertical
            ></v-divider>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="500px">
              <v-btn slot="activator" color="primary" dark class="mb-2">新建</v-btn>
              <v-card>
               <!-- <v-card-title>
                  <span style="background-color: #409EFF" class="headline">{{isEditSpecGroup ? "编辑规格参数组" : "新建规格参数在组"}}</span>
                </v-card-title>-->
                <!--对话框的标题-->
                <v-toolbar dense dark color="primary">
                  <v-toolbar-title>{{isEditSpecGroup ? "编辑规格参数组" : "新建规格参数在组"}}</v-toolbar-title>
                  <v-spacer/>
                  <!--关闭窗口的按钮-->
                  <v-btn icon @click="closeWindow">
                    <v-icon>close</v-icon>
                  </v-btn>
                </v-toolbar>
                <v-card-text>
                  <v-container grid-list-md>
                    <v-layout wrap>
                      <v-flex xs12 sm12>
                        <v-text-field v-model="editedItem.name" label="分组名称" :rules="nameRules" required></v-text-field>
                      </v-flex>
                    </v-layout>
                  </v-container>
                </v-card-text>

                <v-card-actions>
                  <v-spacer></v-spacer>
                  <v-layout class="my-4" row>
                    <v-spacer/>
                    <v-btn @click="submitSpecGroup" color="primary">提交</v-btn>
                    <v-btn @click="clearSpecGroup">重置</v-btn>
                  </v-layout>
                </v-card-actions>
              </v-card>
            </v-dialog>
          </v-toolbar>
          <v-data-table
            :headers="headers"
            :items="specGroupList"
            class="elevation-1"
          >
            <template slot="items" slot-scope="props">
              <td class="text-xs-center">{{ props.item.id }}</td>
              <td class="text-xs-center">{{ props.item.name }}</td>
              <td class="justify-center layout px-0">
                <v-btn icon @click="editBrand(props.item)"><i class="el-icon-edit"/></v-btn>
                <v-btn icon @click="deleteBrand(props.item)"><i class="el-icon-delete"/></v-btn>
              </td>
            </template>
            <template slot="no-data">
              <v-btn color="primary" @click="initialize">Reset</v-btn>
            </template>
          </v-data-table>
        </v-card>
      </v-flex>

    </v-layout>
</template>

<script type="es6">
    export default {
        data(){
            return {
              currentNode: {},
              isEditSpecGroup: false, //默认为新建
              nameRules: [
                v => !!v || "规格分钟名称不能为空",
                // v => v.length > 1 || "品牌名称至少2位"
              ],
              dialog: false,
              headers: [
                {
                  text: 'ID',
                  align: 'center',
                  sortable: true,
                  value: 'name'
                },
                { text: '组名',align: 'center', value: 'name' },
                { text: '操作', align: 'center', value: 'name', sortable: false }
              ],
              editedItem: {},
              defaultItem: {},

              specGroupList: [],//规格参数组列表
            }
        },
        methods: {
          closeWindow(){this.dialog = !this.dialog},
          initialize(){},
          submitSpecGroup(){
            if (this.currentNode.id == undefined || this.currentNode.id == null){
              this.$message.error("请选择分组所属分类！");
              return;
            }
            let params = {};
            params.cid = this.currentNode.id;
            params.name = this.editedItem.name;

          },
          clearSpecGroup(){},
          handleClick(node){
            if(!node.isParent){
              this.currentNode = node;
              this.$http.get("/item/spec-group/"+node.id).then(res =>{
                this.specGroupList = res.data;
              });
            }else{
              this.currentNode = {};
            }
          },
        },
      watch:{
        dialog:{
          handler(){
            if(this.dialog){
              if (this.currentNode.id == undefined || this.currentNode.id == null){
                this.$message.error("请先选择新建规格组分类！");
                // this.dialog = false;
              }
            }
          }
        }
      },
        mounted(){
        }
    }

</script>
