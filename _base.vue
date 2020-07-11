<!--
 * @Description: In User Settings Edit
 * @Author: your name
 * @Date: 2019-09-10 19:51:23
 * @LastEditTime: 2020-07-11 17:48:11
 * @LastEditors: Please set LastEditors


## props
1. formList
 {
          label: '选择平台用户',
          dom: 'input',
          prop: 'ptyh',
          placeholder: '请选择的选择平台用户',
          disabled: false,
          bind: {},
          listeners: {
            click() {
            }
          },
          render: (h, row) => {
            return (<div></div>)
          }
        }

2. layout: 'vertical' | 'horizontal'

3. formItemLayout item布局；


 -->
<template>
  <div>
    <a-form :form="form" :layout="layout" style="padding: 8px 18px;">
      <a-row :gutter="isDetails ? 0 : 24">
        <a-col
          v-for="(item, index) in formList"
          :key="index"
          :span="item.span ? item.span : 12"
          style="min-height: 0px;"
        >
          <a-form-item
            v-show="item.show !== false"
            :label="
              item.show !== false
                ? item.label && !(item.customLabel || isDetails)
                  ? item.label
                  : ''
                : ''
            "
            v-bind="{
              ...(item.formItemLayout
                ? item.formItemLayout
                : item.label
                ? formItemLayout
                : defaultFormItemLayout),
              ...formItemBind,
              ...item.formItemBind,
              ...{
                class: {
                  ...(formItemBind.class || {}),
                  ...((item.formItemBind || {}).class || {}),
                  'form-isDetails-form-item': isDetails
                }
              }
            }"
          >
            <div class="form-item-wrapper">
              <label
                v-if="item.label && (item.customLabel || isDetails)"
                :class="{
                  'form-isDetails-label': isDetails
                }"
                :style="{
                  width: labelWidth
                }"
                v-bind="{
                  ...(item.labelBind || {})
                }"
              >
                {{ item.label }}
                <span
                  v-if="
                    formItemBind &&
                      formItemBind.colon &&
                      item.formItemBind &&
                      item.formItemBind.colon !== false
                  "
                  >:</span
                >
              </label>
              <div>
                <a-input
                  v-if="item.dom === 'input'"
                  v-show="item.show !== false"
                  v-decorator="[
                    item.prop,
                    {
                      initialValue: formData[item.prop],
                      rules: rules[item.prop]
                    }
                  ]"
                  v-bind="item.bind"
                  :disabled="item.disabled"
                  :placeholder="item.placeholder"
                  @change="
                    (...args) => {
                      item.change && item.change(...args)
                    }
                  "
                  v-on="item.listeners || {}"
                >
                  <template
                    v-for="slotItem in item.slots || []"
                    v-slot:[slotItem]
                  >
                    <slot
                      :name="slotItem"
                      :scopedSlots="item.scopedSlots || {}"
                      :item="item"
                    ></slot>
                  </template>
                </a-input>
                <a-input-number
                  v-else-if="item.dom === 'input-number'"
                  v-show="item.show !== false"
                  v-decorator="[
                    item.prop,
                    {
                      initialValue: formData[item.prop],
                      rules: rules[item.prop]
                    }
                  ]"
                  v-bind="item.bind"
                  :disabled="item.disabled"
                  :placeholder="item.placeholder"
                  @change="
                    (...args) => {
                      item.change && item.change(...args)
                    }
                  "
                  v-on="item.listeners || {}"
                />

                <a-input-search
                  v-if="item.dom === 'input-search'"
                  v-show="item.show !== false"
                  v-decorator="[
                    item.prop,
                    {
                      initialValue: formData[item.prop],
                      rules: rules[item.prop]
                    }
                  ]"
                  v-bind="item.bind"
                  :disabled="item.disabled"
                  :placeholder="item.placeholder"
                  v-on="item.listeners || {}"
                >
                  <a-icon
                    v-if="formData[item.prop]"
                    slot="suffix"
                    type="close-circle"
                    @click="emitEmpty(item.prop)"
                  />
                  <template
                    v-for="slotItem in item.slots || []"
                    v-slot:[slotItem]
                  >
                    <slot
                      :name="slotItem"
                      :scopedSlots="item.scopedSlots || {}"
                      :item="item"
                    ></slot>
                  </template>
                </a-input-search>
                <a-textarea
                  v-else-if="item.dom === 'textarea'"
                  v-show="item.show !== false"
                  v-decorator="[
                    item.prop,
                    {
                      initialValue: formData[item.prop],
                      rules: rules[item.prop]
                    }
                  ]"
                  v-bind="item.bind"
                  :disabled="item.disabled"
                  :placeholder="item.placeholder"
                  v-on="item.listeners || {}"
                ></a-textarea>
                <a-button
                  v-else-if="item.dom === 'button'"
                  v-show="item.show !== false"
                  :key="item.key ? item.key : 'submit'"
                  :type="item.type ? item.type : 'primary'"
                  :disabled="item.disabled"
                  v-bind="item.bind"
                  @click="
                    () => {
                      item.click && item.click()
                    }
                  "
                  @change="
                    (...args) => {
                      item.change && item.change(...args)
                    }
                  "
                  v-on="item.listeners || {}"
                  >{{ item.text }}
                  <template
                    v-for="slotItem in item.slots || []"
                    v-slot:[slotItem]
                  >
                    <slot :name="slotItem" :item="item"></slot>
                  </template>
                </a-button>
                <a-select
                  v-else-if="item.dom === 'select'"
                  v-show="item.show !== false"
                  v-decorator="[
                    item.prop,
                    {
                      initialValue: formData[item.prop],
                      rules: rules[item.prop]
                    }
                  ]"
                  v-bind="item.bind"
                  :disabled="item.disabled"
                  :placeholder="item.placeholder"
                  v-on="item.listeners || {}"
                >
                  <a-select-option
                    v-if="!(item.bind || {}).emptySelect"
                    value=""
                  >
                    请选择
                  </a-select-option>
                  <a-select-option
                    v-for="(item2, key) in item.list || []"
                    :key="'option' + key"
                    :value="item2.value"
                    >{{ item2.label }}</a-select-option
                  >
                </a-select>

                <div
                  v-else-if="item.dom === 'input-select'"
                  v-show="item.show !== false"
                  class="input-select clearfix"
                >
                  <a-input
                    v-decorator="[
                      item.prop,
                      {
                        initialValue: formData[item.prop],
                        rules: rules[item.prop]
                      }
                    ]"
                    class="input-select-input"
                    v-bind="item.bind"
                    :placeholder="item.placeholder"
                    v-on="{
                      ...(item.listeners || {}),
                      change: value => handleInputSelectChange(value, item.prop)
                    }"
                  >
                    <template
                      v-for="slotItem in item.slots || []"
                      v-slot:[slotItem]
                    >
                      <slot
                        :name="slotItem"
                        :scopedSlots="item.scopedSlots || {}"
                        :item="item"
                      ></slot>
                    </template>
                  </a-input>
                  <a-select
                    v-decorator="[
                      item.prop + '_input_select',
                      {
                        initialValue: formData[item.prop],
                        rules: rules[item.prop]
                      }
                    ]"
                    style="width: 100%;"
                    class="input-select-select"
                    v-bind="item.bind"
                    :placeholder="item.placeholder"
                    v-on="{
                      ...(item.listeners || {}),
                      change: value => handleInputSelectChange(value, item.prop)
                    }"
                  >
                    <a-select-option
                      v-if="(item.bind || {}).emptySelect"
                      value=""
                    >
                      请选择
                    </a-select-option>
                    <a-select-option
                      v-for="(item2, key) in item.list || []"
                      :key="'option' + key"
                      :value="item2.value"
                      >{{ item2.label }}</a-select-option
                    >
                  </a-select>
                </div>
                <div
                  v-else-if="item.dom === 'transfer' && item.list"
                  v-show="item.show !== false"
                  class="transfer-wrapper"
                >
                  <a-transfer
                    :data-source="item.list"
                    :disabled="item.disabled"
                    v-bind="item.bind"
                    :target-keys="item.targetKeys"
                    :render="item.render"
                    @change="
                      (...args) => {
                        item.change && item.change(...args)
                      }
                    "
                  >
                  </a-transfer>
                </div>
                <span
                  v-else-if="item.dom === 'checkbox' && item.show !== false"
                >
                  <a-checkbox-group
                    :options="item.list || []"
                    :value="formData[item.prop] || []"
                    v-bind="item.bind"
                  />
                </span>
                <div v-else-if="item.render">
                  <expand
                    :row="item"
                    :index="index"
                    :render="item.render"
                  ></expand>
                </div>
                <div v-else-if="item.dom === 'slot' && item.show !== false">
                  <template v-for="slotItem in item.slots || []">
                    <slot :name="slotItem" :item="item"></slot>
                  </template>
                </div>
                <div
                  v-else-if="item.dom === 'text'"
                  v-show="item.show !== false"
                  :class="{
                    'form-isDetails-text': isDetails
                  }"
                >
                  <span v-if="item.list && item.list.length">
                    {{
                      (
                        item.list.find(
                          listItem => listItem.value == formData[item.prop]
                        ) || {}
                      ).label || ''
                    }}
                  </span>
                  <span v-else :key="item.prop">
                    {{ formData[item.prop] }}
                  </span>
                  <a-input
                    v-decorator="[
                      item.prop,
                      {
                        initialValue: formData[item.prop],
                        rules: rules[item.prop]
                      }
                    ]"
                    style="display: none;"
                  ></a-input>
                </div>
                <div
                  v-else-if="item.dom === 'radio'"
                  v-show="item.show !== false"
                >
                  <a-radio-group
                    v-decorator="[
                      item.prop,
                      {
                        initialValue: formData[item.prop],
                        rules: rules[item.prop] || []
                      }
                    ]"
                    :options="item.list || []"
                    v-bind="item.bind"
                    v-on="item.listeners || {}"
                    @change="item.radioChange"
                  >
                  </a-radio-group>
                </div>
                <div
                  v-else-if="item.dom === 'areaPicker'"
                  v-show="item.show !== false"
                >
                  <a-cascader
                    v-decorator="[
                      item.prop,
                      {
                        initialValue: formData[item.prop] || [],
                        rules: rules[item.prop] || []
                      }
                    ]"
                    :options="item.list || areaJson"
                    v-bind="item.bind"
                    style="width: 100%"
                    v-on="item.listeners || {}"
                  >
                  </a-cascader>
                </div>
              </div>
            </div>
          </a-form-item>
        </a-col>
      </a-row>
    </a-form>
  </div>
