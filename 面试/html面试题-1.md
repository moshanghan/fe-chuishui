本文转载自[HTML 部分问题与解答](https://github.com/paddingme/Front-end-Web-Development-Interview-Question/blob/master/questions/2.md)     
原文作者： PaddingMe   

这里是 [Front End Web Development Quiz](http://davidshariff.com/quiz/)HTML 部分问题与解答

1. Q: `<keygen>` 是正确的HTML5标签吗？

    A: 是。

    `<keygen>` 标签规定用于表单的密钥对生成器字段。当提交表单时，私钥存储在本地，公钥发送到服务器。是HTML5 标签。

2. Q: `<bdo>` 标签是否可以改变文本方向？

    A: 可以。

    `<bdo>`标签覆盖默认的文本方向。

    ```html
    <bdo dir="rtl">Here is some text</bdo>
    ```

3. Q: 下列HTML代码是否正确？

    ```html
    <figure>
        <img src="myimage.jpg" alt="My image">
        <figcaption>
            <p>This is my self portrait.</p>
        </figcaption>
    </figure>
    ```

    A: 正确

     `<figure>` 标签规定独立的流内容（图像、图表、照片、代码等等）。`figure` 元素的内容应该与主内容相关，但如果被删除，则不应对文档流产生影响。使用`<figcaption>`元素为`figure`添加标题（caption）。

4. Q: 哪种情况下应该使用`small`标签？当你想在`h1` 标题后创建副标题？还是当在`footer`里面增加版权信息？

    A: `small`标签一般使用场景是在版权信息和法律文本里使用，也可以在标题里使用标注附加信息（bootstrap中可见），但不可以用来创建副标题。

    > The HTML Small Element (`<small>`) makes the text font size one size smaller (for example, from large to medium, or from small to x-small) down to the browser's minimum font size.  In HTML5, this element is repurposed to represent side-comments and small print, including copyright and legal text, independent of its styled presentation.

5. Q: 在一个结构良好的web网页里，多个`h1`标签会不利于SEO吗？

    A: 不影响。

    > According to Matt Cutts (lead of Google's webspam team and the de facto expert on these things), using multiple `<h1>` tags is fine, as long as you're not abusing it (like sticking your whole page in an `<h1>` and using CSS to style it back to normal size). That would likely have no effect, and might trigger a penalty, as it looks spammy.

    > If you have multiple headings and it would be natural to use multiple `<h1>`'s, then go for it.

    摘自：<http://www.quora.com/Does-using-multiple-h1-tags-on-a-page-affect-search-engine-rankings>

6. Q: 如果你有一个搜索结果页面，你想高亮搜索的关键词。什么HTML 标签可以使用?

    A:`<mark>` 标签表现高亮文本。

    > The HTML `<mark>` Element represents highlighted text, i.e., a run of text marked for reference purpose, due to its relevance in a particular context. For example it can be used in a page showing search results to highlight every instance of the searched for word.

7. Q: 下列代码中`scope` 属性是做什么的？

    ```html
    <article>
        <h1>Hello World</h1>
        <style scoped>
            p {
                color: #FF0;
            }
        </style>
        <p>This is my text</p>
    </article>

    <article>
        <h1>This is awesome</h1>
        <p>I am some other text</p>
    </article>
    ```

    A: `scoped` 属性是一个布尔属性。如果使用该属性，则样式仅仅应用到 style 元素的父元素及其子元素。

8. HTML5 支持块级超链接吗？例如：

    ```html
    <article>
        <a href="#">
            <h1>Hello</h1>
            <p>I am some text</p>
        </a>
    </article>
    ```
    A: 支持。

    HTML5中`<a>` 元素表现为一个超链接，支持任何行内元素和块级元素。

9. Q: 当下列的HTML代码加载时会触发新的HTTP请求吗？

    ```html
    <img src="mypic.jpg" style="visibility: hidden" alt="My picture">
    ```

    A: 会哇

10. Q: 当下列的HTML代码加载时会触发新的HTTP请求吗？

    ```html
    <div style="display: none;">
        <img src="mypic.jpg" alt="My photo">
    </div>
    ```

    A: 会！

11. `main1.css`一定会在`alert('Hello world')`被加载和编译吗?

    ```html
    <head>
        <link href="main1.css" rel="stylesheet">
        <script>
            alert('Hello World');
        </script>
    </head>
    ```

    A: 是！

12. Q: 在`main2.css`获取前`main1`一定必须被下载解析吗？

    ```html
    <head>
        <link href="main1.css" rel="stylesheet">
        <link href="main2.css" rel="stylesheet">
    </head>
    ```

    A: no!

13. Q: 在`Paragraph 1`加载后`main2.css`才会被加载编译吗？

    ```html
    <head>
        <link href="main1.css" rel="stylesheet">
    </head>
    <body>
        <p>Paragraph 1</p>
        <p>Paragraph 2</p>
        <link href="main2.css" rel="stylesheet">
    </body>
    ```

    A: yes!

## 知识点总结

  - 浏览器如何渲染，可以查阅如下文章：
    + [浏览器的渲染原理简介](http://coolshell.cn/articles/9666.html)
    + [专题：浏览器:渲染重绘、重排两三事](http://developer.51cto.com/art/201311/418133.htm)
    + [浏览器加载和渲染HTML的顺序以及Gzip的问题](http://www.nowamagic.net/academy/detail/48110160)
    + [从FE的角度上再看输入url后都发生了什么](http://div.io/topic/609)

  - HTML5 方便的资料可阅读：
    + [你需要知道的五个HTML5新功能](http://www.html5cn.org/article-6180-1.html)
    + [必须知道的七个HTML5新特性](http://camnpr.com/archives/must-know-the-seven-html5-features.html)

