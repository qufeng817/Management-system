<template lang="">
    <div>
        <el-form label-width="80px" :model="spu">
          <el-form-item label="SPU名称" >
            <el-input  placeholder="SPU名称" v-model="spu.spuName"></el-input>
          </el-form-item>
          <el-form-item label="品牌">
            <el-select  placeholder="请选择品牌" v-model="spu.tmId">
              <el-option :label="tm.tmName" :value="tm.id" v-for="(tm,index) in tradeMarkList" :key="tm.id"></el-option>
            </el-select>
          </el-form-item>
          <el-form-item label="SPU描述">
            <el-input  type="textarea" rows="4" placeholder="SPU描述" v-model="spu.description"></el-input>
          </el-form-item>
          <el-form-item label="SPU图片">
            <el-upload
            action="/dev-api/admin/product/fileUpload"
            list-type="picture-card"
            :on-preview="handlePictureCardPreview"
            :on-remove="handleRemove"
            :file-list="spuImageList"
            :on-success="handlerSuccess"
            >
             <i class="el-icon-plus"></i>
            </el-upload>
            <el-dialog :visible.sync="dialogVisible">
              <img width="100%" :src="dialogImageUrl" alt="">
            </el-dialog>
          </el-form-item>
          <el-form-item label="销售属性">
            <el-select  :placeholder="`还有${unAttr.length}未选择`" v-model="attrIdAndName" >
                <el-option :label="unattr.name" :value="`${unattr.id}:${unattr.name}`" v-for="(unattr,index) in unAttr" :key="unattr.id"></el-option>
            </el-select>
            <el-button type="primary" icon="el-icon-plus" :disabled="!attrIdAndName" @click="addSaleAttr">添加销售属性</el-button>
            <el-table style="width:100%" border :data="spu.spuSaleAttrList">
              <el-table-column type="index" label="序号" width="80px"></el-table-column>
              <el-table-column prop="saleAttrName" label="属性名" ></el-table-column>
              <el-table-column prop="prop" label="属性值名称列表">
                <template slot-scope="{row,$index}">
                  <el-tag
                    :key="tag.id"
                    v-for="(tag,index) in row.spuSaleAttrValueList"
                    closable
                    :disable-transitions="false"
                    @close="row.spuSaleAttrValueList.splice(index,1)">
                    {{tag.saleAttrValueName}}
                  </el-tag>
                  <el-input
                    class="input-new-tag"
                    v-if="row.inputVisible"
                    v-model="row.inputValue"
                    ref="saveTagInput"
                    size="small"
                    @keyup.enter.native="handleInputConfirm"
                    @blur="handleInputConfirm(row)"
                  >
                  </el-input>
                  <el-button v-else class="button-new-tag" size="small" @click="addSaleAttrValue(row)">添加</el-button>
                </template>
              </el-table-column>
              <el-table-column label="操作">
                <template slot-scope="{row,$index}">
                  <el-button type="danger" icon="el-icon-delete" size="mini" @click="spu.spuSaleAttrList.splice(index,1)"></el-button>
                </template>
              </el-table-column>
            </el-table>
          </el-form-item>
          <el-form-item label="">
            <el-button type="primary" @click="addOrUpdateSpu">保存</el-button>
            <el-button @click="cancel">取消</el-button>
          </el-form-item>
        </el-form>
    </div>
