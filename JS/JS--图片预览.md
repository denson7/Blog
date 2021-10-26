## 本地图片预览
### FileReader API
```
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8"/>
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>图片本地预览</title>
</head>
<body>
  <h3>图片本地预览</h3>
  <input type="file" accept="image/*" onchange="loadFile(event)"/>
  <img id="previewContainer"/>
<script>
  const loadFile = function (event) {
    const reader = new FileReader();
    reader.onload = function () {
      const output = document.querySelector("#previewContainer");
      output.src = reader.result;
    };
    // FileReader 对象，除了可以把 File/Blob 对象转换为 Data URL 之外，它还提供了 readAsArrayBuffer() 和 readAsText() 方法，用于把 File/Blob 对象转换为其它的数据格式
    reader.readAsDataURL(event.target.files[0]);
  };
</script>
</body>
</html>
```
## 网络图片预览
```
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>远程图片预览</title>
</head>
<body>
<h3>获取远程图片预览</h3>
<img id="previewContainer" style="width: 50%;"/>

<script>
  const image = document.querySelector("#previewContainer");

  // 把响应对象（Response）转换为 Blob 对象，再用 URL.createObjectURL 方法，创建 Object URL 并把它赋给 img 元素的 src 属性
  fetch("https://avatars2.githubusercontent.com/u/14807241")
    .then((response) => response.blob())
    .then((blob) => {
      const objectURL = URL.createObjectURL(blob);
      image.src = objectURL;
    });
</script>
</body>
</html>
```
## Object URL 说明
`Object URL` 是一种伪协议，也被称为 Blob URL。它允许 Blob 或 File 对象用作图像，下载二进制数据链接等的 URL 源。
在浏览器中，我们使用 URL.createObjectURL 方法来创建 Blob URL，该方法接收一个 Blob 对象，并为其创建一个唯一的 URL。
```
形式为: blob:<origin>/<uuid>
示例：blob:https://xxx.org/40a5fb5a-d56d-4a33-b4e2-0acf6a8e5f641
```
浏览器内部为每个通过 URL.createObjectURL 生成的 URL 存储了一个 「URL → Blob」 映射。因此，此类 URL 较短，但可以访问 Blob。生成的 URL 仅在当前文档打开的状态下才有效。但如果你访问的 Blob URL 不再存在，则会从浏览器中收到 404 错误。
createObjectURL() 方法实际上有副作用，因为浏览器存储了 URL → Blob 的映射，但 Blob 本身仍驻留在内存中，浏览器无法释放它。映射在文档卸载时自动清除，因此 Blob 对象随后被释放。但是，如果应用程序寿命很长，那不会很快发生。因此，如果我们创建一个 Blob URL，即使不再需要该 Blob，它也会存在内存中。
可以调用 `URL.revokeObjectURL(url)` 方法，从内部映射中删除引用，从而允许删除 Blob（如果没有其他引用），并释放内存。


```
<!DOCTYPE html>
<html>

<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <script src="http://seikichi.github.io/tiff.js/tiff.min.js"></script>
  <title>图片本地预览</title>
  <style>
    #drop
  </style>
</head>

<body>
  <h3>图片本地预览</h3>
  <input type="file" accept="image/*" onchange="loadFile(event)" />
  <img id="previewContainer" />

  <div id="drop"></div>
  <script>
    const loadFile = function (event) {
      const reader = new FileReader();
      // FileReader 对象，除了可以把 File/Blob 对象转换为 Data URL 之外，它还提供了 readAsArrayBuffer() 和 readAsText() 方法，用于把 File/Blob 对象转换为其它的数据格式
      reader.readAsDataURL(event.target.files[0]);
      reader.onload = function () {
        const output = document.querySelector("#previewContainer");
        const res = reader.result
        console.log(res)
        output.src = res;
      };

    };


    const elm = document.getElementById("drop")
    const img = document.getElementById("img")
    elm.addEventListener("dragenter", e => e.preventDefault())
    elm.addEventListener("dragover", e => e.preventDefault())
    elm.addEventListener("drop", e => {
      e.preventDefault()
      const file = e.dataTransfer.files[0];
      const reader = new FileReader();
      reader.addEventListener("load", e => {
        const arrayBuffer = e.target.result
        const tiff = new Tiff({ buffer: arrayBuffer })
        // const canvas = tiff.toCanvas() // 使用canvas调此方法
        const imgData = tiff.toDataURL() // 使用base64调此方法
        img.src = imgData
      })
      reader.readAsArrayBuffer(file)
    })
  </script>
</body>

</html>
```
