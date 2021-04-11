<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="客户">
              <j-search-select-tag placeholder="请选择客户" v-model="queryParam.customer"
                                   dict="man_customer where del_flag = 0,customer_name,id"/>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="提单编号">
              <j-input placeholder="请输入提单编号" v-model="queryParam.billNo"/>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="退款类型">
              <j-dict-select-tag placeholder="请选择退款类型" v-model="queryParam.refundType" dictCode="refund_type"/>
            </a-form-item>
          </a-col>
          <template v-if="toggleSearchStatus">
            <a-col :xl="6" :lg="7" :md="8" :sm="24">
              <a-form-item label="退货确认">
                <j-dict-select-tag placeholder="请选择退货确认" v-model="queryParam.refundAgree" dictCode="refund_agree"/>
              </a-form-item>
            </a-col>
            <a-col :xl="6" :lg="7" :md="8" :sm="24">
              <a-form-item label="退款审核">
                <j-dict-select-tag placeholder="请选择退款审核" v-model="queryParam.refundStatus" dictCode="refund_check"/>
              </a-form-item>
            </a-col>
          </template>
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <span style="float: left;overflow: hidden;" class="table-page-search-submitButtons">
              <a-button type="primary" @click="searchQuery" icon="search">查询</a-button>
              <a-button type="primary" @click="searchReset" icon="reload" style="margin-left: 8px">重置</a-button>
              <a @click="handleToggleSearch" style="margin-left: 8px">
                {{ toggleSearchStatus ? '收起' : '展开' }}
                <a-icon :type="toggleSearchStatus ? 'up' : 'down'"/>
              </a>
            </span>
          </a-col>
        </a-row>
      </a-form>
    </div>
    <!-- 查询区域-END -->

    <!-- 操作按钮区域 -->
    <div class="table-operator">
      <a-button v-has="'cash:check'" @click="refundGoods" type="primary">退货确认</a-button>
      <a-button v-has="'cash:check'" @click="refund" type="primary">退款审核</a-button>
      <a-button v-has="'people:button'" type="primary" icon="download" @click="handleExportXls('退款管理')">导出</a-button>
      <!--
            <a-upload name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl"
                      @change="handleImportExcel">
              <a-button type="primary" icon="import">导入</a-button>
            </a-upload>
      -->
      <!-- 高级查询区域 -->
      <j-super-query :fieldList="superFieldList" ref="superQueryModal"
                     @handleSuperQuery="handleSuperQuery"></j-super-query>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel">
            <a-icon type="delete"/>
            删除
          </a-menu-item>
        </a-menu>
        <a-button v-has="'people:button'" style="margin-left: 8px"> 批量操作
          <a-icon type="down"/>
        </a-button>
      </a-dropdown>
    </div>

    <div>
      <a-modal  v-model="refundView" title="退款审核" :width="900" @ok="refundOk">
        <div>
          <span>审核结果：</span>
          <a-radio-group default-value="pass" button-style="solid"  @change="handleChange">
            <a-radio-button  value="pass">
              通 过
            </a-radio-button>
            <a-radio-button value="unpass">
              不通过
            </a-radio-button>

          </a-radio-group>
        </div>
        <br/>
        <a-table
          ref="tableRefund"
          bordered
          rowKey="id"
          :columns="refundColumns"
          :dataSource="selectionRows"
          :loading="loading"
        >
        </a-table>

      </a-modal>
    </div>
    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{
        selectedRowKeys.length }}</a>项
        <a style="margin-left: 24px" @click="onClearSelected">清空</a>
      </div>


      <a-table
        ref="table"
        size="middle"
        :scroll="{x:true}"
        bordered
        rowKey="id"
        :columns="columns"
        :dataSource="dataSource"
        :pagination="ipagination"
        :loading="loading"
        :rowSelection="{selectedRowKeys: selectedRowKeys, onChange: onSelectChange}"
        class="j-table-force-nowrap"
        @change="handleTableChange">

        <template slot="htmlSlot" slot-scope="text">
          <div v-html="text"></div>
        </template>
        <template slot="imgSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无图片</span>
          <img v-else :src="getImgView(text)" height="25px" alt=""
               style="max-width:80px;font-size: 12px;font-style: italic;"/>
        </template>
        <template slot="fileSlot" slot-scope="text">
          <span v-if="!text" style="font-size: 12px;font-style: italic;">无文件</span>
          <a-button
            v-else
            :ghost="true"
            type="primary"
            icon="download"
            size="small"
            @click="downloadFile(text)">
            下载
          </a-button>
        </template>

        <span slot="action" slot-scope="text, record">
          <a v-has="'people:button'" @click="handleEdit(record)">编辑</a>

          <a-divider type="vertical"/>
          <a-dropdown>
            <a v-has="'people:button'" class="ant-dropdown-link">更多 <a-icon type="down"/></a>
            <a-menu slot="overlay">
              <a-menu-item>
                <a @click="handleDetail(record)">详情</a>
              </a-menu-item>
              <a-menu-item>
                <a-popconfirm title="确定删除吗?" @confirm="() => handleDelete(record.id)">
                  <a>删除</a>
                </a-popconfirm>
              </a-menu-item>
            </a-menu>
          </a-dropdown>
        </span>

      </a-table>
    </div>

    <refund-modal ref="modalForm" @ok="modalFormOk"></refund-modal>
  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import RefundModal from './modules/RefundModal'
  import JDictSelectTag from '@/components/dict/JDictSelectTag.vue'
  import { filterMultiDictText } from '@/components/dict/JDictSelectUtil'
  import JSearchSelectTag from '@/components/dict/JSearchSelectTag'
  import JSuperQuery from '@/components/jeecg/JSuperQuery.vue'
  import { httpAction, getAction } from '@/api/manage'

  export default {
    name: 'RefundList',
    mixins: [JeecgListMixin, mixinDevice],
    components: {
      JDictSelectTag,
      JSearchSelectTag,
      RefundModal,
      JSuperQuery
    },
    data() {
      return {
        description: '退款管理管理页面',
        refundView: false,
        selectData: 'pass',
        labelCol: {
          xs: { span: 24 },
          sm: { span: 5 }
        },
        wrapperCol: {
          xs: { span: 24 },
          sm: { span: 16 }
        },
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key: 'rowIndex',
            width: 60,
            align: 'center',
            customRender: function(t, r, index) {
              return parseInt(index) + 1
            }
          },
          {
            title: '创建日期',
            align: 'center',
            dataIndex: 'createTime'
          },
          {
            title: '退款编号',
            align: 'center',
            dataIndex: 'refundNo'
          },
          {
            title: '客户',
            align: 'center',
            dataIndex: 'customer_dictText'
          },
          {
            title: '退款金额',
            align: 'center',
            dataIndex: 'refundMoney'
          },
          {
            title: '提单编号',
            align: 'center',
            dataIndex: 'billNo'
          },
          {
            title: '退款类型',
            align: 'center',
            dataIndex: 'refundType_dictText'
          },
          {
            title: '退货确认',
            align: 'center',
            dataIndex: 'refundAgree_dictText'
          },
          {
            title: '退款审核',
            align: 'center',
            dataIndex: 'refundStatus_dictText'
          },
          {
            title: '审核人',
            align: 'center',
            dataIndex: 'checker'
          },
          {
            title: '退款时间',
            align: 'center',
            dataIndex: 'refundDate'
          },
          {
            title: '备注',
            align: 'center',
            dataIndex: 'remaks'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align: 'center',
            fixed: 'right',
            width: 147,
            scopedSlots: { customRender: 'action' }
          }
        ],
        refundColumns: [
          {
            title: '创建日期',
            align: 'center',
            dataIndex: 'createTime'
          },
          {
            title: '客户',
            align: 'center',
            dataIndex: 'customer_dictText'
          },
          {
            title: '退款金额',
            align: 'center',
            dataIndex: 'refundMoney'
          },
          {
            title: '退款类型',
            align: 'center',
            dataIndex: 'refundType_dictText'
          },
          {
            title: '退货确认',
            align: 'center',
            dataIndex: 'refundAgree_dictText'
          },
          {
            title: '退款审核',
            align: 'center',
            dataIndex: 'refundStatus_dictText'
          }

        ],
        url: {
          list: '/cash/refund/list',
          delete: '/cash/refund/delete',
          deleteBatch: '/cash/refund/deleteBatch',
          exportXlsUrl: '/cash/refund/exportXls',
          importExcelUrl: 'cash/refund/importExcel',
          refundGoods: '/sto/enterHouse/refundGoods',
          refundVerify: '/cash/refund/refundVerify',

        },
        dictOptions: {},
        superFieldList: []
      }
    },
    created() {
      this.getSuperFieldList()
    },
    computed: {
      importExcelUrl: function() {
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`
      }
    },
    methods: {
      initDictConfig() {
      },
      handleChange(e) {
        this.selectData = e.target.value
        console.log('selectData', this.selectData)
      },
      refund() {
        this.refundView = true
      },
      refundOk() {
        console.log('selectData', this.selectData)
        var len = this.selectedRowKeys.length
        var ids = ''
        for (let i = 0; i < len; i++) {
          ids += this.selectedRowKeys[i] + ','
        }
        ids = ids.substring(0, ids.length - 1)
        var refundObj = { id: ids ,status:this.selectData}
        getAction(this.url.refundVerify, refundObj).then((res) => {
          if (res.success) {
            this.$message.success(res.message)
            this.refundView = false
            this.loadData()
          } else {
            this.$message.error(res.message)
          }

        })

      },
      refundGoods() {
        var len = this.selectedRowKeys.length
        if (len === 0) {
          this.$message.warning('请选择至少一条记录！')
        } else {
          var ids = ''
          for (let i = 0; i < len; i++) {
            ids += this.selectedRowKeys[i] + ','
          }
          ids = ids.substring(0, ids.length - 1)
          var idObj = { id: ids }
          getAction(this.url.refundGoods, idObj).then((res) => {
            if (res.success) {
              this.$message.success(res.message)
            } else {
              this.$message.error(res.message)
            }
            this.loadData()
          })
        }

      },
      getSuperFieldList() {
        let fieldList = []
        fieldList.push({ type: 'datetime', value: 'createTime', text: '创建日期' })
        fieldList.push({ type: 'string', value: 'refundNo', text: '退款编号', dictCode: '' })
        fieldList.push({
          type: 'sel_search',
          value: 'customer',
          text: '客户',
          dictTable: 'man_customer',
          dictText: 'customer_name',
          dictCode: 'id'
        })
        fieldList.push({ type: 'BigDecimal', value: 'refundMoney', text: '退款金额', dictCode: '' })
        fieldList.push({ type: 'string', value: 'billNo', text: '提单编号', dictCode: '' })
        fieldList.push({ type: 'string', value: 'refundType', text: '退款类型', dictCode: 'refund_type' })
        fieldList.push({ type: 'string', value: 'refundAgree', text: '退货确认', dictCode: 'refund_agree' })
        fieldList.push({ type: 'string', value: 'refundStatus', text: '退款审核', dictCode: 'refund_check' })
        fieldList.push({ type: 'string', value: 'checker', text: '审核人', dictCode: '' })
        fieldList.push({ type: 'datetime', value: 'refundDate', text: '退款时间' })
        fieldList.push({ type: 'string', value: 'remaks', text: '备注', dictCode: '' })
        this.superFieldList = fieldList
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>