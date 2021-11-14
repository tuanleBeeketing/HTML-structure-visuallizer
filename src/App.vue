<template>
  <div class="container-fluid py-3 px-5">
    <h1 class="text-center">HTML structure visuallizer</h1>
    <input-field
      @update-html-input="handleUpdateInput"
      @process-input="processInput"
    />
    <html-diagram :hierachyObject="hierachyObject" />
  </div>
</template>

<script>
import './assets/style/index.css'
import HtmlDiagram from "./components/HtmlDiagram.vue";
import InputField from "./components/inputField.vue";
export default {
  name: "App",
  data() {
    return {
      htmlInput: "",
      hierachyObject: {},
    };
  },
  methods: {
    handleUpdateInput: function (value) {
      this.htmlInput = value;
    },
    processInput: function () {
      const handleDomNode = function (domNode) {
        const tagName = domNode.localName;
        let attributes = [...domNode.attributes];
        attributes = attributes.map((attr) => {
          return { [attr.nodeName]: attr.value };
        });

        // let title = tagName;
        // if (attributes.hasOwnProperty("class")) {
        //   const classNameRegex = /[a-zA-Z0-9-]+/;
        //   const classList = [...attributes.class.match(classNameRegex)];
        //   if(classList.length)
        // }
        let title = tagName;
        if (domNode.classList.length > 0) {
          title += `.${domNode.classList[0]}`;
        }
        attributes = Object.assign({}, ...attributes);
        let childNodes = [...domNode.childNodes];
        if (childNodes.length > 0) {
          childNodes = childNodes.filter((node) => {
            return node.nodeName !== "#text" && node.nodeName !== "#comment";
          });
        }
        if (childNodes.length > 0) {
          childNodes = childNodes.map((node) => {
            return handleDomNode(node);
          });
        }
        return { tagName,title, attributes, childNodes };
      };

      const target = document.getElementById("target");
      target.innerHTML = this.htmlInput;
      let rootNodeArr = [...target.childNodes];
      rootNodeArr = rootNodeArr.filter((node) => {
        return node.nodeName !== "#text";
      });
      this.hierachyObject = handleDomNode(rootNodeArr[0]);
    },
  },
  components: {
    HtmlDiagram,
    InputField,
    // InputField,
    // HtmlDiagram,
  },
};
</script>

<style lang="scss">
* {
  box-sizing: border-box;
}
ul{
  list-style: none;
  padding-left: 0;
}
</style>
