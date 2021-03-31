<template>
  <a-card :bordered="false">
    <!-- 查询区域 -->
    <div class="table-page-search-wrapper">
      <a-form layout="inline" @keyup.enter.native="searchQuery">
        <a-row :gutter="24">
          <a-col :xl="10" :lg="11" :md="12" :sm="24">
            <a-form-item label="创建日期">
              <j-date :show-time="true" date-format="YYYY-MM-DD HH:mm:ss" placeholder="请选择开始时间" class="query-group-cust" v-model="queryParam.createTime_begin"></j-date>
              <span class="query-group-split-cust"></span>
              <j-date :show-time="true" date-format="YYYY-MM-DD HH:mm:ss" placeholder="请选择结束时间" class="query-group-cust" v-model="queryParam.createTime_end"></j-date>
            </a-form-item>
          </a-col>
          <a-col :xl="6" :lg="7" :md="8" :sm="24">
            <a-form-item label="物料种类">
              <j-dict-select-tag placeholder="请选择物料种类"  dictCode="mat_type" v-model="queryParam.matType" />
            </a-form-item>
          </a-col>
          <template v-if="toggleSearchStatus">
            <a-col :xl="6" :lg="7" :md="8" :sm="24">
              <a-form-item label="材料名称">
                <a-input placeholder="请输入材料名称" v-model="queryParam.matName"></a-input>
              </a-form-item>
            </a-col>
            <a-col :xl="6" :lg="7" :md="8" :sm="24">
              <a-form-item label="材料长度">
                <a-input placeholder="请输入材料长度" v-model="queryParam.matLen"></a-input>
              </a-form-item>
            </a-col>
            <a-col :xl="6" :lg="7" :md="8" :sm="24">
              <a-form-item label="材料厚度">
                <a-input placeholder="请输入材料厚度" v-model="queryParam.matThick"></a-input>
              </a-form-item>
            </a-col>
            <a-col :xl="6" :lg="7" :md="8" :sm="24">
              <a-form-item label="材料宽度">
                <a-input placeholder="请输入材料宽度" v-model="queryParam.matWidth"></a-input>
              </a-form-item>
            </a-col>
            <a-col :xl="6" :lg="7" :md="8" :sm="24">
              <a-form-item label="材料号">
                <a-input placeholder="请输入材料号" v-model="queryParam.matNo"></a-input>
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
      <a-button v-has="'people:button'" @click="handleAdd" type="primary" icon="plus">新增</a-button>
      <a-button v-has="'people:button'" type="primary" icon="download" @click="handleExportXls('材料入库')">导出</a-button>
      <a-upload v-has="'people:button'" name="file" :showUploadList="false" :multiple="false" :headers="tokenHeader" :action="importExcelUrl" @change="handleImportExcel">
        <a-button type="primary" icon="import">导入</a-button>
      </a-upload>
      <!-- 高级查询区域 -->
      <j-super-query :fieldList="superFieldList" ref="superQueryModal" @handleSuperQuery="handleSuperQuery"></j-super-query>
      <a-dropdown v-if="selectedRowKeys.length > 0">
        <a-menu slot="overlay">
          <a-menu-item key="1" @click="batchDel"><a-icon type="delete"/>删除</a-menu-item>
        </a-menu>
        <a-button v-has="'people:button'" style="margin-left: 8px"> 批量操作 <a-icon type="down" /></a-button>
      </a-dropdown>
    </div>

    <!-- table区域-begin -->
    <div>
      <div class="ant-alert ant-alert-info" style="margin-bottom: 16px;">
        <i class="anticon anticon-info-circle ant-alert-icon"></i> 已选择 <a style="font-weight: 600">{{ selectedRowKeys.length }}</a>项
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
          <img v-else :src="getImgView(text)" height="25px" alt="" style="max-width:80px;font-size: 12px;font-style: italic;"/>
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
          <!--<a @click="handleEdit(record)">编辑</a>-->
          <a @click="handleDetail(record)">详情</a>

          <a-divider type="vertical" />
          <a v-has="'people:button'" @click="handleEdit(record)">编辑</a>
<!--
          <a-dropdown>
            <a class="ant-dropdown-link">更多 <a-icon type="down" /></a>
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
-->
        </span>

      </a-table>
    </div>

    <enter-house-modal ref="modalForm" @ok="modalFormOk"></enter-house-modal>
  </a-card>
