## 动态添加js,css文件
```js
// 动态加载js,css文件
export const loadJsCssFile = (filename, filetype) => {
  //如果文件类型为 .js ,则创建 script 标签，并设置相应属性
  if (filetype === "js") {
    var fileref = document.createElement('script');
    fileref.setAttribute("type", "text/javascript");
    fileref.setAttribute("src", filename);
  } else if (filetype === "css") {
    //如果文件类型为 .css ,则创建 script 标签，并设置相应属性
    var fileref = document.createElement("link");
    fileref.setAttribute("rel", "stylesheet");
    fileref.setAttribute("type", "text/css");
    fileref.setAttribute("href", filename);
  }
  if (typeof fileref != "undefined") {
    document.getElementsByTagName("head")[0].appendChild(fileref);
  }
};

// 使用：
import { removeJsCssFile, loadJsCssFile} from '@utils/function'     
loadJsCssFile('//xx.xx.com/xx/xx/xx/css/xx.css', 'css');
```

## 动态删除js,css文件
```js
// 动态删除js,css文件
export const removeJsCssFile = (filename, filetype) => {
  // 判断文件类型
  let targetEle = '';
  targetEle = (filetype === "js") ? "script" : (filetype === "css") ? "link" : "none";
  // 判断文件名
  let targetAttr= '';
  targetAttr = (filetype === "js") ? "src" : (filetype === "css") ? "href" : "none";
  // 获取所有的js或css文件
  const allFiles = document.getElementsByTagName(targetEle);
  const len = allFiles.length;
  //遍历所有元素，并删除匹配的目标元素
  for (let i = len; i >= 0; i--) {
    const file = allFiles[i];
    if (file && file.getAttribute(targetAttr) != null && file.getAttribute(targetAttr).indexOf(filename) != -1) {
      file.parentNode.removeChild(file);
    }
  }
};

// 使用：
import { removeJsCssFile, loadJsCssFile} from '@utils/function'
removeJsCssFile("xx.css", "css");
```