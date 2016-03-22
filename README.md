# TableSearch文档

> 我们不需要高大全的功能，只要够用的、好用的功能。不求最多，但求最好。

**1. 如果你觉得这个文档太长，那就不要读完它，把例子抄下来改就是了**

**2. 使用者只用根据文档修改不同的配置参数就可以了，不涉及其它的东西**

起因：因为管理后台有很多表格的展示，在表格基础上又衍生出搜索、分页、排序、按钮、批量操作等功能，如果每个页面都写一堆js来应付这些功能，也是挺ugly的。因此这个TableSearch组件有了诞生的理由。

## 主要功能
先上图：
![](http://s.wandougongzhu.cn/s/dd/demo1_f90add.jpg)

从图上看到的，功能分别为：

1. 列表显示
2. 分页
3. 搜索
4. 批量管理
5. 行内按钮
6. 列排序

## 使用方法
只要三步：

1. 引用js代码：
  
  ```
  <script src="/resource/js/dist/tableSearchPage_v2.js"></script>
  ``` 

2. 在页面给TableSearch组件留个坑，比如下面的id为“container”的div
  ```
  <div id="container"></div>
  ```
  
3. 通过js初始化这个组件，示例：
  ```
  $(function(){
    var app = tableSearchPage.init({
        container: '#container',
        url: '/community/ajaxGetPostList',
        page: 1,
        columns: [
            {
                header: 'ID',
                dataIndex: 'post_id',
                sortable: true,
            },
            {
                header: '首图',
                dataIndex: '',
                render: function (row){
                    return "<img src='" + row['img_small'] + "' height='30' />";
                },
            },
            {
                header: '作者',
                dataIndex: '',
                render: function (row){
                    msg = '[uid]:' + row['user_id'];
                    msg += "<br/>";
                    msg += '<a href="'+row['user_id']+'">' + row['user_info']['display_name'] + '</a>';
                    return msg;
                },
            },
            {
                header: '浏览',
                dataIndex: 'pv_cnt',
                sortable: true,
                render: function (row){
                    return row['pv_cnt'];
                },
            },
            {
                header: '点赞',
                dataIndex: 'dig_cnt',
                sortable: true,
                render: function (row){
                    return row['dig_cnt'];
                },
            },
            {
                header: '操作',
                dataIndex: '',
                buttons:[{
                    text: '全文',
                    type: 'info',
                    click: function(item, columnConfig){
                        var action = '/community/getPostDetail?post_id='+item.post_id;
                        var host   = window.location.host;
                        var url    = 'http://'+ host + action;
                        location.href = url;
                    }
                }]
            },
        ],

        searchFilter: {
            "post_id":{"label":"ID"}, 
            "topic_id":{"label":"话题ID"}, 
            "is_del":{
                default_value: '-1',
                label: '删除状态',
                list: {
                    '-1': '全部',
                    '0': '正常',
                    '1': '已删除',
                }
            }
        },
        listeners:{
            'cellclick': function(row, colCfg, evt){
                if(colCfg.dataIndex=="post_id"){
                    $('#container input[name="post_id"]').val(row.post_id);
                    this.refresh();
                }
            }
        },
        batOp:[{
            text:'删除选中行',
            type: 'danger',
            click: function(rows){
                alert('确认要删除这'+rows.length+'条记录?');
            }
        }]
    });
});
  ```

## 配置参数
### container
__必须__。tableSearch组件所在dom元素，可以是dom元素、css选择器、jquery元素，如：

```
container:'.container'
container:document.getElementById('container')
container:$('.container')
```

### url
__必须__。列表请求的ajax接口。请求此url时会带上页号page，如果配置了搜索，也会带上搜索参数。如果排序了，还要加上排序信息。可能的信息有：

1. 搜索参数；
2. page,页号
3. sort_key,排序字段，对应columns中的dataIndex
4. sort_direction，排序方向，取值为'asc'和'desc'

返回数据结构要求如下:
```
{
    "errno": 0,
    "errmsg": "ok",
    "data": {
        //list,数组，列表数据，每一组数据显示为表格一行
        "list": [
            {
                "comment_id": "21",
                "user_id": "23",
                "goods_id": "46",
                "content": " \u5927\u5bb6\u597d\u597d\u65b9\u5f0f\u53d1\u9001\u65b9\u662f\u5426\u6492\u53d1\u751f\u6cd5\u8428\u82ac\u6492\u53d1\u653e",
                "ctime": "2016-01-18 14:13:07",
                "mtime": "2016-01-18 14:13:07",
                "status": "0",
                "goods_name": "\u82b1\u738b Laurier S\u7cfb\u5217\u591c\u7528\u77ac\u54381mm\u62a4\u7ffc\u536b\u751f\u5dfe35cm",
                "goods_count": "5",
                "goods_display": "46(5)",
                "goods_img": "http:\/\/ossimg.wandougongzhu.cn\/b2679fdbdc1c8829a0423cbee77bb436.jpg@200w_200h_1l.jpg",
                "user_img": "<img src=\"\" width=\"50\" heigh=\"50\" >",
                "user_display_name": null
            },
            {
                "comment_id": "20",
                "user_id": "1025",
                "goods_id": "16",
                "content": "afasdfasdfasdfasdfasdfasdfasdf",
                "ctime": "2016-01-15 19:24:00",
                "mtime": "2016-01-15 19:24:00",
                "status": "0",
                "goods_name": "\u4f73\u4e3d\u5b9dsuisai\u9175\u6bcd\u6d01\u9762\u7c89 \u53bb\u9ed1\u5934\u6e05\u6bdb\u5b54",
                "goods_count": "0",
                "goods_display": "16(0)",
                "goods_img": "http:\/\/ossimg.wandougongzhu.cn\/4bb14337d5619e843caf44a05a5f4fc2.jpg@200w_200h_1l.jpg",
                "user_img": "<img src=\"\" width=\"50\" heigh=\"50\" >",
                "user_display_name": "18513400425"
            }
        ],
        //page, number, 当前页号
        "page": 1,
        //page_count, number, 总页数
        "page_count": 5
    }
```

其中list数组，其中每一条数据对应表格的一行，page为当前页号,page_count为总页数。

### columns
### page
初始显示的页号，默认为1.


