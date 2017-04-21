Title: Auto-Scroll mit jQuery
Published: 2017-04-21
Image: ../images/blog-title-8.jpg
Tags: [javascript, jquery]
---

```css
#header,#footer,#content {
  position:absolute;
  right:0;
  left:0;
  background:#F00;
}

#header {
  top:0;
  height:100px;
}

#footer {
  bottom:0;
  height:50px; 
}

#content {
  top:100px;
  bottom:50px;
  background:#FFF;
}

#scrolling {
  height:100%;
  overflow:auto;
}
```

```html
<div id="header"></div>
<div id="content">
  <div id="scrolling">
    <!-- Hier viel Content einfügen -->
  </div>
</div>
<div id="footer"></div>
```

```javascript
scrollToBottom() {
    var content = $('#scrolling');
    var currentBottom = content.scrollTop() + content.height();
    var scrollHeight = content[0].scrollHeight;

    if (currentBottom >= scrollHeight - 50) {
        content.scrollTop(scrollHeight);
    }
}
```