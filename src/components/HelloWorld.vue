<template>
  <div class="json-config">
    <article id="editor" class="content"></article>
  </div>
</template>

<script setup>
import { defineProps, reactive, onMounted } from "vue";
import * as monaco from "monaco-editor";
import editorWorker from "monaco-editor/esm/vs/editor/editor.worker?worker";
import jsonWorker from "monaco-editor/esm/vs/language/json/json.worker?worker";
import cssWorker from "monaco-editor/esm/vs/language/css/css.worker?worker";
import axios from "axios";
import qs from "qs";

self.MonacoEnvironment = {
  getWorker(_, label) {
    if (label === "json") {
      return new jsonWorker();
    }
    if (label === "css" || label === "scss" || label === "less") {
      return new cssWorker();
    }
    if (label === "html" || label === "handlebars" || label === "razor") {
      return new htmlWorker();
    }
    return new editorWorker();
  },
};

function createNewPPTRequest(data) {
  return axios({
    url: "/api/course/resource/interactiveware_add/",
    data: qs.stringify(data),
    method: "post",
  });
}

function initEditor() {
  let editor;
  editor = monaco.editor.create(document.getElementById("editor"), {
    value: '{ "hello": "world" }',
    language: "json",
    selectOnLineNumbers: true,
    readOnly: false, // 只读
    cursorStyle: "line", //光标样式
    glyphMargin: true, //字形边缘
    folding: true,
    foldingStrategy: "indentation", // 代码可分小段折叠
    automaticLayout: true, // 自适应布局
    scrollBeyondLastLine: true, // 取消代码后面一大段空白
    minimap: {
      enabled: true, // 不要小地图
    },
  });

  editor.onDidChangeModelContent((event) => {
    this.editorChange();
  });
}

defineProps({
  msg: String,
});

onMounted(() => {
  initEditor();

  createNewPPTRequest()
    .then((data) => console.log(data))
    .catch((err) => console.log(err));
});
</script>

<style lang="less" scoped>
.json-config {
  .content {
    width: 660px;
    min-height: 260px;
    padding-top: 1rem;
    border: 1px solid #dcdee2;
    border-radius: 4px;
  }
  .btn {
    margin-top: 1rem;
    width: 660px;
    display: flex;
    justify-content: space-between;
  }
}
</style>
