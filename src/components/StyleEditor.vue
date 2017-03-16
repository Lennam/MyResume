<template>
    <div class="styleEditor" ref="container">
    	<div class="code" v-html="codeInStyle"></div>
    	<pre class="" v-html="highlighCode"></pre>
    </div>
</template>

<script type="ecmascript-6">
	import Prism from 'prismjs'
    export default {
    	name: 'styleEditor',
    	props: ['code'],
    	computed: {
            highlighCode: function () {
                return Prism.highlight(this.code, Prism.languages.css)
            },
    		codeInStyle: function () {
    			return '<style>' + this.code + '</style>'
    		}
    	},
    	methods: {
      		goBottom() {
        		this.$refs.container.scrollTop = 100000
            },
        }
    }
</script>

<style rel="stylesheet" scoped>
.code {
    display: none;
  }
</style>