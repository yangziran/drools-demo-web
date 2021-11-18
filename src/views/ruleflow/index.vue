<template>
  <div class="dashboard-container">
    <div class="containers" ref="canvas"></div>
    <div id="properties" class="properties"></div>
    <ul class="buttons">
      <li>
        <a href="javascript:" @click="saveXml" title="保存为BPMN文件"><i class="el-icon-download"></i></a>
      </li>
      <li>
        <a href="javascript:" @click="handlerZoom(0.1)" title="放大"><i class="el-icon-zoom-in"></i></a>
      </li>
      <li>
        <a href="javascript:" @click="handlerZoom(-0.1)" title="缩小"><i class="el-icon-zoom-out"></i></a>
      </li>
      <li>
        <a href="javascript:" @click="handlerZoom(0)" title="还原"><i class="el-icon-aim"></i></a>
      </li>
    </ul>
  </div>
</template>

<script>
import i18n from '@szgc/bpmn-i18n'
import Modeler from 'bpmn-js/lib/Modeler'
import OriginModule from 'diagram-js-origin'
import PanelModule from 'bpmn-js-properties-panel'
import ProviderModule from 'bpmn-js-properties-panel/lib/provider/bpmn'
// import ModuleDescriptor from 'camunda-bpmn-moddle/resources/camunda'

export default {
  name: 'RuleFlow',
  data() {
    return {
      modeler: null,
      scale: 1
    }
  },
  mounted() {
    const canvas = this.$refs.canvas
    this.modeler = new Modeler({
      container: canvas,
      propertiesPanel: {
        parent: '#properties'
      },
      additionalModules: [
        i18n('zh-CN'),
        OriginModule,
        PanelModule,
        ProviderModule
      ]
      // moddleExtensions: {
      //   camunda: ModuleDescriptor
      // }
    })
    this.newDiagram()
  },
  methods: {
    async newDiagram() {
      const bpmnXml = `
        <?xml version="1.0" encoding="UTF-8"?>
        <!-- origin at X=0.0 Y=0.0 -->
        <bpmn2:definitions xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:bpmn2="http://www.omg.org/spec/BPMN/20100524/MODEL" xmlns:bpmndi="http://www.omg.org/spec/BPMN/20100524/DI" xmlns:dc="http://www.omg.org/spec/DD/20100524/DC" xmlns:di="http://www.omg.org/spec/DD/20100524/DI" xmlns:java="http://www.java.com/javaTypes" xmlns:tns="http://www.jboss.org/drools" xmlns="http://www.jboss.org/drools" xsi:schemaLocation="http://www.omg.org/spec/BPMN/20100524/MODEL BPMN20.xsd http://www.jboss.org/drools drools.xsd http://www.bpsim.org/schemas/1.0 bpsim.xsd" id="Definition" exporter="org.eclipse.bpmn2.modeler.core" exporterVersion="1.5.3.Final-v20210519-2007-B1" expressionLanguage="http://www.mvel.org/2.0" targetNamespace="http://www.jboss.org/drools" typeLanguage="http://www.java.com/javaTypes">
          <bpmn2:process id="defaultPackage.New_Process" tns:packageName="defaultPackage" name="New Process" isExecutable="true" processType="Private"/>
          <bpmndi:BPMNDiagram id="BPMNDiagram_1">
            <bpmndi:BPMNPlane id="BPMNPlane_Process_1" bpmnElement="defaultPackage.New_Process"/>
          </bpmndi:BPMNDiagram>
        </bpmn2:definitions>
      `
      await this.modeler.importXML(bpmnXml)
    },
    async saveXml() {
      const result = await this.modeler.saveXML({ format: true })
      const { xml } = result
      var xmlBlob = new Blob([xml], {
        type: 'application/bpmn20-xml;charset=UTF-8,'
      })
      var downloadLink = document.createElement('a')
      downloadLink.download = 'RuleFlow.bpmn2'
      downloadLink.innerHTML = 'Get BPMN SVG'
      downloadLink.href = window.URL.createObjectURL(xmlBlob)
      downloadLink.onclick = function(event) {
        document.body.removeChild(event.target)
      }
      downloadLink.style.visibility = 'hidden'
      document.body.appendChild(downloadLink)
      downloadLink.click()
    },
    handlerZoom(radio) {
      const newScale = !radio ? 1.0 : this.scale + radio
      this.modeler.get('canvas').zoom(newScale)
      this.scale = newScale
    }
  }
}
</script>

<style lang="scss" scoped>
@import '~bpmn-js/dist/assets/diagram-js.css';
@import '~bpmn-js/dist/assets/bpmn-font/css/bpmn.css';
@import '~bpmn-js-properties-panel/dist/assets/bpmn-js-properties-panel.css';

.dashboard {
  &-container {
    margin: 20px;
  }
  &-text {
    font-size: 30px;
    line-height: 46px;
  }
}

.containers{
  position: absolute;
  background-color: #eeeeee;
  width: 96%;
  height: 100%;
  .canvas{
    width: 100%;
    height: 100%;
  }
  .bjs-powered-by {
    display: none;
  }
}

.properties {
  position: absolute;
  top: 70px;
  right: 29px;
  width: 300px;
  height: 89%;
  overflow-y: scroll;
}

.buttons {
  position: absolute;
  left: 10px;
  bottom: 10px;
}
.buttons li {
  display: inline-block;
  margin: 5px;
}
.buttons li a {
  color: #555555;
  background: #fff;
  cursor: pointer;
  padding: 8px;
  border: 1px solid #ccc;
  text-decoration: none;
}
</style>
