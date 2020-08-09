<template>
  <div>
    <el-table-column
        :align="align"
        :prop="prop"
        :header-align="headerAlign"
        :label="label"
        :width="width"
        :fixed="fixed"
        v-if="show">
      <template slot="header" slot-scope="scope">
        <el-popover class="popover"
                    :disabled="!renderType"
                    placement="bottom-start"
                    :ref="'popover'+scope.$index"
                    :width="renderWidth" trigger="click">
          <el-input v-show="renderType === 'input'"
                    v-model="value"
                    @change="handlerInput"
                    :placeholder="placeholder">

          </el-input>
          <el-select v-show="renderType === 'select'"
                     :isClear="isClear"
                     @change="handlerSelect"
                     v-model="value"
                     :placeholder="placeholder">
            <el-option :label="item.label"
                       :value="item.value"
                       :key="item.value"
                       v-for="item in selectList">

            </el-option></el-select>
          <el-date-picker v-show="renderType === 'date'"
                          v-model="value"
                          @change="handlerDate"
                          type="date"
                          :width="renderWidth"
                          :placeholder="placeholder">

          </el-date-picker>
          <el-date-picker v-show="renderType === 'datetime'"
                          v-model="dateTime"
                          @change="handlerDate"
                          type="daterange"
                          range-separator="至"
                          start-placeholder="起始日期"
                          end-placeholder="結束日期">
          </el-date-picker>
          <div slot="reference"
               :class="renderType?'popover-ref':''"
               :style="color">
            <span>{{label}}</span>
            <i v-show="renderType" :class="filterIcon"></i>
          </div>
        </el-popover>
      </template>
      <template slot-scope="scope">
        <slot :row="scope.row" :$index="scope.$index">
<!--          <div>&#45;&#45;{{scope.row}}</div>-->
<!--          <div>&#45;&#45;{{prop}}</div>-->
<!--          <div>&#45;&#45;{{scope.row[prop]}}</div>-->
<!--          <div>&#45;&#45;{{isMore}}</div>-->
          <el-tooltip :content="formatter(scope.row,scope.row[prop])" placement="top" v-show="isMore">
            <span class="ellipsis">{{scope.row[prop] | substring}}</span>
          </el-tooltip>
          <span class="ellipsis" v-show="!isMore">{{formatter(scope.row,scope.row[prop])}}</span>
        </slot>
      </template>
    </el-table-column>
  </div>
</template>

<script>
import moment from 'dayjs'

export default {
  name: "ElTableCoulumnPro2",
  props: {
    fixed: {
      type: String,
      default: null
    },
    prop: {
      type: String
    },
    label: {
      type: String
    },
    width: {
      type: Number
    },
    renderType: {
      type: String,
      validator: value => ['date', 'input', 'select', 'datetime'].includes(value)
    },
    placeholder: {
      type: String
    },
    renderWidth: {
      type: String,
      default: '350px'
    },
    param: {
      type: String,
      default: ''
    },
    startDate: {
      type: String
    },
    endDate: {
      type: String
    },
    selectList: {
      type: Array
    },
    isClear: {
      type: Boolean,
      default: true
    },
    visible: {
      type: Boolean,
      default: true
    },
    filterIcon: {
      type: String,
      default: 'el-icon-search'
    },
    callback: {
      type: Function
    },
    formatter: {
      type: Function,
      default: function (row,cellValue)  {
        return cellValue ? cellValue.toString() : '';
      }
    },
    align: {
      type: String,
      default:'center'
    },
    headerAlign: {
      type: String,
      default:'center'
    },
    page: {
      type: Number
    },
    isMore: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      dateTime: [],
      value: this.startDate || this.endDate ? [new Date(this.startDate), new Date(this.endDate)] : this.param,
      color: this.param || this.startDate || this.endDate ? 'color:#20a0ff' : '',
      show: this.visible
    }
  },
  methods: {
    toggle(visible) {
      this.show = visible
    },
    handlerInput(value) {
      this.$emit('update:param', value)
      this.$emit('update:page', 0)
      this.color = value ? 'color:#20a0ff' : ''
      this.callback()
    },
    handlerSelect(value) {
      this.$emit('update:param', value)
      this.$emit('update:page', 0)
      this.color = value ? 'color:#20a0ff' : ''
      this.callback()
    },
    handlerDate(value) {
      console.log(value);
      if (value) {
        if (this.renderType === 'date') {
          this.$emit('update:param', moment(value).format('YYYY-MM-DD'))
          this.$emit('update:page', 0)
          this.color = value ? 'color:#20a0ff' : ''
          this.callback()
        } else {
          this.color = value ? 'color:#20a0ff' : ''
          const startDate = moment(this.dateTime[0]).format('YYYY-MM-DD')
          const endDate = moment(this.dateTime[1]).format('YYYY-MM-DD')
          this.$emit('update:param', startDate + '~' + endDate)
          this.$emit('update:startDate', startDate)
          this.$emit('update:endDate', endDate)
          this.$emit('update:page', 0)
          this.callback()
        }
      } else {
        if (this.renderType === 'date') {
          this.$emit('update:param', value)
          this.$emit('update:page', 0)
          this.color = value ? 'color:#20a0ff' : ''
          this.callback()
        } else {
          this.color = value ? 'color:#20a0ff' : ''
          this.$emit('update:param', '')
          this.$emit('update:startDate', '')
          this.$emit('update:endDate', '')
          this.$emit('update:page', 0)
          this.callback()
        }
      }
    }
  },
  filters: {
    substring(value){
      if(value){
        if(value instanceof  String){
          return `${value.substring(0,10)}...`
        }
        return value
      }
      return ''
    }
  },
  watch: {
    'param'(val) {
      this.value = val
      this.color = val ? 'color:#20a0ff' : ''
    },
    'startDate'(val) {
      this.color = val ? 'color:#20a0ff' : ''
      this.dateTime[0] = new Date(val)
    },
    'endDate'(val) {
      this.color = val ? 'color:#20a0ff' : ''
      this.dateTime[1] = new Date(val)
    }
  },
}
</script>

<style lang="stylus" scoped>
.popover-ref {
  cursor: pointer;
  padding: 0;
}

.popover
  display flex
  justify-content center
  align-items center


</style>
