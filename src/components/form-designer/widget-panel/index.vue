<template>
  <el-scrollbar class="side-scroll-bar" :style="{height: scrollerHeight}">
    <div class="panel-container">

    <el-tabs v-model="firstTab" class="no-bottom-margin indent-left-margin" style="height: 100%">
      <el-tab-pane name="dictionary" :label="i18nt('designer.dictionary')" style="height: 100%">
        <div style="height: 100%; display: flex">
          <div style="width: 140px; height: calc(100% - 14px); padding-top: 14px; overflow: auto; background: #fafafa;">
            <el-tree
              class="dictionary-tree"
              :data="data"
              :props="{
                children: 'children',
                label: 'label'
              }"
              @node-click="handleNodeClick"
            />
          </div>
          <div class="dictionary-content">
            <div class="section-title">{{ sectionTitle }}<span class="count">共 {{ basicFields.length }} 个字段</span></div>
            <div class="c-list">
              <draggable tag="ul" :list="basicFields" item-key="key" :group="{name: 'dragGroup', pull: 'clone', put: false}"
                        :move="checkFieldMove"
                        :clone="handleFieldWidgetClone" ghost-class="ghost" :sort="false">
                <template v-for="(fld, index) in basicFields">
                  <li v-if="!fld.disShow" :key="index" class="field-widget-item" :title="fld.displayName" @dblclick="addFieldByDbClick(fld)">
                    <span><i class="el-icon-rank" style="color: #409EFF; margin: 0px 2px"></i>{{i18n2t(`designer.widgetLabel.${fld.type}`, `extension.widgetLabel.${fld.type}`)}}</span>
                    <!-- <span><svg-icon :icon-class="fld.icon" class-name="color-svg-icon" />{{i18n2t(`designer.widgetLabel.${fld.type}`, `extension.widgetLabel.${fld.type}`)}}</span> -->
                  </li>
                </template>
              </draggable>
            </div>
          </div>
        </div>
        <!-- <template #label>
          <span><svg-icon icon-class="el-set-up" /> {{i18nt('designer.componentLib')}}</span>
        </template> -->

      </el-tab-pane>
      <el-tab-pane name="componentLib">
        <span slot="label"><i class="el-icon-set-up"></i> {{i18nt('designer.componentLib')}}</span>

      <el-collapse v-model="activeNames" class="widget-collapse">
        <el-collapse-item name="1" :title="i18nt('designer.containerTitle')">
          <draggable tag="ul" :list="containers" :group="{name: 'dragGroup', pull: 'clone', put: false}"
                     :clone="handleContainerWidgetClone" ghost-class="ghost" :sort="false"
                     :move="checkContainerMove" @end="onContainerDragEnd">
            <template v-for="(ctn, index) in containers">
              <li v-if="!ctn.disShow" :key="index" class="container-widget-item" :title="ctn.displayName"
                  @dblclick="addContainerByDbClick(ctn)">
                <span><svg-icon :icon-class="ctn.icon" class-name="color-svg-icon" />{{i18n2t(`designer.widgetLabel.${ctn.type}`, `extension.widgetLabel.${ctn.type}`)}}</span>
              </li>
            </template>
          </draggable>
        </el-collapse-item>

        <el-collapse-item name="2" :title="i18nt('designer.basicFieldTitle')">
          <draggable tag="ul" :list="basicFields" :group="{name: 'dragGroup', pull: 'clone', put: false}"
                     :move="checkFieldMove"
                     :clone="handleFieldWidgetClone" ghost-class="ghost" :sort="false">
            <template v-for="(fld, index) in basicFields">
              <li v-if="!fld.disShow" :key="index" class="field-widget-item" :title="fld.displayName"
                  @dblclick="addFieldByDbClick(fld)">
                <span><svg-icon :icon-class="fld.icon" class-name="color-svg-icon" />{{i18n2t(`designer.widgetLabel.${fld.type}`, `extension.widgetLabel.${fld.type}`)}}</span>
              </li>
            </template>
          </draggable>
          <draggable tag="ul" :list="containers" item-key="key" :group="{name: 'dragGroup', pull: 'clone', put: false}"
                    :clone="handleContainerWidgetClone" ghost-class="ghost" :sort="false"
                    :move="checkContainerMove" @end="onContainerDragEnd">
            <template v-for="(ctn, index) in containers">
              <li v-if="ctn.type === 'table'" :key="index" class="container-widget-item" :title="ctn.displayName" @dblclick="addContainerByDbClick(ctn)">
                <span><svg-icon :icon-class="ctn.icon" class-name="color-svg-icon" />{{i18n2t(`designer.widgetLabel.${ctn.type}`, `extension.widgetLabel.${ctn.type}`)}}</span>
              </li>
            </template>
          </draggable>
          <draggable tag="ul" :list="advancedFields" item-key="key" :group="{name: 'dragGroup', pull: 'clone', put: false}"
                    :move="checkFieldMove"
                    :clone="handleFieldWidgetClone" ghost-class="ghost" :sort="false">
            <template v-for="(fld, index) in advancedFields">
              <li v-if="!fld.disShow" :key="index" class="field-widget-item" :title="fld.displayName" @dblclick="addFieldByDbClick(fld)">
                <span><svg-icon :icon-class="fld.icon" class-name="color-svg-icon" />{{i18n2t(`designer.widgetLabel.${fld.type}`, `extension.widgetLabel.${fld.type}`)}}</span>
              </li>
            </template>
          </draggable>
        </el-collapse-item>

        <el-collapse-item v-if="false" name="3" :title="i18nt('designer.advancedFieldTitle')">
          <draggable tag="ul" :list="advancedFields" :group="{name: 'dragGroup', pull: 'clone', put: false}"
                     :move="checkFieldMove"
                     :clone="handleFieldWidgetClone" ghost-class="ghost" :sort="false">
            <li v-for="(fld, index) in advancedFields" :key="index" class="field-widget-item" :title="fld.displayName"
                @dblclick="addFieldByDbClick(fld)">
              <span><svg-icon :icon-class="fld.icon" class-name="color-svg-icon" />{{i18n2t(`designer.widgetLabel.${fld.type}`, `extension.widgetLabel.${fld.type}`)}}</span>
            </li>
          </draggable>
        </el-collapse-item>

        <!-- -->
        <el-collapse-item v-if="false" name="4" :title="i18nt('designer.customFieldTitle')">
          <draggable tag="ul" :list="customFields" :group="{name: 'dragGroup', pull: 'clone', put: false}"
                     :move="checkFieldMove"
                     :clone="handleFieldWidgetClone" ghost-class="ghost" :sort="false">
            <li v-for="(fld, index) in customFields" :key="index" class="field-widget-item" :title="fld.displayName"
                @dblclick="addFieldByDbClick(fld)">
              <span>
                <svg-icon :icon-class="fld.icon" class-name="color-svg-icon" />{{i18n2t(`designer.widgetLabel.${fld.type}`, `extension.widgetLabel.${fld.type}`)}}</span>
            </li>
          </draggable>
        </el-collapse-item>
        <!-- -->

      </el-collapse>

      </el-tab-pane>

      <el-tab-pane v-if="showFormTemplates()" name="formLib" style="padding: 8px">
        <span slot="label"><i class="el-icon-c-scale-to-original"></i> {{i18nt('designer.formLib')}}</span>

        <template v-for="(ft, idx) in formTemplates">
          <el-card :key="idx" :bord-style="{ padding: '0' }" shadow="hover" class="ft-card">
            <el-popover placement="right" trigger="hover">
              <img slot="reference" :src="ft.imgUrl" style="width: 200px">
              <img :src="ft.imgUrl" style="height: 600px;width: 720px">
            </el-popover>
            <div class="bottom clear-fix">
              <span class="ft-title">#{{idx+1}} {{ft.title}}</span>
              <el-button type="text" class="right-button" @click="loadFormTemplate(ft.jsonUrl)">
                {{i18nt('designer.hint.loadFormTemplate')}}</el-button>
            </div>
          </el-card>
        </template>
      </el-tab-pane>

    </el-tabs>

    </div>
  </el-scrollbar>
