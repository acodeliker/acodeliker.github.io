# Docsify Quick tourial

> An awesome project.

- index.html有两个作用：一是作为网站入口，也就是一开始打开网站时显示的loading页面，二是作为配置文件
- README.md即是网站根目录网页，也就是普通概念里网站的index.html,为主页内容作渲染
- .nojekyll 用于阻止 GitHub Pages 忽略掉下划线开头的文件


> 
> - #### 多页文档
  
    Docs
	├── index.html
	├── README.md       =>  http://domain.com
	└── FileDomain 
        ├── README.md   =>  http://domain.com/wenjinyu  
	    └── test.md     =>  http://domain.com/wenjinyu/test
如果需要创建多个页面，或者需要多级路由的网站，在 docsify 里也能很容易的实现。例如创建一个 guide.md 文件，那么对应的路由就是 /#/guide

> 
> 
> - #### 代码高亮
> 
>    docsify的代码块支持代码高亮，使用的工具是Prism，不过并没有保存高亮的支持文件，需要自行到Prims 仓库找到js文件，然后添加引用，格式如下：

> 
```
  1.<script src="//unpkg.com/prismjs/components/prism-bash.js"></script> 
  2.<script src="//unpkg.com/prismjs/components/prism-php.js"></script>
```
> 
>   上面两行就是支持bash和php语法高亮，其他语法也是同理。

 
>    需要说明的一点是，CPP比较特殊，语法高亮需要额外添加两个文件：prism-c和prism-clike，之后要使用其他 CPP 语法高亮再另行添加，也就是说，任何 CPP 语法高亮都需要添加三行，以 CPP 为例：
```
1.<script src="//unpkg.com/prismjs/components/prism-c.js"></script>
2.<script src="//unpkg.com/prismjs/components/prism-clike.js"></script>
3.<script src="//unpkg.com/prismjs/components/prism-cpp.js"></script> 
```
