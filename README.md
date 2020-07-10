<!--
 * @Author: your name
 * @Date: 2020-07-10 21:56:46
 * @LastEditTime: 2020-07-10 22:03:54
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \pageForm\README.md
--> 
使用方法：

1. 
示例：
```

<!--
 * @Description: In User Settings Edit
 * @Author:
 your name
 * @Date: 2019-09-23 18:18:22
 * @LastEditTime: 2020-03-08 14:11:39
 * @LastEditors: Please set LastEditors
 -->
<template>
  <div>
    <CommonPageForm
      ref="CommonPageForm"
      :title="formData.id ? '编辑资源' : '新增资源'"
      :visible="true"
      :form-list="formList"
      :form-data="formData"
      :rules="rules"
    >
      <template v-slot:addonAfter="scope">
        <span class="pointer" @click="handleSelectMenu(scope)">选择</span>
      </template>
    </CommonPageForm>
  </div>
</template>
<script>
import { mapActions, mapState } from 'vuex'
export default {
  components: {},
  props: {
    formData: {
      type: Object,
      default: () => {
        return {}
      }
    }
  },
  data() {
    return {
      rules: {
        resourceGroupId: [{ required: true, message: '请选择导入分组' }],
        resourceName: [{ required: true, message: '请填写资源名称' }]
        // reqType: [{ required: true, message: '请选择请求方式' }]
      }
    }
  },
  computed: {
    ...mapState('index', [
      'curResourceType',
      'curApp',
      'resurceGroups',
      'curResourceType',
      'formLength',
      'reqTypes'
    ]),
    formList() {
      return [
        {
          label: '菜单名称',
          dom: 'input',
          prop: 'menuName',
          placeholder: '请选择菜单名称',
          bind: {
            disabled: true,
          },
          span: 24,
          slots: ['addonAfter']
        },
        {
          label: '选择导入分组',
          dom: 'select',
          prop: 'resourceGroupId',
          placeholder: '请选择导入分组',
          list: this.resurceGroups,
          bind: {
            labelInValue: true,
            emptySelect: true
          },
          listeners: {
            change: value => {
              // this.handleResourceTypeChange(value)
            }
          },
          span: 24
        },
        {
          label: '资源名称',
          dom: 'input',
          prop: 'resourceName',
          placeholder: '请填写资源名称',
          bind: {
            disabled: false,
            maxLength: this.formLength.resource.name
          },
          span: 24
        },
        {
          label: '资源编码',
          dom: 'input',
          prop: 'resourceCode',
          placeholder: '请填写资源编码',
          bind: {
            maxLength: this.formLength.resource.code
          },
          span: 24
        },
        {
          label: '资源路径',
          dom: 'textarea',
          prop: 'resourceUrl',
          placeholder: '资源路径',
          span: 24,
          bind: {
            maxLength: this.formLength.resource.url
          }
        },
        {
          label: '请求方式',
          dom: 'select',
          prop: 'reqType',
          placeholder: '请选择请求方式',
          list: this.reqTypes,
          bind: {
            labelInValue: true,
            emptySelect: false
          },
          listeners: {
            change: value => {
              // this.handleResourceTypeChange(value)
            }
          },
          span: 24
        },
        {
          label: '物流付款方式',
          dom: 'select',
          prop: 'wlPayMethod',
          placeholder: '请选择物流付款方式',
          render: (h, params) => {
            const label =
              (
                payMethods.find(
                  item => item.value === this.sFormData.wlPayMethod
                ) || {}
              ).label || ''
            return h('span', {
              domProps: {
                innerHTML: label
              }
            })
          },
        {
          label: '物流付款方式2',
          dom: 'select',
          prop: 'wlPayMethod2',
          render: (h, params) => {
            const { row, index } = params
            const btnArr = []
            return h(
              'a-row',
              {
                attrs: {
                  'data-json': JSON.stringify(row),
                  'data-index': index,
                  gutter: 20
                },
                on: {}
              },
              [
                h(
                  'a-col',
                  {
                    attrs: {
                      span: 24
                    },
                    domProps: {}
                  },
                  [
                    h(
                      'a-checkbox',
                      {
                        attrs: {
                          indeterminate: that.indeterminate,
                          checked: that.checkAll
                        },
                        on: {
                          click: that.onCheckAllChange
                        }
                      },
                      [
                        h('span', {
                          attrs: {},
                          domProps: {
                            innerHTML: '全选'
                          }
                        })
                      ]
                    )
                  ]
                ),
                h(
                  'a-col',
                  {
                    attrs: {
                      span: 24
                    }
                  },
                  [
                    h('a-checkbox-group', {
                      attrs: {
                        options: that.plainOptions
                      },
                      props: {
                        value: that.checkedList
                      },
                      on: {
                        change: that.onChange
                      }
                    })
                  ]
                ),
                ...btnArr
              ]
            )
          }
        }
        },
      ]
    }
  },
  created() {
    this.getData()
  },
  methods: {
    ...mapActions('index', ['addResourceList', 'getResourceGroups']),
    getData() {
      this.getResourceGroups({
        query: this.otoHump(
          {
            page: 1,
            size: 990000,
            appCode: this.curApp.appCode,
            resourceType: this.curResourceType
          },
          false
        )
      }).then(res => {
        if (res[this.errcodeName] === 0 && res.total > 990000) {
          this.getResourceGroups({
            query: this.otoHump(
              {
                page: 1,
                size: res.total,
                appCode: this.curApp.appCode
              },
              false
            )
          })
        }
      })
    },
    handleOk(values) {
      this.$refs.CommonPageForm &&
        this.$refs.CommonPageForm.handleOk((err, values) => {
          if (!err) {
            if (this.formData.id) {
              this.$emit('ok', {
                id: this.formData.id,
                ...values,
                reqType: values.reqType && values.reqType.key
              })
            } else {
              this.$emit('ok', {
                ...values,
                reqType: values.reqType && values.reqType.key
              })
            }
          }
        })
    },
    handleClose() {
      this.$emit('close')
    }
  }
}
</script>


```