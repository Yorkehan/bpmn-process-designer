<template>
  <div id="app">
    <my-process-designer
      :key="`designer-${reloadIndex}`"
      v-bind="controlForm"
      ref="processDesigner"
      @element-click="elementClick"
      @init-finished="initModeler"
    />
    <my-process-panel :key="`penal-${reloadIndex}`" :bpmn-modeler="modeler" :prefix="controlForm.prefix" class="process-panel" />

    <!-- demo config -->
    <div class="demo-control-bar">
      <div class="open-control-dialog" @click="controlDrawerVisible = true"><i class="el-icon-setting"></i></div>
    </div>
    <el-drawer :visible.sync="controlDrawerVisible" size="400px" title="偏好设置" append-to-body destroy-on-close>
      <el-form :model="controlForm" size="small" label-suffix="：" label-width="100px" class="control-form">
        <el-form-item label="流转模拟">
          <el-switch v-model="controlForm.simulation" inactive-text="停用" active-text="启用" @change="reloadProcessDesigner" />
        </el-form-item>
        <el-form-item label="双击编辑">
          <el-switch v-model="controlForm.labelEditing" inactive-text="停用" active-text="启用" @change="changeLabelEditingStatus" />
        </el-form-item>
        <el-form-item label="隐藏label">
          <el-switch v-model="controlForm.labelVisible" inactive-text="停用" active-text="启用" @change="changeLabelVisibleStatus" />
        </el-form-item>
        <el-form-item label="流程引擎">
          <el-radio-group v-model="controlForm.prefix" @change="reloadProcessDesigner">
            <el-radio label="camunda">camunda</el-radio>
            <el-radio label="flowable">flowable</el-radio>
            <el-radio label="activiti">activiti</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="工具栏">
          <el-radio-group v-model="controlForm.headerButtonSize">
            <el-radio label="mini">mini</el-radio>
            <el-radio label="small">small</el-radio>
            <el-radio label="medium">medium</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-form>
      <br />
      <p style="color: #999999">注：activiti 好像不支持表单配置，控制台可能会报错</p>
      <p style="color: #999999">更多配置请查看源码：<a href="https://github.com/miyuesc/bpmn-process-designer">MiyueSC/bpmn-process-designer</a></p>
    </el-drawer>
  </div>
</template>

<script>
import translations from "@/translations";
import CustomRenderer from "@/modules/custom-remderer";
import MyProcessPanel from "../package/process-panel/ProcessPanel";

export default {
  name: "App",
  components: { MyProcessPanel },
  data() {
    return {
      xmlString: "",
      modeler: null,
      reloadIndex: 0,
      controlDrawerVisible: false,
      translationsSelf: translations,
      controlForm: {
        simulation: true,
        labelEditing: false,
        labelVisible: false,
        prefix: "camunda",
        headerButtonSize: "medium",
        additionalModel: []
      }
    };
  },
  created() {
    // console.log(this.translationsSelf);
  },
  methods: {
    initModeler(modeler) {
      setTimeout(() => {
        this.modeler = modeler;
        console.log(this.modeler.get("customRenderer"));
      }, 10);
    },
    reloadProcessDesigner() {
      this.reloadIndex += 1;
      this.modeler = null; // 避免 panel 异常
    },
    changeLabelEditingStatus(status) {
      this.controlForm.additionalModel = status ? [] : [{ labelEditingProvider: ["value", ""] }];
      this.reloadProcessDesigner();
    },
    changeLabelVisibleStatus(status) {
      this.controlForm.additionalModel = status ? [CustomRenderer] : [];
      this.reloadProcessDesigner();
    },
    elementClick(element) {
      this.element = element;
      console.log(element);
    }
  }
};
</script>

<style lang="scss">
body {
  overflow: hidden;
  margin: 0;
  padding: 8px !important;
  box-sizing: border-box;
}
#app {
  width: 100%;
  height: calc(100vh - 16px);
  box-sizing: border-box;
  display: inline-grid;
  grid-template-columns: auto max-content;
}
.demo-control-bar {
  position: fixed;
  right: 8px;
  top: 50%;
  z-index: 1;
  transform: translateY(-50%);
  .open-control-dialog {
    width: 48px;
    height: 48px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 4px;
    font-size: 32px;
    background: rgba(64, 158, 255, 1);
    color: #ffffff;
    cursor: pointer;
  }
}
.control-form {
  .el-radio {
    width: 100%;
    line-height: 32px;
  }
}
body,
body * {
  /* 滚动条 */
  &::-webkit-scrollbar-track-piece {
    background-color: #fff; /*滚动条的背景颜色*/
    -webkit-border-radius: 0; /*滚动条的圆角宽度*/
  }
  &::-webkit-scrollbar {
    width: 10px; /*滚动条的宽度*/
    height: 8px; /*滚动条的高度*/
  }
  &::-webkit-scrollbar-thumb:vertical {
    /*垂直滚动条的样式*/
    height: 50px;
    background-color: rgba(153, 153, 153, 0.5);
    -webkit-border-radius: 4px;
    outline: 2px solid #fff;
    outline-offset: -2px;
    border: 2px solid #fff;
  }
  &::-webkit-scrollbar-thumb {
    /*滚动条的hover样式*/
    background-color: rgba(159, 159, 159, 0.3);
    -webkit-border-radius: 4px;
  }
  &::-webkit-scrollbar-thumb:hover {
    /*滚动条的hover样式*/
    background-color: rgba(159, 159, 159, 0.5);
    -webkit-border-radius: 4px;
  }
}
</style>
