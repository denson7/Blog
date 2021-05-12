## 懒加载
懒加载也叫做延迟加载、按需加载，指的是在长网页中延迟加载图片数据，是一种较好的网页性能优化的方式。
在应用中，如果图片很多，所有的图片都被加载出来，而用户只能看到可视窗口的那一部分图片数据，这样就浪费了性能。
如果我们使用图片的懒加载就可以解决以上问题。在滚动屏幕之前，可视化区域之外的图片不会进行加载，在滚动屏幕时才加载。这样使得网页的加载速度更快，减少了服务器的负载。懒加载适用于图片较多，页面较长的页面场景中。

### 懒加载的优点
1.减少无用资源的加载：使用懒加载明显减少了服务器的压力和流量，同时也减小了浏览器的负担。
2.提升用户体验: 如果同时加载较多图片，可能需要等待的时间较长，这样影响了用户体验，而使用懒加载就能大大的提高用户体验。
3.防止加载过多图片而影响其他资源文件的加载，会影响网站应用的正常使用。

### 懒加载的原理
图片的加载是由src引起的，当对src赋值时，浏览器就会请求图片资源。根据这个原理，我们使用HTML5 的data-xxx属性来储存图片的路径，在需要加载图片的时候，将data-xxx中图片的路径赋值给src，这样就实现了图片的按需加载，即懒加载。
注意：data-xxx 中的xxx可以自定义，这里我们使用data-src来定义。

### 示例
```javascript
//原理：
懒加载的实现重点在于确定用户需要加载哪张图片，在浏览器中，可视区域内的资源就是用户需要的资源。所以当图片出现在可视区域时，获取图片的真实地址并赋值给图片即可。
知识点：
（1）window.innerHeight 是浏览器可视区的高度
（2）document.body.scrollTop || document.documentElement.scrollTop 是浏览器滚动的过的距离
（3）imgs.offsetTop 是元素顶部距离文档顶部的高度（包括滚动条的距离）
（4）图片加载条件：img.offsetTop < window.innerHeight + document.body.scrollTop;

// 示例
<div class="container">
     <img src="loading.gif"  data-src="pic.png">
     <img src="loading.gif"  data-src="pic.png">
     <img src="loading.gif"  data-src="pic.png">
     <img src="loading.gif"  data-src="pic.png">
     <img src="loading.gif"  data-src="pic.png">
     <img src="loading.gif"  data-src="pic.png">
</div>
<script>
var imgs = document.querySelectorAll('img');
function lozyLoad(){
        var scrollTop = document.body.scrollTop || document.documentElement.scrollTop;
        var winHeight= window.innerHeight;
        for(var i=0;i < imgs.length;i++){
            if(imgs[i].offsetTop < scrollTop + winHeight ){
                imgs[i].src = imgs[i].getAttribute('data-src');
            }
        }
    }
  window.onscroll = lozyLoad();
</script>
```
