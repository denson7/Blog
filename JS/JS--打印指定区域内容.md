JS打印指定区域内容：

```javascript

<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>打印指定区域</title>
</head>
<body>
<div id="top">
    <table cellspacing="0" cellpadding="5" border="1" width="400">
        <thead>
        <tr>
            <td>序号</td>
            <td>用户组别</td>
            <td>用户姓名</td>
            <td>登录帐号</td>
        </tr>
        </thead>
        <tbody>
        <tr>
            <td>1</td>
            <td>214</td>
            <td>John</td>
            <td>20161442</td>
        </tr>
        <tr>
            <td>2</td>
            <td>214</td>
            <td>Tom</td>
            <td>20161441</td>
        </tr>
        <tr>
            <td>3</td>
            <td>214</td>
            <td>Scott</td>
            <td>20161440</td>
        </tr>
        </tbody>
    </table>
</div>
<div>隔断内容</div>
<!--startprint-->
<div id="left">
    <table id="tbcompare" cellspacing="0" cellpadding="0" border="1">
        <tbody>
        <tr>
            <td>Rig Model</td>
            <td>Your Rig</td>
        </tr>
        <tr>
            <td>Hook Load</td>
            <td></td>
            <td><input name="hook_load" value="" type="hidden"></td>
        </tr>
        <tr>
            <td>Drawworks HP</td>
            <td></td>
            <td><input name="dh" value="" type="hidden"></td>
        </tr>
        </tbody>
    </table>
</div>
<!--endprint-->
<script type="text/javascript">
  function preview() {
    var bdhtml = window.document.body.innerHTML;//获取当前页的html代码
    var startStr = "<!--startprint-->";//设置打印开始区域
    var endStr = "<!--endprint-->";//设置打印结束区域
    var printHtml = bdhtml.substring(bdhtml.indexOf(startStr) + startStr.length, bdhtml.indexOf(endStr));//从标记里获取需要打印的页面

    window.document.body.innerHTML = printHtml;//需要打印的页面
    window.print();
    window.document.body.innerHTML = bdhtml;//还原界面
  }


</script>
<input onclick="javascript:preview()" id="Print" value="Print" type="button">
</body>
</html>
```