<template>
  <v-stepper :value="step">
    <v-stepper-header>
      <v-stepper-step step="1" :complete="step > 1">基本信息</v-stepper-step>
      <v-divider/>
      <v-stepper-step step="2" :complete="step > 2">商品描述</v-stepper-step>
      <v-divider/>
      <v-stepper-step step="3" :complete="step > 3">规格参数</v-stepper-step>
      <v-divider/>
      <v-stepper-step step="4">SKU属性</v-stepper-step>
    </v-stepper-header>


    <v-stepper-items>

      <!--商品SPU信息录入-->
      <v-stepper-content step="1">
        <v-layout justify-center>
          <v-flex xs10>
            <v-form v-model="valid" ref="basic">
              <v-layout row wrap>
                <v-flex xs5><v-cascader url="/item/category" v-model="goods.categories" required show-all-levels label="商品分类"/></v-flex>
                <v-flex offset-xs2 xs5>
                  <v-select label="所属品牌" v-model="goods.brandId" :items="brandOptions" dense autocomplete item-text="name" item-value="id" :rules="[v => !!v || '品牌不能为空']" required/></v-flex>
                <v-flex xs12>
                  <v-text-field label="商品标题" v-model="goods.title" :counter="200" required :rules="[v => !!v || '商品标题不能为空']"/>
                  <v-text-field label="商品卖点" v-model="goods.subTitle" :counter="200"/>
                  <v-text-field label="包装清单" v-model="goods.spuDetail.packingList" :counter="1000" multi-line :rows="3"/>
                  <v-text-field label="售后服务" v-model="goods.spuDetail.afterService" :counter="1000" multi-line :rows="3"/>
                </v-flex>
              </v-layout>
            </v-form>
          </v-flex>
        </v-layout>

      </v-stepper-content>

      <!--商品描述-->
      <v-stepper-content step="2">

        <quill-editor style=" height:460px" v-model="goods.spuDetail.description" :options="editorOption"/>

      </v-stepper-content>

      <!--规格参数-->
      <v-stepper-content step="3">



      </v-stepper-content>

      <v-stepper-content step="4">

        <v-card class="mb-5" color="grey lighten-1" height="200px"></v-card>

      </v-stepper-content>

      </v-stepper-items>

  </v-stepper>
</template>

<script type="es6">
  import 'quill/dist/quill.core.css'
  import 'quill/dist/quill.snow.css'
  import 'quill/dist/quill.bubble.css'

  import {quillEditor} from 'vue-quill-editor'
    export default {
      components: {
        quillEditor
      },
      props:{
        step: {
          type: Number,
          default: 1,
        },
      },
        data(){
            return {
              valid: false,

              goods:{
                spuDetail: {},
              },
              brandOptions:[],

              editorOption: {// 富文本编辑器配置
                placeholder: '编写商品描述信息，让客户了解你的商品',
                height:500
              },
            }
        },
        methods: {
        },
      watch:{
        "goods.categories":{
          handler(){
            this.goods.brandId == null;
            let cid = this.goods.categories[2].id;
            //根据分类加载品牌
            this.$http.get("/item/brand/list-by-cid/"+cid).then(res =>{
              this.brandOptions = res.data;
            });
            //根据分类加载规格参数
            this.$http.get("/item/spec-param/list-by-param",{
              params:{
                cid: cid,
              }
            }).then(res =>{
              alert(JSON.stringify(res))
            })
          },
        }
      },
        mounted(){

        }
    }

</script>