</template>

<script>
  import Draggable from 'vuedraggable'
  import {containers, basicFields, advancedFields, customFields} from "./widgetsConfig"
  import {formTemplates} from './templatesConfig'
  import {addWindowResizeHandler} from "@/utils/util"
  import i18n from "@/utils/i18n"
  import axios from "axios"
  import SvgIcon from '@/components/svg-icon'

  // import ftImg1 from '@/assets/ft-images/t1.png'
  // import ftImg2 from '@/assets/ft-images/t2.png'
  // import ftImg3 from '@/assets/ft-images/t3.png'
  // import ftImg4 from '@/assets/ft-images/t4.png'
  // import ftImg5 from '@/assets/ft-images/t5.png'
  // import ftImg6 from '@/assets/ft-images/t6.png'
  // import ftImg7 from '@/assets/ft-images/t7.png'
  // import ftImg8 from '@/assets/ft-images/t8.png'

  export default {
    name: "FieldPanel",
    mixins: [i18n],
    components: {
      Draggable,
      SvgIcon,
    },
    props: {
      designer: Object,
    },
    inject: ['getBannedWidgets', 'getDesignerConfig'],
    data() {
      return {
        designerConfig: this.getDesignerConfig(),

        // firstTab: 'componentLib',
        firstTab: 'dictionary',

        scrollerHeight: 0,

        activeNames: ['1', '2', '3', '4'],

        containers,
        basicFields,
        advancedFields,
        customFields,

        formTemplates: formTemplates,
        data: [{
          label: '患者人口学信息',
          children: [{ label: '患者人口学信息' }],
        }, {
          label: 'Level one 2',
          children: [{ label: 'Level two 2-1' }, { label: 'Level two 2-2' }]
        }, {
          label: 'Level one 3',
          children: [{ label: 'Level two 3-1' }, { label: 'Level two 3-2' }]
        }],
        sectionTitle: '',
        // ftImages: [
        //   {imgUrl: ftImg1},
        //   {imgUrl: ftImg2},
        //   {imgUrl: ftImg3},
        //   {imgUrl: ftImg4},
        //   {imgUrl: ftImg5},
        //   {imgUrl: ftImg6},
        //   {imgUrl: ftImg7},
        //   {imgUrl: ftImg8},
        // ]
      }
    },
    computed: {
      //
    },
    mounted() {
      this.loadWidgets()

      this.scrollerHeight = window.innerHeight + 'px'
      addWindowResizeHandler(() => {
        this.$nextTick(() => {
          this.scrollerHeight = window.innerHeight + 'px'
          //console.log(this.scrollerHeight)
        })
      })
    },
    methods: {
      handleNodeClick(row) {
        if(row.children) return;
        this.sectionTitle = row.label
      },

      isBanned(wName) {
        return this.getBannedWidgets().indexOf(wName) > -1
      },

      showFormTemplates() {
        if (this.designerConfig['formTemplates'] === undefined) {
          return true
        }

        return !!this.designerConfig['formTemplates']
      },

      loadWidgets() {
        this.containers = this.containers.map(con => {
          return {
            ...con,
            displayName: this.i18n2t(`designer.widgetLabel.${con.type}`, `extension.widgetLabel.${con.type}`)
          }
        }).filter(con => {
          return !con.internal && !this.isBanned(con.type)
        })

        this.basicFields = this.basicFields.map(fld => {
          return {
            ...fld,
            displayName: this.i18n2t(`designer.widgetLabel.${fld.type}`, `extension.widgetLabel.${fld.type}`)
          }
        }).filter(fld => {
          return !this.isBanned(fld.type)
        })

        this.advancedFields = this.advancedFields.map(fld => {
          return {
            ...fld,
            displayName: this.i18n2t(`designer.widgetLabel.${fld.type}`, `extension.widgetLabel.${fld.type}`)
          }
        }).filter(fld => {
          return !this.isBanned(fld.type)
        })

        this.customFields = this.customFields.map(fld => {
          return {
            ...fld,
            displayName: this.i18n2t(`designer.widgetLabel.${fld.type}`, `extension.widgetLabel.${fld.type}`)
          }
        }).filter(fld => {
          return !this.isBanned(fld.type)
        })
      },

      handleContainerWidgetClone(origin) {
        return this.designer.copyNewContainerWidget(origin)
      },

      handleFieldWidgetClone(origin) {
        return this.designer.copyNewFieldWidget(origin)
      },

      checkContainerMove(evt) {
        return this.designer.checkWidgetMove(evt)
      },

      checkFieldMove(evt) {
        return this.designer.checkFieldMove(evt)
      },

      onContainerDragEnd(evt) {
        //console.log('Drag end of container: ')
        //console.log(evt)
      },

      addContainerByDbClick(container) {
        this.designer.addContainerByDbClick(container)
      },

      addFieldByDbClick(widget) {
        if(!this.designer.widgetList.length) return this.$message.info('请先添加题组!')
        this.designer.addFieldByDbClick(widget)
      },

      loadFormTemplate(jsonUrl) {
        this.$confirm(this.i18nt('designer.hint.loadFormTemplateHint'), this.i18nt('render.hint.prompt'), {
          confirmButtonText: this.i18nt('render.hint.confirm'),
          cancelButtonText: this.i18nt('render.hint.cancel')
        }).then(() => {
          axios.get(jsonUrl).then(res => {
            let modifiedFlag = false
            if (typeof res.data === 'string') {
              modifiedFlag = this.designer.loadFormJson( JSON.parse(res.data) )
            } else if (res.data.constructor === Object) {
              modifiedFlag = this.designer.loadFormJson(res.data)
            }
            if (modifiedFlag) {
              this.designer.emitHistoryChange()
            }

            this.$message.success(this.i18nt('designer.hint.loadFormTemplateSuccess'))
          }).catch(error => {
            this.$message.error(this.i18nt('designer.hint.loadFormTemplateFailed') + ':' + error)
          })
        }).catch(error => {
          console.error(error)
        })
      }

    }

  }
