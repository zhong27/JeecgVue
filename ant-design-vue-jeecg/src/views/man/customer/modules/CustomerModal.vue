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
            <a-form-item label="客户名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['customerName', validatorRules.customerName]" placeholder="请输入客户名称" ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="地址" :labelCol="labelCol" :wrapperCol="wrapperCol">
		      <j-area-linkage type="cascader" v-decorator="['addres',validatorRules.addres]" placeholder="请输入省市区" />
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label=" 电话号码" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['phone',validatorRules.phone]" placeholder="请输入 电话号码" ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="客户类型   " :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-radio-group buttonStyle="solid" v-decorator="[ 'customerType', {'initialValue':'common'}]">
                <a-radio disabled value="common">普通客户</a-radio>
                <a-radio disabled value="start">星级客户</a-radio>
              </a-radio-group>
              <!--<j-dict-select-tag type="list" v-decorator="['customerType']" :trigger-change="true" dictCode="customer_type" placeholder="请选择客户类型   " />-->
            </a-form-item>
          </a-col>
          <a-col :span="24">
            <a-form-item label="账户状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-radio-group buttonStyle="solid" v-decorator="[ 'accountStatus', {'initialValue':'normal'}]">
                <a-radio-button disabled value="normal">正常</a-radio-button>
                <a-radio-button disabled value="frozen">冻结</a-radio-button>
              </a-radio-group>

              <!--<j-dict-select-tag type="list" v-decorator="['accountStatus']" :trigger-change="true" dictCode="account_type" placeholder="请选择账户状态" />-->
            </a-form-item>
          </a-col>
<!--
          <a-col :span="24">
            <a-form-item label="营业执照" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-image-upload isMultiple  v-decorator="['price']" ></j-image-upload>
            </a-form-item>
          </a-col>
-->
        </a-row>
      </a-form>
    </a-spin>
  </j-modal>
</template>

<script>

  import { httpAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  import JImageUpload from '@/components/jeecg/JImageUpload'
  import JDictSelectTag from "@/components/dict/JDictSelectTag"
  import JAreaLinkage from '@comp/jeecg/JAreaLinkage'

  export default {
    name: "CustomerModal",
    components: { 
      JImageUpload,
      JDictSelectTag,
      JAreaLinkage,
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
          customerName: {
            rules: [
              { required: true, message: '请输入客户名称!'},
            ]
          },
          phone: {
            rules: [
              { required: true, message: '请输入电话号码!'},
              { pattern: /^1[3456789]\d{9}$/, message: '请输入正确的手机号码!'},
            ]
          },
          addres: {
            rules: [
              { required: true, message: '请输入地址!'},
            ]
          },
        },
        url: {
          add: "/man/customer/add",
          edit: "/man/customer/edit",
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
          this.form.setFieldsValue(pick(this.model,'customerName','addres','phone','customerType','accountStatus','price'))
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
        this.form.setFieldsValue(pick(row,'customerName','addres','phone','customerType','accountStatus','price'))
      },

      
    }
  }
</script>