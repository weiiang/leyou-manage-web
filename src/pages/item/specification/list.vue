<template>
  <v-layout row wrap>

    <v-flex xs3>
      <v-card color="secondary">
        <v-tree url="/item/category"
                :isEdit="false"
                @handleClick="handleClick"
        />
      </v-card>
    </v-flex>
    <!--规格参数组-->
    <v-flex xs9 v-if="showGroup">
      <v-card color="secondary">
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


              <!--新建表单-->
              <v-card-text>
                <v-layout row wrap>
                  <v-flex xs12 sm12>
                    <v-text-field v-model="editedItem.name" label="分组名称" :rules="nameRules" required></v-text-field>
                  </v-flex>

                  <v-flex xs12 sm12>
                    <v-btn color="info" @click="addRow">新增行</v-btn>
                  </v-flex>

                  <div v-for="(editedParam,index ) in editedParamList">
                    <v-flex xs12 sm12>
                      <v-layout row wrap>
                        <v-flex xs2>
                          <v-text-field v-model="editedParam.name" label="参数名称" :rules="nameRules"
                                        required></v-text-field>
                        </v-flex>

                        <v-flex xs1>
                          <v-select
                            v-model="editedParam.isNumeric"
                            @change="selectChange($event, index)"
                            :items="judgeList"
                            label="数值型"
                          ></v-select>
                        </v-flex>

                        <v-flex xs2>
                          <v-text-field :disabled="editedParam.disbaledUnit" v-model="editedParam.unit"
                                        label="单位"></v-text-field>
                        </v-flex>

                        <v-flex xs1>
                          <v-select
                            v-model="editedParam.isgeneric"
                            :items="judgeList"
                            label="通用属性"
                            @change="selectGenericChange($event, index)"
                          ></v-select>
                        </v-flex>

                        <v-flex xs1>
                          <v-select
                            v-model="editedParam.isSearching"
                            :items="judgeList"
                            label="搜素属性"
                            @change="selectSearchingChange($event, index)"
                          ></v-select>
                        </v-flex>

                        <v-flex xs3>
                          <v-text-field :disabled="editedParam.disbaledSearching" v-model="editedParam.segments"
                                        label="搜索值"></v-text-field>
                        </v-flex>

                        <v-flex xs2 sm2>
                          <div>
                            <v-btn color="error" @click="removeCurrentRow(index, editedParam)">移除行</v-btn>
                          </div>
                        </v-flex>
                      </v-layout>

                    </v-flex>
                  </div>
                </v-layout>
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
            <!--<tr @click="showSpecParam(props.item.id)" > </tr>-->
            <td class="text-xs-center">{{ props.item.id}}</td>
            <td class="text-xs-center, nameStyle" style="" @click="showSpecParam(props.item.id)">{{ props.item.name }}
            </td>
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

    <v-flex xs9 v-if="!showGroup">
      <v-card color="secondary">
        <v-toolbar flat color="white">
          <v-toolbar-title @click="backToGroup">
            <v-btn slot="activator" color="primary" dark class="mb-2">返回规格组</v-btn>
          </v-toolbar-title>
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
                <v-toolbar-title>{{isEditSpecGroup ? "编辑规格参数组" : "新建规格参数组"}}</v-toolbar-title>
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
          <template slot="items" slot-scope="props">
            <tr>
              <td class="text-xs-center">{{ props.item.id }}</td>
              <td class="text-xs-center">{{ props.item.name }}</td>
              <td class="text-xs-center">{{ props.item.numeric ? "是" :"否"}}</td>
              <td class="text-xs-center">{{ props.item.unit }}</td>
              <td class="text-xs-center">{{ props.item.generic ? "是" :"否" }}</td>
              <td class="text-xs-center">{{ props.item.searching ? "是" :"否" }}</td>
              <td class="text-xs-center">{{ props.item.segments }}</td>
              <td class="justify-center layout px-0">
                <v-btn icon @click="editParam(props.item)"><i class="el-icon-edit"/></v-btn>
                <v-btn icon @click="deleteParam(props.item)"><i class="el-icon-delete"/></v-btn>
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
        editedParam: {}, //规格参数
        disbaledUnit: false,
        specGroupId: "", //规格组ID
        editedParamList: [
          //{
          //  cid:this.currentNode.id,
          //  groupId:"",
          //  name:"",
          //  numeric: true,
          //  unit:"",
          //  generic: true,
          //  searching:true,
          //  segments:"",
          //  disbaledUnit: false,
          //  disbaledSearching: false,
          //}
        ],//规格参数列表
        showSegments: "", //是否显示搜索属性值
        judgeList: ["是", "否"], //是否的下拉框

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
          {
            text: '组名', align: 'center', value: 'name',

          },
          {text: '操作', align: 'center', value: 'name', sortable: false}
        ],
        sparamHeaders: [
          {
            text: 'ID',
            align: 'center',
            sortable: true,
            value: 'name'
          },
          {text: '参数名称', align: 'center', value: 'name'},
          {text: '数字类型', align: 'center', value: 'numeric'},
          {text: '单位', align: 'center', value: 'unit'},
          {text: '通用属性', align: 'center', value: 'generic'},
          {text: '是否搜索', align: 'center', value: 'searching'},
          {text: '搜索条件', align: 'center', value: 'segments'},
          {text: '操作', align: 'center', value: 'name', sortable: false}
        ],

        editedItem: {},
        defaultItem: {},

        specGroupList: [],//规格参数组列表
        specParamList: [], //规格参数列表
        showGroup: true, //显示规格组
      }
    },
    methods: {
      //规格参数组添加规格参数行
      addRow(){
        let editParam = {
          cid: this.currentNode.id,
          groupId: this.specGroupId,
          name: "",
          numeric: true,
          unit: "",
          generic: true,
          searching: true,
          segments: "",
          disbaledUnit: false,
          disbaledSearching: false,
          isNumeric: "是",
          isgeneric: "是",
          isSearching: "是",
        };
        this.editedParamList.push(editParam);
      },
      removeCurrentRow(editedParam, index){
        this.editedParamList.splice(index, 1);
      },
      selectChange(data, index){
        if (data == "是") {
          this.editedParamList[index].disbaledUnit = false;
          this.editedParamList[index].numeric = true;
        } else if (data = "否") {
          this.editedParamList[index].disbaledUnit = true;
          this.editedParamList[index].numeric = false;
        }
      },

      selectGenericChange(data, index){
        if (data == "是") {
          this.editedParamList[index].generic = true;
        } else if (data = "否") {
          this.editedParamList[index].generic = false;
        }
      },

      selectSearchingChange(data, index){
        if (data == "是") {
          this.editedParamList[index].disbaledSearching = false;
          this.editedParamList[index].searching = true;
        } else if (data = "否") {
          this.editedParamList[index].disbaledSearching = true;
          this.editedParamList[index].searching = false;
        }
      },
      //返回显示规格组
      backToGroup(){
        this.showGroup = true;
      },

      //点击规格组显示规格参数
      showSpecParam(specGroupId){
        this.specGroupId = specGroupId;
        this.$http.get("/item/spec-param/" + specGroupId).then(res => {
          this.specParamList = res.data;
          this.showGroup = false;
        })
      },
      closeWindow(){
        this.dialog = !this.dialog
        this.editedItem = {};
        this.editedParamList = [];
      },
      initialize(){
      },
      submitSpecGroup(){
        if (this.currentNode.id == undefined || this.currentNode.id == null) {
          this.$message.error("请选择分组所属分类！");
          return;
        }

        if (this.editedItem.id == undefined || this.editedItem.id == null) {//新增
          this.editedItem.cid = this.currentNode.id;
          //提交规格组新增数据
          this.$http.post("/item/spec-group", this.$qs.stringify(this.editedItem)).then(res => {
            if (this.editedParamList.length > 0) {
              this.addSpecParamList(res.data);
            }
            this.handleClick(this.currentNode);
            this.closeWindow();
            this.$message.success("新增成功！");
          });
        } else {//修改
          this.editedParamList.forEach(e => {
            e.groupId = this.editedItem.id;
          });
          this.$http.put("/item/spec-group?" + this.$qs.stringify(this.editedItem), this.editedParamList).then(res => {
            this.handleClick(this.currentNode);
            this.closeWindow();
            this.$message.success("修改成功！");
          });
        }
      },
      addSpecParamList(groupId){
        this.isEditSpecGroup = false;
        this.editedParamList.forEach(e => {
          e.groupId = groupId;
        });
        this.$http.post("/item/spec-param",
          this.editedParamList
        ).then(res => {
        })
      },
      editSpec(item){
        this.isEditSpecGroup = true;
        this.dialog = true;
        this.editedItem = item;
        this.$http.get("/item/spec-param/" + item.id).then(res => {
          this.editedParamList = [];
          res.data.forEach(e => {
            if (e.numeric) {
              e.isNumeric = "是";
              e.disbaledUnit = false;
            } else {
              e.isNumeric = "否";
              e.disbaledUnit = true;
            }

            if (e.searching) {
              e.isSearching = "是";
              e.disbaledSearching = false;
            } else {
              e.isSearching = "否";
              e.disbaledSearching = true;
            }

            if (e.generic) {
              e.isgeneric = "是";
            } else {
              e.isgeneric = "否";
            }
            this.editedParamList.push(e);
          })
        })
      },
      //删除规格参数组
      deleteSpec(item){
        this.$message.confirm('此操作将永久删除该品牌, 是否继续?').then(() => {
          let ids = [];
          ids.push(item.id);
          this.$http.delete("/item/spec-group", {
            params: {
              ids: ids
            }
          }).then(res => {
            this.handleClick(this.currentNode);
            this.$message.success("删除成功！");
          });
        }).catch(() => {
          this.$message.info("删除已取消！");
        });

      },
      //删除规格参数
      deleteParam(item){
        this.$message.confirm('此操作将永久删除该品牌, 是否继续?').then(() => {
          let param = [];
          param.push(item.id);
          this.$http.delete("/item/spec-param", {
            params: {ids: param}
          }).then(res => {
            this.showSpecParam(this.specGroupId);
            this.$message.success("删除成功！");
          });
        }).catch(() => {
          this.$message.info("删除已取消！");
        });
      },
      clearSpecGroup(){
      },
      handleClick(node){
        if (!node.isParent) {
          this.currentNode = node;
          this.$http.get("/item/spec-group/" + node.id).then(res => {
            this.specGroupList = res.data;
            this.showGroup = true;
          });
        } else {
          this.currentNode = {};
        }
      },
    },
    watch: {
      editedParam: {
        deep: true,
        handler(){
          if (this.editedParam.searching) {
            this.showSegments = true;
          } else {
            this.showSegments = false;
          }
        }
      },
      dialog: {
        handler(){
          if (this.dialog) {
            if (this.currentNode.id == undefined || this.currentNode.id == null) {
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

  .nameStyle {
    /*background-color: rebeccapurple;*/
    text-align: center;
    color: blue;
    cursor: pointer;
  }
</style>