</script>

<style lang="scss" scoped>
  .dictionary-tree {
    background: #fafafa;
    height: 100%;
    :deep(.el-tree-node__label) {
      text-overflow: ellipsis;
      overflow: hidden;
      cursor: pointer;
    }
  }
  .dictionary-content {
    height: calc(100% - 20px);
    padding: 20px 15px 0 10px;
    width: 195px;
    overflow: auto;
    .section-title {
      color: #333;
      font-weight: 500;
      font-size: 13px;
      .count {
        margin-left: 8px;
        font-size: 12px;
        font-weight: 400;
        color: #999faf;
        vertical-align: bottom;
      }
    }
    .c-list {
      display: flex;
      flex-wrap: wrap;
      overflow: hidden;
      ul {
        padding: 0px;
        padding-left: 1px;
        .field-widget-item {
          display: inline-block;
          height: 28px;
          line-height: 28px;
          width: auto;
          // float: left;
          margin: 2px 6px 6px 0;
          padding: 3px 6px 3px 0px;
          cursor: move;
          white-space: nowrap;
          text-overflow: ellipsis;
          overflow: hidden;
          background: #f1f2f3;
        }
      }
      .field-widget-item:hover {
        background: #EBEEF5;
        outline: 1px solid $--color-primary;
      }
    }
  }

  .color-svg-icon {
    -webkit-font-smoothing: antialiased;
    color: #7c7d82;
  }

  .side-scroll-bar {
    ::v-deep .el-scrollbar__wrap {
      overflow-x: hidden;
      .el-scrollbar__view {
        height: 100%;
      }
    }
  }

  div.panel-container {
    height: calc(100% - 10px);
    padding-bottom: 10px;
    .el-tabs {
      :deep(.el-tabs__content) {
        height: calc(100% - 40px);
      }
    }
  }

  .no-bottom-margin ::v-deep .el-tabs__header {
    margin-bottom: 0;
  }

  .indent-left-margin {
    ::v-deep .el-tabs__nav {
      margin-left: 20px;
    }
  }

  .el-collapse-item ::v-deep ul > li {
    list-style: none;
  }

  .widget-collapse {
    border-top-width: 0;

    ::v-deep .el-collapse-item__header {
      margin-left: 8px;
      font-style: italic;
      font-weight: bold;
    }

    ::v-deep .el-collapse-item__content {
      padding-bottom: 6px;

      ul {
        padding-left: 10px;  /* 重置IE11默认样式 */
        margin: 0;  /* 重置IE11默认样式 */
        margin-block-start: 0;
        margin-block-end: 0.25em;
        padding-inline-start: 10px;

        &:after {
          content: "";
          display: block;
          clear: both;
        }

        .container-widget-item, .field-widget-item {
          display: inline-block;
          height: 32px;
          line-height: 32px;
          width: 92px;
          float: left;
          margin: 2px 6px 6px 0;
          padding: 8px 0px;
          cursor: move;
          white-space: nowrap;
          text-overflow: ellipsis;
          overflow: hidden;
          background: #fff;
          border: 1px solid #e8e9eb;
          border-radius: 4px;
          padding: 0 8px;
        }

        .container-widget-item:hover, .field-widget-item:hover {
          background: #F1F2F3;
          border-color: $--color-primary;

          .color-svg-icon {
            color: $--color-primary;
          }
        }

        .drag-handler {
          position: absolute;
          top: 0;
          left: 160px;
          background-color: #dddddd;
          border-radius: 5px;
          padding-right: 5px;
          font-size: 11px;
          color: #666666;
        }
      }
    }
  }

  .el-card.ft-card {
    border: 1px solid #8896B3;
  }

  .ft-card {
    margin-bottom: 10px;

    .bottom {
      margin-top: 10px;
      line-height: 12px;
    }

    .ft-title {
      font-size: 13px;
      font-weight: bold;
    }

    .right-button {
      padding: 0;
      float: right;
    }

    .clear-fix:before, .clear-fix:after {
      display: table;
      content: "";
    }

    .clear-fix:after {
      clear: both;
    }

  }

</style>
