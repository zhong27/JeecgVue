<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form :form="form" slot="detail">
        <a-row>
          <a-col :span="12">
            <a-form-item label="材料名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-popup
                v-decorator="['productName', validatorRules.productName]"
                :trigger-change="true"
                org-fields="product_name,product_class,mat_len,mat_width,mat_thick,mat_no"
                dest-fields="productName,productClass,matLen,matWidth,matThick,matNo"
                code="sto_mater_info"
                @callback="popupCallback"
              />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="产品大类" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag disabled type="list" v-decorator="['productClass', validatorRules.productClass]"
                                 :trigger-change="true" dictCode="product_class" placeholder="请选择产品大类"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="材料长度" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number disabled v-decorator="['matLen', validatorRules.matLen]" placeholder="请输入材料长度"
                              style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="材料厚度" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number disabled v-decorator="['matThick', validatorRules.matThick]" placeholder="请输入材料厚度"
                              style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="材料宽度" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number disabled v-decorator="['matWidth', validatorRules.matWidth]" placeholder="请输入材料宽度"
                              style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="材料号" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input disabled v-decorator="['matNo', validatorRules.matNo]" placeholder="请输入材料号"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="材料重量" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number v-decorator="['matWeight', validatorRules.matWeight]" placeholder="请输入材料重量" style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="材料数量" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number v-decorator="['matNumber', validatorRules.matNumber]" placeholder="请输入材料数量" style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="仓库" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag v-decorator="['warehouse', validatorRules.warehouse]" :trigger-change="true"
                                 dictCode="sto_warehouse,house_name,id" placeholder="请输入仓库"></j-dict-select-tag>
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
  import JDictSelectTag from '@/components/dict/JDictSelectTag.vue'

  export default {
    name: 'EnterHouseForm',
    components: {
      JFormContainer,
      JDictSelectTag
    },
    props: {
      //流程表单data
      formData: {
        type: Object,
        default: () => {
        },
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
    data() {
      return {
        form: this.$form.createForm(this),
        model: {},
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 }
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 }
        },
        confirmLoading: false,
        validatorRules: {
          productClass: {
            rules: [
              { required: true, message: '请输入产品大类!' }
            ]
          },
          matWeight: {
            rules: [
              { required: true, message: '请输入材料重量!' }
            ]
          },
          matNumber: {
            rules: [
              { required: true, message: '请输入材料数量!' }
            ]
          },
          warehouse: {
            rules: [
              { required: true, message: '请选择仓库!' }
            ]
          },
          productName: {
            rules: [
              { required: true, message: '请输入材料名称!' }
            ]
          },
          matLen: {
            rules: [
              { required: true, message: '请输入材料长度!' }
            ]
          },
          matThick: {
            rules: [
              { required: true, message: '请输入材料厚度!' }
            ]
          },
          matWidth: {
            rules: [
              { required: true, message: '请输入材料宽度!' }
            ]
          },
          matNo: {
            rules: [
              { required: true, message: '请输入材料号!' }
            ]
          }
        },
        url: {
          add: '/sto/enterHouse/add',
          edit: '/sto/enterHouse/edit',
          queryById: '/sto/enterHouse/queryById'
        }
      }
    },
    computed: {
      formDisabled() {
        if (this.formBpm === true) {
          if (this.formData.disabled === false) {
            return false
          }
          return true
        }
        return this.disabled
      },
      showFlowSubmitButton() {
        if (this.formBpm === true) {
          if (this.formData.disabled === false) {
            return true
          }
        }
        return false
      }
    },
    created() {
      //如果是流程中表单，则需要加载流程表单data
      this.showFlowData()
    },
    methods: {
      add() {
        this.edit({})
      },
      edit(record) {
        this.form.resetFields()
        this.model = Object.assign({}, record)
        this.visible = true
        this.$nextTick(() => {
          this.form.setFieldsValue(pick(this.model, 'createTime', 'productClass', 'productName', 'matLen', 'matThick', 'matWidth', 'matWeight', 'matNumber', 'matNo', 'warehouse'))
        })
      },
      //渲染流程表单数据
      showFlowData() {
        if (this.formBpm === true) {
          let params = { id: this.formData.dataId }
          getAction(this.url.queryById, params).then((res) => {
            if (res.success) {
              this.edit(res.result)
            }
          })
        }
      },
      submitForm() {
        const that = this
        // 触发表单验证
        this.form.validateFields((err, values) => {
          if (!err) {
            that.confirmLoading = true
            let httpurl = ''
            let method = ''
            if (!this.model.id) {
              httpurl += this.url.add
              method = 'post'
            } else {
              httpurl += this.url.edit
              method = 'put'
            }
            let formData = Object.assign(this.model, values)
            console.log('表单提交数据', formData)
            httpAction(httpurl, formData, method).then((res) => {
              if (res.success) {
                that.$message.success(res.message)
                that.$emit('ok')
              } else {
                that.$message.warning(res.message)
              }
            }).finally(() => {
              that.confirmLoading = false
            })
          }

        })
      },
      popupCallback(row) {
        this.form.setFieldsValue(pick(row, 'createTime', 'productClass', 'productName', 'matLen', 'matThick', 'matWidth', 'matWeight', 'matNumber', 'matNo', 'warehouse'))
      }
    }
  }
</script>