</template>

<script>

  import '@/assets/less/TableExpand.less'
  import { mixinDevice } from '@/utils/mixin'
  import { JeecgListMixin } from '@/mixins/JeecgListMixin'
  import EnterHouseModal from './modules/EnterHouseModal'
  import JDate from '@/components/jeecg/JDate.vue'
  import JSuperQuery from '@/components/jeecg/JSuperQuery.vue'
  import JDictSelectTag from '@/components/dict/JDictSelectTag.vue'

  export default {
    name: 'EnterHouseList',
    mixins:[JeecgListMixin, mixinDevice],
    components: {
      JDate,
      EnterHouseModal,
      JSuperQuery,
      JDictSelectTag
    },
    data () {
      return {
        description: '材料入库管理页面',
        // 表头
        columns: [
          {
            title: '#',
            dataIndex: '',
            key:'rowIndex',
            width:60,
            align:"center",
            customRender:function (t,r,index) {
              return parseInt(index)+1;
            }
          },
          {
            title:'创建日期',
            align:"center",
            dataIndex: 'createTime'
          },
          {
            title:'物料种类',
            align:"center",
            dataIndex: 'matType_dictText'
          },
          {
            title:'材料名称',
            align:"center",
            dataIndex: 'matName'
          },
          {
            title:'材料长度',
            align:"center",
            dataIndex: 'matLen'
          },
          {
            title:'材料厚度',
            align:"center",
            dataIndex: 'matThick'
          },
          {
            title:'材料宽度',
            align:"center",
            dataIndex: 'matWidth'
          },
          {
            title:'材料重量',
            align:"center",
            dataIndex: 'matWeight'
          },
          {
            title:'材料号',
            align:"center",
            dataIndex: 'matNo'
          },
          {
            title:'单价',
            align:"center",
            dataIndex: 'price'
          },
          {
            title:'材料总件数',
            align:"center",
            dataIndex: 'matNumber'
          },
          {
            title:'材料总重量',
            align:"center",
            dataIndex: 'totalWeight'
          },
          {
            title:'仓库',
            align:"center",
            dataIndex: 'warehouse_dictText'
          },
          {
            title: '操作',
            dataIndex: 'action',
            align:"center",
            fixed:"right",
            width:147,
            scopedSlots: { customRender: 'action' }
          }
        ],
        url: {
          list: "/sto/enterHouse/list",
          delete: "/sto/enterHouse/delete",
          deleteBatch: "/sto/enterHouse/deleteBatch",
          exportXlsUrl: "/sto/enterHouse/exportXls",
          importExcelUrl: "sto/enterHouse/importExcel",
          
        },
        dictOptions:{},
        superFieldList:[],
      }
    },
    created() {
    this.getSuperFieldList();
    },
    computed: {
      importExcelUrl: function(){
        return `${window._CONFIG['domianURL']}/${this.url.importExcelUrl}`;
      },
    },
    methods: {
      initDictConfig(){
      },
      getSuperFieldList(){
        let fieldList=[];
        fieldList.push({type:'datetime',value:'createTime',text:'创建日期'})
        fieldList.push({type:'popup',value:'matType',text:'物料种类', popup:{code:'sto_mater_info',field:'mat_name',orgFields:'mat_name',destFields:'mat_name'}})
        fieldList.push({type:'string',value:'matName',text:'材料名称',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'matLen',text:'材料长度',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'matThick',text:'材料厚度',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'matWidth',text:'材料宽度',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'matWeight',text:'材料总重量',dictCode:''})
        fieldList.push({type:'BigDecimal',value:'price',text:'单价',dictCode:''})
        fieldList.push({type:'int',value:'matNumber',text:'材料总件数',dictCode:''})
         fieldList.push({type:'BigDecimal',value:'totalWeight',text:'材料总重量',dictCode:''})
        fieldList.push({type:'string',value:'matNo',text:'材料号',dictCode:''})
        fieldList.push({type:'string',value:'warehouse',text:'仓库',dictCode:''})
        this.superFieldList = fieldList
      }
    }
  }
</script>
<style scoped>
  @import '~@assets/less/common.less';
</style>