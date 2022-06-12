<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <p>
      For a guide and recipes on how to configure / customize this project,<br />
      check out the
      <a href="https://cli.vuejs.org" target="_blank" rel="noopener"
        >vue-cli documentation</a
      >.
    </p>
    <h3>Installed CLI Plugins</h3>
    <ul>
      <li>
        <a
          href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-babel"
          target="_blank"
          rel="noopener"
          >babel</a
        >
      </li>
      <li>
        <a
          href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-router"
          target="_blank"
          rel="noopener"
          >router</a
        >
      </li>
      <li>
        <a
          href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-vuex"
          target="_blank"
          rel="noopener"
          >vuex</a
        >
      </li>
      <li>
        <a
          href="https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-plugin-eslint"
          target="_blank"
          rel="noopener"
          >eslint</a
        >
      </li>
    </ul>
    <h3>Essential Links</h3>
    <ul>
      <li>
        <a href="https://vuejs.org" target="_blank" rel="noopener">Core Docs</a>
      </li>
      <li>
        <a href="https://forum.vuejs.org" target="_blank" rel="noopener"
          >Forum</a
        >
      </li>
      <li>
        <a href="https://chat.vuejs.org" target="_blank" rel="noopener"
          >Community Chat</a
        >
      </li>
      <li>
        <a href="https://twitter.com/vuejs" target="_blank" rel="noopener"
          >Twitter</a
        >
      </li>
      <li>
        <a href="https://news.vuejs.org" target="_blank" rel="noopener">News</a>
      </li>
    </ul>
    <h3>Ecosystem</h3>
    <ul>
      <li>
        <a href="https://router.vuejs.org" target="_blank" rel="noopener"
          >vue-router</a
        >
      </li>
      <li>
        <a href="https://vuex.vuejs.org" target="_blank" rel="noopener">vuex</a>
      </li>
      <li>
        <a
          href="https://github.com/vuejs/vue-devtools#vue-devtools"
          target="_blank"
          rel="noopener"
          >vue-devtools</a
        >
      </li>
      <li>
        <a href="https://vue-loader.vuejs.org" target="_blank" rel="noopener"
          >vue-loader</a
        >
      </li>
      <li>
        <a
          href="https://github.com/vuejs/awesome-vue"
          target="_blank"
          rel="noopener"
          >awesome-vue</a
        >
      </li>
    </ul>
  </div>
  <span id="output">渲染后台公式： {{ `$${myFormula}$` }} </span>
  <br/>
  <input v-model="inputFormula"/>
  <span id="output2">公式实时渲染： {{ inputFormula ? `$${inputFormula}$`:"" }}</span>
</template>

<script>
import { reactive, ref } from "@vue/reactivity";
import { onMounted, watch } from '@vue/runtime-core';

export default {
  name: "HelloWorld",
  props: {
    msg: String,
  },
  setup() {
    const myFormula = ref("\\frac{1}{2}"); //自己指定公式
    const inputFormula = ref("") //也可以动态输入

    //渲染函数，调用时会渲染指定节点elements，如果没有指定节点，渲染页面上所有公式
    //elements可以是一个DOM节点的数组(注意getXXXsByYYY的结果是collection，必须手动转为数组才行)
    const TypeSet = async function (elements) {
      if (!window.MathJax) {
        return;
      }
      window.MathJax.startup.promise = window.MathJax.startup.promise
        .then(() => {
          return window.MathJax.typesetPromise(elements);
        })
        .catch((err) => console.log("Typeset failed: " + err.message));
      return window.MathJax.startup.promise;
    };

    //onMounted的时候必须调用
    onMounted(() => {
      TypeSet();
      //这里写自己的onMounted内容
    });
  
    watch(
      () => myFormula.value,
      (newValue) => {
        //直接调用TypeSet()也可以，但是会重新渲染页面所有公式
        TypeSet([document.getElementById("output")]);
      }
    );

    watch(
      () => inputFormula.value,
      (newValue) => {
        //直接调用TypeSet()也可以，但是会重新渲染页面所有公式
        TypeSet([document.getElementById("output2")]);
      }
    );

    return {
      myFormula,
      inputFormula
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