</template>
<script>
import { mapState, mapActions } from 'vuex'
import Expand from './expand.js'
// import areaJson from '@/assets/data/area.json'
export default {
  name: 'CommonPageForm',
  components: { expand: Expand },
  props: {
    layout: {
      type: String,
      default: 'horizontal'
    },
    labelWidth: {
      type: String,
      default: '80px'
    },
    formItemLayout: {
      type: Object,
      default: () => {
        return {
          labelCol: {
            xs: { span: 24 },
            sm: { span: 6 }
          },
          wrapperCol: {
            xs: { span: 24 },
            sm: { span: 18 }
          }
        }
      }
    },
    defaultFormItemLayout: {
      type: Object,
      default: () => {
        return {
          labelCol: {
            xs: { span: 0 },
            sm: { span: 0 }
          },
          wrapperCol: {
            xs: { span: 24 },
            sm: { span: 24 }
          }
        }
      }
    },
    formItemBind: {
      type: Object,
      default: () => {
        return {
          colon: true
        }
      }
    },
    rules: {
      type: Object,
      default: () => {
        return {}
      }
    },
    formList: {
      type: Array,
      default: () => {
        return []
      }
    },
    formData: {
      type: Object,
      default: () => {
        return {}
      }
    },
    value: {
      type: Object,
      default: () => {
        return {}
      }
    },
    isDetails: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      form: this.$form.createForm(this, {
        onValuesChange: (_, values) => {
          this.$emit('input', values)
        }
      })
    }
  },
  computed: {
    ...mapState('index', [])
  },
  created() {},
  mounted() {},
  methods: {
    ...mapActions('index', []),
    handleOk(callback) {
      this.form.validateFields((err, values) => {
        callback && callback(err, values)
      })
    },
    handleAreaClick(e, label, option) {
      e.stopPropagation()
      console.log('clicked', label, option)
    },
    emitEmpty(prop) {
      if (!prop) return false
      this.form.setFieldsValue({
        [prop]: ''
      })
    },
    reset() {
      this.form.resetFields()
    },
    setFieldsValue(obj) {
      if (!obj) return false
      this.form.setFieldsValue(obj)
    },
    getFieldsValue(obj) {
      return this.form.getFieldsValue(obj)
    },
    handleInputSelectChange(value, prop) {
      this.setFieldsValue({
        [prop]: value
      })
    }
  }
}
</script>
<style scoped>
.form-item-wrapper {
  display: flex;
}
.form-item-wrapper > label {
  padding-right: 16px;
}
.form-item-wrapper > div {
  flex: 1;
}
.form-isDetails-label {
  background: #f9f9f9;
  border: 1px solid #e8e8e8;
  padding-left: 16px;
  min-height: 100%;
  line-height: 16px;
  padding-top: 16px;
  padding-bottom: 16px;
}
.form-isDetails-text {
  background: #ffffff;
  border: 1px solid #e8e8e8;
  padding-left: 16px;
  border-left: none;
  min-height: 100%;
  line-height: 16px;
  padding-top: 16px;
  padding-bottom: 16px;
  padding-right: 16px;
}
.form-isDetails-form-item {
  margin-bottom: 0;
}

.input-select {
  position: relative;
}
input.input-select-input {
  position: absolute;
  z-index: 2;
  left: 0;
  width: calc(100% - 40px);
}
div.input-select-select {
  position: absolute;
  top: 0;
  left: 0;
}
.select-color-btn {
  width: 14%;
  display: inline-block;
  height: 80rpx;
  float: right;
  z-index: 3;
  position: relative;
}
.clearfix:after {
  display: block;
  clear: both;
  content: '';
  visibility: hidden;
  height: 0;
}
.clearfix {
  zoom: 1;
}
</style>
