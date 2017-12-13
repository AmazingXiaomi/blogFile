---
title: select2 
date: 2017-11-6 22:43:32
tags: js
---

# select2 实现可搜索下拉框查询

[select2官网地址](https://select2.org/)：https://select2.org/

## 使用select2

```base
页面引入select2的js和css文件
```
1. 设置select2的文字格式为中文
先引入select2.js和select2.css，然后引入中文js
```code
    <script type="text/javascript" src="../static/js/libs/select2/select2.full.min.js"></script>
    <script type="text/javascript" src="../static/js/libs/select2/zh-CN.js"></script>
    <link rel="stylesheet" type="text/css" href="../static/css/select2.min.css">
```

然后在初始化的时候加上 language: 'zh-CN'即可实现汉化.
2. 使用ajax查询select的数据
```code
$('#' + id + '').select2({
            placeholder: "搜索",
            minimumInputLength: 1,
            language: 'zh-CN',
            ajax: {
                url: url,
                dataType: 'json',
                quietMillis: 250,
                data: function (params) {
                    var query = {
                        name:_.trim(params.term)
                    }
                    return query;
                },
                processResults: function (data) {
                    var datas=[];
                    if (data.data){
                        for(key in data.data){
                            var da={};
                            da.id=data.data[key].code;
                            da.text=data.data[key].name;
                            datas.push(da);
                        }
                    }
                    return {
                        results: datas
                    };
                }
            },
        })
``` 

```base
选中默认值问题
```

在使用过程中我们如果需要回显下拉框的数据时，可先将利用ajax获取 select数据，然后利用一下代码根据我们的需要回显的id默认选中
```code
function initDrugName(parm,id) {
        $.ajax({
            url: BASE.urlPrefix + 'base/common/stdDrugName',
            method: 'get',
            data: {
                name: parm
            },
            success: function (data) {
                if (data.code == 0) {
                    var options = BASE.generateOptions(data.data);
                    $("#addStdDrugName").append(options);
                    $("#addStdDrugName").val(id).trigger("change");
                }
            }
        })
    }
```
目前只是初步实现了select2的基本功能！日后再说！