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
          <a-col :span="12">
            <a-form-item label="客户名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag type="list" v-decorator="['customer', validatorRules.customer]" :trigger-change="true" dictCode="per_customer,customer_name,id" placeholder="请选择客户名称" />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="业务员" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag type="list" v-decorator="['business', validatorRules.business]" :trigger-change="true" dictCode="per_business,name,id" placeholder="请选择业务员" />
            </a-form-item>
          </a-col>
          <!--<a-col :span="24">-->
            <!--<a-form-item label="订单编号" :labelCol="labelCol" :wrapperCol="wrapperCol">-->
              <!--<a-input v-decorator="['orderNo']" placeholder="请输入订单编号" ></a-input>-->
            <!--</a-form-item>-->
          <!--</a-col>-->
          <!--<a-col :span="24">-->
            <!--<a-form-item label="订单总价" :labelCol="labelCol" :wrapperCol="wrapperCol">-->
              <!--<a-input-number v-decorator="['orderTotal']" placeholder="请输入订单总价" style="width: 100%" />-->
            <!--</a-form-item>-->
          <!--</a-col>-->
          <a-col :span="12">
            <a-form-item label="司机" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-popup
                v-decorator="['driver', validatorRules.driver]"
                :trigger-change="true"
                org-fields="name,car_no,phone"
                dest-fields="driver,carNo,phone"
                code="per_driver"
                @callback="popupCallback"
              />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="车牌号" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input disabled v-decorator="['carNo', validatorRules.carNo]" placeholder="请输入车牌号" ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="电话号码" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input disabled v-decorator="['phone', validatorRules.phone]" placeholder="请输入电话号码" ></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="支付状态" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag disabled type="list" v-decorator="['payStatus', validatorRules.payStatus]" :trigger-change="true" dictCode="pay_status" placeholder="请选择支付状态" />
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
    name: "OrderBookingModal",
    components: { 
      JDictSelectTag,
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
          customer: {
            rules: [
              { required: true, message: '请输入客户名称!'},
            ]
          },
          business: {
            rules: [
              { required: true, message: '请输入业务员!'},
            ]
          },
          payStatus: {
            initialValue:"unpay",
            rules: [
            ]
          },
          driver: {
            rules: [
              { required: true, message: '请输入司机!'},
            ]
          },
          carNo: {
            rules: [
              { required: true, message: '请输入车牌号!'},
            ]
          },
          phone: {
            rules: [
              { required: true, message: '请输入电话号码!'},
            ]
          },
        },
        url: {
          add: "/ord/orderBooking/add",
          edit: "/ord/orderBooking/edit",
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
          this.form.setFieldsValue(pick(this.model,'createTime','customer','orderNo','orderTotal','driver','carNo','phone','payStatus','business'))
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
        this.form.setFieldsValue(pick(row,'createTime','customer','orderNo','orderTotal','driver','carNo','phone','payStatus','business'))
      },

      
    }
  }
</script>