<svelte:options tag="code-highlighter" />

<script>
import hljs from 'highlight.js/lib/core';
import javascript from 'highlight.js/lib/languages/javascript';
import xml from 'highlight.js/lib/languages/xml';
import json from 'highlight.js/lib/languages/json';
import phpTemplate from 'highlight.js/lib/languages/php-template';
import php from 'highlight.js/lib/languages/php';
import bash from 'highlight.js/lib/languages/bash';
let languages = {
	javascript,
	html:xml,
	xml,
	json,
	"php-template":phpTemplate,
	php,
	bash
};
import { onMount } from 'svelte';
export let content='';
export let lang;
let text = '';
let e;
onMount(()=>{
	document.addEventListener('DOMContentLoaded', () => {
		update();
	});
});

function update(){
	e.className = ''
	e.classList.add('hljs')
	e.classList.add(lang.split("\s")[0])
	hljs.registerLanguage(lang,languages[lang]);
	e.innerHTML = text;
	hljs.highlightBlock(e);
}

function onContentChange(content){
	if(!e || !hljs) return;
	content = content.replaceAll(/^ {4}/g,"\t");
	content = content.replaceAll(/^ +/g,'');
	let bar = content.match(/.+(?=\|)/);
	if(bar && bar.length > 0){
		content = content.replace(/.+\|/,'');
		const regex = new RegExp("^"+bar[0]);
		const rows = content.split("\n");
		text = '';
		rows.forEach(row => {
			if(row.startsWith(bar[0])){
				text += row.replace(regex,'')+"\n";
			}else{
				text += row+"\n";
			}
		});
		text = text.trim();
	}else{
		text = content.trim();
	} 
	update();
}
$:onContentChange(content);
</script>

<style>
	@import './style.css';
	pre{
		margin: 0;
	}
	code{
		padding: 0;
	}
	@media screen and (max-width: 980px) {
		code{
			padding: 0.3rem;
		}
   }
	.hljs{
		padding-top: 0;
		padding-bottom: 0;
		background: transparent;
	}
</style>
<pre {...$$restProps}><code bind:this={e}></code></pre>