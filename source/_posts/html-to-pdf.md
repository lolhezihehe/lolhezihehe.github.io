---
title: html2canvas+jspdf实现html转pdf
top: false
cover: false
date: 2020-05-10 18:36:05
categories: 前端
tags: 
- html2canvas
- jspdf
- html2pdf
---
通过html转图片，图片转pdf实现html转pdf，这里以Vue为例
#### html2canvas
首先实现[html2canvas](https://html2canvas.hertzen.com/)转图片
##### 安装
```bash
npm install --save html2canvas
```
##### template
```html
<div id="view">
    <!--something-->
</div>
```
##### js
实现过程遇到了截取的div超出可视区域，根据[stackoverflow](https://stackoverflow.com/questions/36213275/html2canvas-does-not-render-full-div-only-what-is-visible-on-screen)提供的方案解决了这个问题：添加{ scrollY: -window.scrollY }，其他配置可以看[官方文档](https://html2canvas.hertzen.com/)
```js
import html2canvas from 'html2canvas';

export default {
  methods: {
    html2canvas(){
        html2canvas(document.getElementById('view'), { scrollY: -window.scrollY }).then((canvas) => {
            const imgUrl = canvas.toDataURL('image/jepg');
            window.open(imgUrl);
        })
    }
  }
}
```

#### jspdf
然后通过[jspdf](https://parall.ax/products/jspdf)实现图片转pdf，这里还是通过[stackoverflow](https://stackoverflow.com/questions/36472094/how-to-set-image-to-fit-width-of-the-page-using-jspdf)解决了div width铺满pdf的问题，在原有html2canvas添加jspdf代码
```js
import html2canvas from 'html2canvas';
import jsPDF from 'jspdf';

export default {
  methods: {
    html2canvas(){
        html2canvas(document.getElementById('view'), { scrollY: -window.scrollY }).then((canvas) => {
            const imgUrl = canvas.toDataURL('image/jepg');
            // window.open(imgUrl);
            const doc = new jsPDF('p', 'mm', 'a4');
            const width = doc.internal.pageSize.getWidth();
            const height = doc.internal.pageSize.getHeight();
            doc.addImage(imgUrl, 'JPEG', 0, 0, width, height);
            doc.save('view.pdf');
        })
    }
  }
}
```

#### 总结
好好用google和stackoverflow，虽然英文看起来比较吃力，但是解决问题效率真的高很多；