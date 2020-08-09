<template>
  <div id="app">
    <el-table :data="bankFlow" style="width:100%" stripe>
      <my-el-table-column-pro :visible="showMore"  prop="transactionId" label="編號" :width="120"> </my-el-table-column-pro>
      <my-el-table-column-pro  prop="clientCode" label="客戶編號 " :width="120" placeholder="請輸入客戶編號" :callback="requestTransactionLogs"  renderType="input" :param.sync="request_params.clientCode"> </my-el-table-column-pro>
      <my-el-table-column-pro  prop="eventTypeName" label="事件 " placeholder="請選擇事件"   :selectList="listEventEnum" :callback="requestTransactionLogs"  renderType="select" :param.sync="request_params.event" :width="100">  </my-el-table-column-pro>
      <my-el-table-column-pro  prop="createDateTime" :callback="requestTransactionLogs" :startDate.sync="request_params.startDate" :endDate.sync="request_params.endDate"  label="日期" renderType="datetime" :width="180" :isMore="false" ></my-el-table-column-pro>
    </el-table>
  </div>
</template>

<script>
import MyElTableColumnPro from "@/components/MyElTableColumn";

export default {
  name: 'App',
  components: {
    MyElTableColumnPro
  },
  data(){
    return {
      bankFlow: [
        {transactionId: 1, clientCode: '001', eventTypeName: 'event-1', createDateTime:'2020-07-01'},
        {transactionId: 2, clientCode: '002', eventTypeName: 'event-2', createDateTime:'2020-07-02'},
        {transactionId: 3, clientCode: '003', eventTypeName: 'event-3', createDateTime:'2020-07-03'}
      ],
      showMore: true,
      request_params:{
        clientCode: '',
        event: '',
        startDate: '',
        endDate: '',
      },
      listEventEnum:[
        { label: 'event-1', value: 'ev-1'},
        { label: 'event-2', value: 'ev-2'},
        { label: 'event-3', value: 'ev-3'}
      ]
    };
  },
  methods:{
    requestTransactionLogs(){
      console.log("event - requestTransactionLogs ");
    },
    getLists(){
      console.log("event - getLists")
    },
    dateTimeFormat(){
      console.log("event - dateTime Format")
    }

  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
