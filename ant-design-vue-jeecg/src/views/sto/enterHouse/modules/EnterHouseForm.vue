<template>
  <a-spin :spinning="confirmLoading">
    <j-form-container :disabled="formDisabled">
      <a-form :form="form" slot="detail">
        <a-row>
          <a-col :span="12">
            <a-form-item label="材料名称" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-popup
                v-decorator="['matName', validatorRules.matName]"
                :trigger-change="true"
                org-fields="mat_name,mat_type,mat_len,mat_width,mat_thick,mat_no,price,mat_number,mat_weight"
                dest-fields="matName,matType,matLen,matWidth,matThick,matNo,price,matNumber,matWeight"
                code="sto_mater_info"
                @callback="popupCallback"
              />
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="仓库" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag v-decorator="['warehouse', validatorRules.warehouse]" :trigger-change="true"
                                 dictCode="sto_warehouse,house_name,id" placeholder="请输入仓库"></j-dict-select-tag>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="物料种类" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <j-dict-select-tag disabled type="list" v-decorator="['matType', validatorRules.matType]"
                                 :trigger-change="true" dictCode="mat_type" placeholder="请选择物料种类"/>
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
            <a-form-item label="材料件数" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number disabled v-decorator="['matNumber', validatorRules.matNumber]" placeholder="请输入材料件数" style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="材料重量" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number disabled  v-decorator="['matWeight', validatorRules.matWeight]" placeholder="请输入材料重量" style="width: 100%"/>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="材料号" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input disabled v-decorator="['matNo', validatorRules.matNo]" placeholder="请输入材料号"></a-input>
            </a-form-item>
          </a-col>
          <a-col :span="12">
            <a-form-item label="单价" :labelCol="labelCol" :wrapperCol="wrapperCol">
              <a-input-number disabled v-decorator="['price', validatorRules.price]" placeholder="请输入单价" style="width: 100%"/>
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
          matType: {
            rules: [
              { required: true, message: '请输入物料种类!' }
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
          price: {
            rules: [
              { required: true, message: '请输入单价!' }
            ]
          },
          warehouse: {
            rules: [
              { required: true, message: '请选择仓库!' }
            ]
          },
          matName: {
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
          this.form.setFieldsValue(pick(this.model, 'createTime', 'matType', 'matName', 'matLen', 'matThick', 'matWidth', 'matWeight', 'matNumber', 'matNo', 'warehouse','price'))
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
        this.form.setFieldsValue(pick(row, 'createTime', 'matType', 'matName', 'matLen', 'matThick', 'matWidth', 'matWeight', 'matNumber', 'matNo', 'warehouse','price'))
      }
    }
  }
</script>