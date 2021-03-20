<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form :form="form" slot="detail">
        <a-row>
          <a-col :span="12">
            <a-form-item label="仓库名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['houseName', validatorRules.houseName]" placeholder="请输入仓库名称"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="仓库地址" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-area-linkage v-decorator="['address', validatorRules.address]" placeholder="请输入仓库地址"  ></j-area-linkage>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="仓库负责人" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['houseMan', validatorRules.houseMan]" placeholder="请输入仓库负责人"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="负责人联系号码" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input v-decorator="['phone', validatorRules.phone]" placeholder="请输入负责人联系号码"  ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="占地面积" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number v-decorator="['area']" placeholder="请输入占地面积" style="width: 100%" />
            </a-form-item>
          </a-col>
          <a-col v-if="showFlowSubmitButton" :span="24" style="text-align: center">
            <a-button @click="submitForm">提 交</a-button>
          </a-col>
        </a-row>
      </a-form>
    </j-form-container>
  </a-spin>
</template>

<script>

  import { httpAction, getAction } from '@/api/manage'
  import pick from 'lodash.pick'
  import { validateDuplicateValue } from '@/utils/util'
  import JFormContainer from '@/components/jeecg/JFormContainer'
  import JAreaLinkage from '@comp/jeecg/JAreaLinkage'

  export default {
    name: 'WarehouseForm',
    components: {
      JFormContainer,
      JAreaLinkage
    },
    props: {
      //流程表单data
      formData: {
        type: Object,
        default: ()=>{},
        required: false
      },
      //表单模式：true流程表单 false普通表单
      formBpm: {
        type: Boolean,
        default: false,
        required: false
      },
      //表单禁用
      disabled: {
        type: Boolean,
        default: false,
        required: false
      }
    },
    data () {
      return {
        form: this.$form.createForm(this),
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
          houseName: {
            rules: [
              { required: true, message: '请输入仓库名称!'},
            ]
          },
          address: {
            rules: [
              { required: true, message: '请输入仓库地址!'},
            ]
          },
          houseMan: {
            rules: [
              { required: true, message: '请输入仓库负责人!'},
            ]
          },
          phone: {
            rules: [
              { required: true, message: '请输入负责人联系号码!'},
            ]
          },
        },
        url: {
          add: "/sto/warehouse/add",
          edit: "/sto/warehouse/edit",
          queryById: "/sto/warehouse/queryById"
        }
      }
    },
    computed: {
      formDisabled(){
        if(this.formBpm===true){
          if(this.formData.disabled===false){
            return false
          }
          return true
        }
        return this.disabled
      },
      showFlowSubmitButton(){
        if(this.formBpm===true){
          if(this.formData.disabled===false){
            return true
          }
        }
        return false
      }
    },
    created () {
      //如果是流程中表单，则需要加载流程表单data
      this.showFlowData();
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
          this.form.setFieldsValue(pick(this.model,'houseName','address','houseMan','phone','area'))
        })
      },
      //渲染流程表单数据
      showFlowData(){
        if(this.formBpm === true){
          let params = {id:this.formData.dataId};
          getAction(this.url.queryById,params).then((res)=>{
            if(res.success){
              this.edit (res.result);
            }
          });
        }
      },
      submitForm () {
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
            })
          }
         
        })
      },
      popupCallback(row){
        this.form.setFieldsValue(pick(row,'houseName','address','houseMan','phone','area'))
      },
    }
  }
</script>