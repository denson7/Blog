### 自定义设置cookie函数
```js
#过滤xss
const _filterXSS = function (e) {
    if (!e) return e;
    for (; e != window.unescape(e);) {
        e = window.unescape(e);
    }
    var r = ["<", ">", "'", '"', "%3c", "%3e", "%27", "%22", "%253c", "%253e", "%2527", "%2522"];
    var n = ["&#x3c;", "&#x3e;", "&#x27;", "&#x22;", "%26%23x3c%3B", "%26%23x3e%3B", "%26%23x27%3B", "%26%23x22%3B", "%2526%2523x3c%253B", "%2526%2523x3e%253B", "%2526%2523x27%253B", "%2526%2523x22%253B"];
    for (var a = 0; a < r.length; a++) {
        e = e.replace(new RegExp(r[a], "gi"), n[a]);
    }
    return e;
};

#设置
const setCookie = function (name, value, expiresd, path, domain, secure) {
    var expdate = new Date();
    var expires = arguments[2] || null;
    var path = arguments[3] || "/";
    var domain = arguments[4] || null;
    var secure = arguments[5] || false;
    if (expires) {
        expdate.setMinutes(expdate.getMinutes() + parseInt(expires));
    }
    var cookietemp = window.escape(name) + '=' + window.escape(value) + (expires ? '; expires=' + expdate.toUTCString() : '') + '; path=' + path + (domain ? '; domain=' + domain : '') + (secure ? '; secure' : '');
    document.cookie = cookietemp;
};
#获取
const getCookie = function (b) {
    var a = document.cookie.match(RegExp("(^|;\\s*)" + b + "=([^;]*)(;|$)"));
    return _filterXSS( a ? window.unescape(a[2]) : null);
};

#删除
const delCookie = function (name) {
    var exp = new Date();
    exp.setTime(exp.getTime() - 1);
    var cval = getCookie(name);//获取cookie
    document.cookie = name + "=" + cval + ";expires=" + exp.toUTCString();
};
```