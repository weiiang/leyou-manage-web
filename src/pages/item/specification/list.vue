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
      <!--规格参数组-->
      <v-flex  xs9 v-if="showGroup">
        <v-card  color="secondary">
          <v-toolbar flat color="white">
            <v-toolbar-title>规格参数组列表</v-toolbar-title>
            <v-divider
              class="mx-2"
              inset
              vertical
            ></v-divider>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="800px">
              <v-btn slot="activator" color="primary" dark class="mb-2">新建</v-btn>
              <v-card>
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
            <template slot="items" slot-scope="props" >
              <!--<tr @click="showSpecParam(props.item.id)" > </tr>-->
                <td class="text-xs-center" >{{ props.item.id}}</td>
                <td class="text-xs-center, nameStyle" style="" @click="showSpecParam(props.item.id)">{{ props.item.name }}</td>
                <td class="justify-center layout px-0">
                  <v-btn icon @click="editSpec(props.item)"><i class="el-icon-edit"/></v-btn>
                  <v-btn icon @click="deleteSpec(props.item)"><i class="el-icon-delete"/></v-btn>
                </td>


            </template>
            <template slot="no-data">
              <v-btn color="primary" click="initialize">Reset</v-btn>
            </template>
          </v-data-table>
        </v-card>
      </v-flex>

      <v-flex  xs9 v-if="!showGroup">
        <v-card  color="secondary">
          <v-toolbar flat color="white">
            <v-toolbar-title @click="backToGroup"><v-btn slot="activator" color="primary" dark class="mb-2">返回规格组</v-btn></v-toolbar-title>
            <v-divider
              class="mx-2"
              inset
              vertical
            ></v-divider>
            <v-spacer></v-spacer>
            <v-dialog v-model="dialog" max-width="500px">
              <v-btn slot="activator" color="primary" dark class="mb-2">新建</v-btn>
              <v-card>
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
            :headers="sparamHeaders"
            :items="specParamList"
            class="elevation-1"
          >
            <template slot="items" slot-scope="props" >
              <tr>
                <td class="text-xs-center">{{ props.item.id }}</td>
                <td class="text-xs-center">{{ props.item.name }}</td>
                <td class="text-xs-center">{{ props.item.numeric ? "是" :"否"}}</td>
                <td class="text-xs-center">{{ props.item.unit }}</td>
                <td class="text-xs-center">{{ props.item.generic ? "是" :"否" }}</td>
                <td class="text-xs-center">{{ props.item.searching ? "是" :"否" }}</td>
                <td class="text-xs-center">{{ props.item.segments }}</td>
                <td class="justify-center layout px-0">
                  <v-btn icon @click="editBrand(props.item)"><i class="el-icon-edit"/></v-btn>
                  <v-btn icon @click="deleteBrand(props.item)"><i class="el-icon-delete"/></v-btn>
                </td>
              </tr>

            </template>
            <template slot="no-data">
              <v-btn color="primary" click="initialize">Reset</v-btn>
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
                { text: '组名',align: 'center', value: 'name',

                },
                { text: '操作', align: 'center', value: 'name', sortable: false }
              ],
              sparamHeaders: [
                {
                  text: 'ID',
                  align: 'center',
                  sortable: true,
                  value: 'name'
                },
                { text: '参数名称',align: 'center', value: 'name' },
                { text: '数字类型',align: 'center', value: 'numeric' },
                { text: '单位',align: 'center', value: 'unit' },
                { text: '通用属性',align: 'center', value: 'generic' },
                { text: '是否搜索',align: 'center', value: 'searching' },
                { text: '搜索条件',align: 'center', value: 'segments' },
                { text: '操作', align: 'center', value: 'name', sortable: false }
              ],
              editedItem: {},
              defaultItem: {},

              specGroupList: [],//规格参数组列表
              specParamList:[], //规格参数列表
              showGroup: true, //显示规格组
            }
        },
        methods: {
          //返回显示规格组
          backToGroup(){
            this.showGroup = true;
          },

          //点击规格组显示规格参数
          showSpecParam(specGroupId){
              this.$http.get("/item/spec-param/"+specGroupId).then(res =>{
                  this.specParamList = res.data;
                  this.showGroup = false;
              })
          },
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
          editSpec(item){
            alert(1)
          },
          deleteSpec(item){
            alert(2)
          },
          clearSpecGroup(){},
          handleClick(node){
            if(!node.isParent){
              this.currentNode = node;
              this.$http.get("/item/spec-group/"+node.id).then(res =>{
                this.specGroupList = res.data;
                this.showGroup = true;
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

<style>

  .nameStyle{
    /*background-color: rebeccapurple;*/
    text-align: center;
    color: blue;
    cursor: pointer;
  }
</style>