</template>
<script>
export default {
    name:"SpuForm",
    data() {
        return {
        dialogImageUrl: '',
        dialogVisible: false,
      //spu属性初始化的时候是一个空的对象,在修改SPU的时候，会想服务器发请求，返回SPU信息（对象），在修改的时候可以利用服务器返回的这个对象收集最新的数据提交给服务器
      //添加SPU，如果是添加SPU的时候并没有向服务器发请求，数据收集到哪里呀[SPU]，收集数据的时候有哪些字段呀，看文档
      spu: {
        //三级分类的id
        category3Id: 0,
        //描述
        description: "",
        //spu名称
        spuName: "",
        //平台的id
        tmId: "",
        //收集SPU图片的信息
        spuImageList: [
          // {
          //   id: 0,
          //   imgName: "string",
          //   imgUrl: "string",
          //   spuId: 0,
          // },
        ],
        //平台属性与属性值收集
        spuSaleAttrList: [
          // {
          //   baseSaleAttrId: 0,
          //   id: 0,
          //   saleAttrName: "string",
          //   spuId: 0,
          //   spuSaleAttrValueList: [
          //     {
          //       baseSaleAttrId: 0,
          //       id: 0,
          //       isChecked: "string",
          //       saleAttrName: "string",
          //       saleAttrValueName: "string",
          //       spuId: 0,
          //     },
          //   ],
          // },
        ],
      },
        tradeMarkList:[],
        spuImageList:[],
        saleAttrList:[],
        attrIdAndName:''
        }
    },
    methods: {
      handleRemove(file, fileList) {
        // console.log(file, fileList);
      this.spuImageList = fileList;
      },
      handlePictureCardPreview(file) {
        this.dialogImageUrl = file.url;
        this.dialogVisible = true;
      },
      async initSpuData(spu){
        let spuResult = await this.$API.spu.reqSpu(spu.id);
        // console.log(spuResult);
        if(spuResult.code==200){
          this.spu=spuResult.data;
        }
        //品牌信息
        let tradeMarkResult= await this.$API.spu.reqTradeMarkList();
        if(tradeMarkResult.code==200){
          this.tradeMarkList = tradeMarkResult.data;
        } 
        //sup图片数据
        let spuImageResult= await this.$API.spu.reqSpuImageList(spu.id);
        if(spuImageResult.code==200){
        let listArr = spuImageResult.data;
        //由于照片墙显示图片的数据需要数组，数组里面的元素需要有name与url字段
        //需要把服务器返回的数据进行修改
        listArr.forEach((item) => {
          item.name = item.imgName;
          item.url = item.imgUrl;
        });
        this.spuImageList = listArr;
        }      
        //全部销售属性
        let saleResult= await this.$API.spu.reqBaseSaleAttrList();
        if(saleResult.code==200){
          this.saleAttrList = saleResult.data;
        }          
      },
      //照片墙上传成功
      handlerSuccess(response, file, fileList) {
        this.spuImageList=fileList
      },
      addSaleAttr() {
        const [baseSaleAttrId,saleAttrName] =this.attrIdAndName.split(":")
        // this.spuSaleAttrList.baseSaleAttrId=baseSaleAttrId;
        // this.spuSaleAttrList.saleAttrName=saleAttrName;
        let newSaleAttr = {
          baseSaleAttrId,
          saleAttrName,
          spuSaleAttrValueList: [],
        };
        this.spu.spuSaleAttrList.push(newSaleAttr)
        //清空数据
         this.attrIdAndName = "";
      },
      addSaleAttrValue(row) {
        //响应式数据
        this.$set(row, "inputVisible", true)
        //通过响应式数据inputValue字段收集新增的销售属性值
        this.$set(row, "inputValue", '');
      },
      handleInputConfirm(row) {
        const {baseSaleAttrId,inputValue,} = row

        if(inputValue.trim()=="") {
          this.$message('输入值不能为空')
          return
        }

        let reslut = row.spuSaleAttrValueList.every(
            (item)=>item.saleAttrValueName != inputValue
        )
        
        if(!reslut) return

        let newSaleAttrValue = {baseSaleAttrId,saleAttrValueName:inputValue}
        //新增
        row.spuSaleAttrValueList.push(newSaleAttrValue);
        //修改inputVisible为false，不就显示button
        row.inputVisible = false;
      },
      async addOrUpdateSpu() {
        this.spu.spuImageList = this.spuImageList.map(item=>{
          return {
            imageName: item.name,
            imageUrl: (item.response&&item.response.data) || item.url
          }
        })
        //发请求
        let result = await this.$API.spu.reqAddOrUpdateSpu(this.spu);
        if(result.code==200) {
          this.$message({type:'success',message:'保存成功'});
          this.$emit("changeScene",{scene:0,flag:this.spu.id?'修改':'添加'});
        }
        //清除数据
        Object.assign(this._data,this.$options.data());
      },

      //点击父组件通知子组件
      async addSpuData(category3Id) {
        this.spu.category3Id=category3Id;
        //品牌信息
        let tradeMarkResult= await this.$API.spu.reqTradeMarkList();
        if(tradeMarkResult.code==200){
          this.tradeMarkList = tradeMarkResult.data;
        } 
        //全部销售属性
        let saleResult= await this.$API.spu.reqBaseSaleAttrList();
        if(saleResult.code==200){
          this.saleAttrList = saleResult.data;
        } 
      },
      cancel() {
        this.$emit('changeScene',{scene:0,flag:''})

        //清除数据
        Object.assign(this._data,this.$options.data());

      }

    },
    computed:{
      unAttr() {
        let result= this.saleAttrList.filter(item=>{

          return this.spu.spuSaleAttrList.every(item1=>{
            return item.name != item1.saleAttrName 
          })
        })
        return result
      }
    }
}
</script>

<style>
  .el-tag + .el-tag {
    margin-left: 10px;
  }
  .button-new-tag {
    margin-left: 10px;
    height: 32px;
    line-height: 30px;
    padding-top: 0;
    padding-bottom: 0;
  }
  .input-new-tag {
    width: 90px;
    margin-left: 10px;
    vertical-align: bottom;
  }
</style> 