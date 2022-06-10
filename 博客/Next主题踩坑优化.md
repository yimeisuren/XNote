# Next主题踩坑优化

对于Typora上编辑的markdown文档, 直接使用Next主题会有导致一些问题: 

+ Latex公式不能够正常显示
+ mermaid绘制的图形不能够正常显示
+ 高亮效果不能够正常显示

**术语**

+ 站点配置文件*config.yml*
+ 主题配置文件*themes/next/config.yml*

## Latex公式显示异常

1. 更换hexo默认的markdown渲染引擎

   ```shell
   npm uninstall hexo-renderer-marked --save
   npm install hexo-renderer-kramed --save
   ```

   hexo-renderer-kramed引擎解决了一些问题, 但是并没有完全解决

2. 取消一些转义
   对文件`node_modules\kramed\lib\rules\inline.js`进行修改

   ```js
   //escape: /^\\([\\`*{}\[\]()#$+\-.!_>])/,
   escape: /^\\([`*\[\]()#$+\-.!_>])/,  //取消了对\,{,}的转义
   
   //em: /^\b_((?:__|[\s\S])+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
   em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,    
   ```

3. 在 Next 主题中开启 MathJax 开关
   对主题配置文件`themes/next/config.yml`进行修改

   ```yaml
   # Math Equations Render Support
   math:
     enable: true #由false改为true
     per_page: true
   ```

4. 在文章的Front-matter里打开mathjax开关
   使用hexo new命令生成的markdown文件的开头部分

   ```markdown
   ---
   title: index.html
   date: 2018-07-05 12:01:30
   tags:
   mathjax: true
   --
   ```

   

## mermaid图形显示异常

1. 添加对于mermaid的支持

   ```shell
   npm install --save hexo-filter-mermaid-diagrams
   ```

2. 在主题配置文件`/themes/next/_config.yml`中启用mermaid模块

   ```yaml
    #Mermaid tag
   mermaid:
     enable: true
     # Available themes: default | dark | forest | neutral
     theme: forest
   ```

   

## 高亮效果显示异常