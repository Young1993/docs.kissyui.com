# 简单配置包使用

代码：
```
KISSY.use('json, gallery/pageNotification/1.0/index', function(S, JSON, PN) {
    var pn = new PN({
        "closeButton": true,
        "positionClass": "page-notification-top-right",
        "onclick": null,
        "showDuration": "300",
        "hideDuration": "1000",
        "timeOut": "5000",
        "extendedTimeOut": "1000",
        "showEasing": "swing",
        "hideEasing": "linear",
        "showMethod": "fadeIn",
        "hideMethod": "fadeOut"
    });

    window.alert = function() {
        var args = arguments[0];
        args = S.isObject(args) ? JSON.stringify(args) : args.toString();
        pn.success(args);
    };

    KISSY.getScript('assets/simple.js');
});
```