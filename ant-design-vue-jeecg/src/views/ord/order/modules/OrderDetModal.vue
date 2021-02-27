<template>
  <j-modal
    :title="title"
    :width="width"
    :visible="visible"
    :confirmLoading="confirmLoading"
    switchFullscreen
    @ok="handleOk"
    @cancel="handleCancel"
    cancelText="关闭">
    <a-spin :spinning="confirmLoading">
      <a-form :form="form">
        <a-row>
          <a-col :span="24">
            <a-form-item label="材料名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-popup
                v-decorator="['productName', validatorRules.productName]"
                :trigger-change="true"
                org-fields="product_name,product_class,mat_len,mat_width,mat_thick,mat_no,warehouse"
                dest-fields="productName,productClass,matLen,matWidth,matThick,matNo,warehouse"
                code="sto_enter_house"
                @callback="popupCallback"
                />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="产品大类" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag type="list" v-decorator="['productClass', validatorRules.productClass]" :trigger-change="true" dictCode="product_class" placeholder="请选择产品大类" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="材料长度" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number v-decorator="['matLen']" placeholder="请输入材料长度" style="width: 100%" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="材料宽度" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number v-decorator="['matWidth']" placeholder="请输入材料宽度" style="width: 100%" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="材料厚度" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number v-decorator="['matThick']" placeholder="请输入材料厚度" style="width: 100%" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="材料号" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['matNo']" placeholder="请输入材料号" ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="仓库" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag type="list" v-decorator="['warehouse']" :trigger-change="true" dictCode="sto_warehouse,house_name,id" placeholder="请选择仓库" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="单价" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number v-decorator="['price']" placeholder="请输入单价" style="width: 100%" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="数量" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number v-decorator="['num']" placeholder="请输入数量" style="width: 100%" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="总价" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number v-decorator="['total']" placeholder="请输入总价" style="width: 100%" />
            </a-form-item>
          </a-col>
        </a-row>
      </a-form>
    </a-spin>
  </j-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  import JDictSelectTag from "@/components/dict/JDictSelectTag"

  export default {
    name: "OrderDetModal",
    components: {
      JDictSelectTag,
    },
    props:{
      mainId:{
        type:String,
        required:false,
        default:''
      }
    },
    data () {
      return {
        form: this.$form.createForm(this),
        title:"操作",
        width:800,
        visible: false,
        model: {},
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 },
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 },
        },

        confirmLoading: false,
        validatorRules: {
          productName: {
            rules: [
              { required: true, message: '请输入材料名称!'},
            ]
          },
          productClass: {
            rules: [
              { required: true, message: '请输入产品大类!'},
            ]
          },
        },
        url: {
          add: "/ord/orderBooking/addOrderDet",
          edit: "/ord/orderBooking/editOrderDet",
        }

      }
    },
    created () {
    },
    methods: {
      add () {
        this.edit({});
      },
      edit (record) {
        this.form.resetFields();
        this.model = Object.assign({}, record);
        this.visible = true;
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model,'createBy','createTime','updateBy','updateTime','sysOrgCode','orderId','productName','productClass','matLen','matWidth','matThick','matNo','warehouse','price','num','total'))
        })
      },
      close () {
        this.$emit('close');
        this.visible = false;
      },
      handleOk () {
        const that = this;
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true;
            let httpurl = '';
            let method = '';
            if(!this.model.id){
              httpurl+=this.url.add;
              method = 'post';
            }else{
              httpurl+=this.url.edit;
               method = 'put';
            }
            let formData = Object.assign(this.model, values);
            formData['orderId'] = this.mainId
            console.log("表单提交数据",formData)
            httpAction(httpurl,formData,method).then((res)=>{
              if(res.success){
                that.$message.success(res.message);
                that.$emit('ok');
              }else{
                that.$message.warning(res.message);
              }
            }).finally(() => {
              that.confirmLoading = false;
              that.close();
            })
          }

        })
      },
      handleCancel () {
        this.close()
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'createBy','createTime','updateBy','updateTime','sysOrgCode','orderId','productName','productClass','matLen','matWidth','matThick','matNo','warehouse','price','num','total'))
      },


    }
  }
</script>
