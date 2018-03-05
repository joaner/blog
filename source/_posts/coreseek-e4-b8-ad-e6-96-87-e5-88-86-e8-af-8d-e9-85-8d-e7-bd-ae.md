---
title: coreseek中文分词配置
id: 21
categories:
  - sphinx
date: 2014-06-27 23:46:59
tags:
---

在安装配置coreseek 3.2.13 中积累了一些问题，在这里记录下来:

### 1\. 让一些特殊字符也能被检索，比如 +- （加减号）

正常来说修改主配置文件sphinx.conf的 index.charset_table 就可以了，因为只有charset_table包含的字符才会被sphinx识别并编入索引中。但coreseek中charset_table却不能用，因为这个配置还担任了中文分词开关的角色，就是说如果在配置文件里填写了这个配置，那么中文分词就不会开启，鱼和熊掌不能兼得。

有一个方法，就是修改它程序内置的charset_table配置，这样既能不关闭中文分词，又能添加新的字符给索引识别出来。

找到源码包中的c++文件： <span style="color: #000000;">coreseek-3.2.13/csft-3.2.13/src/sphinx.cpp</span>
<div style="color: #000000;">
<pre class="lang:c++ decode:true ">// 修改 197..198 行
// old:
const char *    SPHINX_DEFAULT_SBCS_TABLE       = "0..9, A..Z-&gt;a..z, _, a..z, U+A8-&gt;U+B8, U+B8, U+C0..U+DF-&gt;U+E0..U+FF, U+E0..U+FF";
const char *    SPHINX_DEFAULT_UTF8_TABLE       = "0..9, A..Z-&gt;a..z, _, a..z, U+410..U+42F-&gt;U+430..U+44F, U+430..U+44F";
// --------------------------------------------
// 添加了 :  +, -, {, }, |, =, &gt;, &lt;  这些符号
const char *    SPHINX_DEFAULT_SBCS_TABLE       = "0..9, A..Z-&gt;a..z, _, +, -, {, }, |, =, &gt;, &lt;, a..z, U+A8-&gt;U+B8, U+B8, U+C0..U+DF-&gt;U+E0..U+FF, U+E0..U+FF";
const char *    SPHINX_DEFAULT_UTF8_TABLE       = "0..9, A..Z-&gt;a..z, _, +, -, {, }, |, =, &gt;, &lt;, a..z, U+410..U+42F-&gt;U+430..U+44F, U+430..U+44F";</pre>
然后重新编译一遍 coreseek，覆盖原先的执行文件如 bin/searchd bin/indexer 。重启searchd并重新编排索引就可以生效。

</div>