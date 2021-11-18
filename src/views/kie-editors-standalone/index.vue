<template>
  <div class="dashboard-container">
    <el-button-group>
      <el-button @click="undo" size="mini" icon="el-icon-refresh-left"></el-button>
      <el-button @click="redo" size="mini" icon="el-icon-refresh-right"></el-button>
      <el-button @click="download" size="mini" icon="el-icon-download"></el-button>
    </el-button-group>
    <div class="containers" id="canvas"></div>
  </div>
</template>

<script>
import * as BpmnEditor from "@kogito-tooling/kie-editors-standalone/dist/bpmn";
import * as I18nCore from "@kogito-tooling/i18n/dist/core";

export default {
  name: "KieEditorsStandalone",
  data() {
    return {
      editor: null,
    };
  },
  mounted() {
    this.editor = BpmnEditor.open({
      container: document.getElementById("canvas"),
      initialContent: Promise.resolve(""),
      readOnly: false,
      origin: '*'
    });
  },
  methods: {
    undo() {
      this.editor.undo()
    },
    redo() {
      this.editor.redo()
    },
    download() {
      this.editor.getContent().then(content => {
        const elem = window.document.createElement("a")
        elem.href = "data:text/plain;charset=utf-8," + encodeURIComponent(content)
        elem.download = "model.bpmn"
        document.body.appendChild(elem)
        elem.click()
        document.body.removeChild(elem)
        editor.markAsSaved()
      })
    }
  },
};
</script>

<style lang="scss" scoped>
.dashboard {
  &-container {
    margin: 20px;
  }
  &-text {
    font-size: 30px;
    line-height: 46px;
  }
}

.containers {
  position: absolute;
  background-color: #eeeeee;
  width: 96%;
  height: 100%;
  .canvas {
    width: 100%;
    height: 100%;
  }
  .bjs-powered-by {
    display: none;
  }
}

.containers >>> .collapsed-docks-bar-E {
  top: 20px;
}
</style>
