<template>
    <div>
        <el-card style="margin:20px 0px">
            <CategorySelect @getCategoryId="getCategoryId" :show="!isShow"></CategorySelect>
        </el-card>

        <el-card>
            <div v-show="isShow">
                <el-button 
                  type="primary" 
                  icon="el-icon-plus"
                  :disabled="!category3Id"
                  @click="addAttr"
                  >添加属性
                </el-button>  
                <el-table stlye="width:100%" border :data="attrList">
                  <el-table-column type="index" label="序号" width="80" align="center"></el-table-column>
                  <el-table-column prop="attrName" label="属性名称" width=150></el-table-column >
                  <el-table-column label="属性值列表">
                    <template slot-scope="{row,$index}">
                        <el-tag 
                        type="success"
                        v-for="(attrValue,index) in row.attrValueList" 
                        :key="attrValue.id"
                        style="margin:5px 10px"
                        >
                        {{attrValue.valueName}}   
                        </el-tag>
                    </template>
                  </el-table-column>
                  <el-table-column label="操作" width=150>
                      <template slot-scope="{row,$index}">
                          <el-button
                              type="warning"
                              icon="el-icon-edit"
                              size="mini"
                              @click="editAttr(row)"
                          ></el-button>
                          <el-button
                              type="danger"
                              icon="el-icon-delete"
                              size="mini"
                          ></el-button>
                      </template>
                  </el-table-column>
            </el-table>  
            </div>

            <!-- 添加/修改属性 -->
            <div v-show="!isShow">
                <el-form :inline="true" label-width="80px" :model="attrInfo">
                  <el-form-item label="属性名" >
                    <el-input 
                    placeholder="请输入属性名"
                    v-model="attrInfo.attrName"
                    ></el-input>
                  </el-form-item>
                </el-form>
                <el-button 
                 type="primary" 
                 icon="el-icon-plus" 
                 @click="addAttrValue" 
                 :disabled="!attrInfo.attrName"
                >添加属性值</el-button>
                <el-button @click="isShow=true">取消</el-button>
                <el-table  style="width: 100%;margin:20px 0px" border :data="attrInfo.attrValueList">
                  <el-table-column type="index" label="序号" width="80" align="center"></el-table-column>
                  <el-table-column label="属性值名称" width="width">
                    <template slot-scope="{row,$index}">
                        <!-- span与input切换 -->
                        <el-input 
                        v-model="row.valueName" 
                        placeholder="请输入属性值名称"
                        size="mini"
                        v-if="row.flag"
                        @blur="toLook(row)"
                        @keyup.native.enter="toLook(row)"
                        :ref="$index"
                        ></el-input>
                        <span v-else @click="toEdit(row,$index)" style="display: block">{{row.valueName}}</span>
                    </template>
                  </el-table-column>
                  <el-table-column label="操作" width="width">
                    <template slot-scope="{row,$index}">
                    <el-popconfirm :title="`确定删除?${row.valueName}`" @onConfirm="deleteAttrValue($index)" >
                        <el-button
                          type="danger"
                          icon="el-icon-delete"
                          size="mini"
                          slot="reference"
                        ></el-button>
                    </el-popconfirm>                       
                    </template>
                  </el-table-column>
                </el-table>
                <el-button type="primary"  @click="reqAddOrUpdateAttr" :disabled="!attrInfo.attrValueList.length">保存</el-button>
                <el-button @click="isShow=true">取消</el-button>

                                    
            </div>
        </el-card> 
    </div>
</template>

<script>
//引入深拷贝
import cloneDeep from 'lodash/cloneDeep'
export default {
    name:"Attr",
    data() {
        return {
          category1Id:'',
          category2Id:'',
          category3Id:'',
          attrList:[],
          isShow:true,
         //收集新增属性|修改属性使用的
         attrInfo: {
           attrName: "", //属性名
           attrValueList: [
             //属性值，因为属性值可以有多个因此用数组，每一个属性值都是一个对象需要attrId，valueName
            //     {
            //         attrId:0,
            //         valueName:""
            //    }
           ],
           categoryId: 0, //三级分类的id
           categoryLevel: 3, //因为服务器也需要区分几级id
         },
        }
    }, 
    methods:{
        getCategoryId({categoryId,level}) {
            if(level==1) {
                this.category1Id=categoryId;
                this.category2Id='';
                this.category3Id='';
            } else if(level==2) {
                this.category2Id=categoryId;
                this.category3Id='';
            }else {
                this.category3Id=categoryId;
                this.getAttrList()
            }
        },
        async getAttrList() {
            let result = await this.$API.attr.reqAttrList(this.category1Id,this.category2Id,this.category3Id) 
            if(result.code==200) {
                this.attrList=result.data
            }
        },
        addAttrValue() {
            this.attrInfo.attrValueList.push({
                    attrId:this.attrInfo.id,
                    valueName:"",
                     flag:true            
            });
            this.$nextTick(()=>{
                this.$refs[this.attrInfo.attrValueList.length-1].focus()
            })
            
        },
        addAttr() {
            this.isShow=false
            this.attrInfo={
                attrName: "", //属性名
                attrValueList: [
                ],
                categoryId: this.category3Id, //三级分类的id
                categoryLevel: 3,  
            }
        },
        editAttr(row){
            this.isShow=false
            this.attrInfo=cloneDeep(row); 
            this.attrInfo.attrValueList.forEach(item=>{
                this.$set(item,'flag',false);
            });

        },
        //切换span与input
        toLook(row) {
            if(row.valueName.trim()==''){
                this.$message('输入一个正常的属性值')
                return
            }

            let isReapat = this.attrInfo.attrValueList.some(item=>{
                if(row!==item) {
                    return row.valueName==item.valueName;
                }
            });
            // console.log(isReapat);
            if(isReapat) return;
            row.flag=false;
        },
        toEdit(row,index){
            row.flag=true
            this.$nextTick(()=>{
                this.$refs[index].focus()
            })
        },
        //删除属性
        deleteAttrValue(index) {
            this.attrInfo.attrValueList.splice(index,1)
        },
        //保存数据
       async reqAddOrUpdateAttr() {
            //整理参数要注意
             this.attrInfo.attrValueList = this.attrInfo.attrValueList.filter((item)=>{
                if(item.valueName!='') {
                   delete item.flag;
                   return true
                }
            })
            try {
                await this.$API.attr.reqAddOrUpdateAttr(this.attrInfo);
                 this.isShow=true;
                this.$message({type:'success',message:'保存成功'})
                this.getAttrList();
            } catch (e) {
            }  
        }
    }
}
</script>
<style lang="">
    
</style>