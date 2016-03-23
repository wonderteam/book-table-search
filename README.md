# TableSearch组件文档

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
    //app为组件实例的引用，一些实例方法可以通过它来调用，比如app.refresh.
    var app = tableSearchPage.init({
        //组件容器
        container: '#container',
        //数据请求地址
        url: '/community/ajaxGetPostList',
        //默认显示页号
        page: 1,
        //列配置
        columns: [
            {
                //此列显示标题
                header: 'ID',
                //对应数据字段
                dataIndex: 'post_id',
                //此列可排序
                sortable: true,
            },
            'update_time',
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
                //不使用dataIndex中的数据显示，自己拼装内容
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
                //显示为按钮
                buttons:[{
                    //按钮上显示的文字
                    text: '全文',
                    //按钮类型(也就是按钮颜色)
                    type: 'info',
                    //点击按钮触发的操作
                    click: function(rowData, columnConfig, evt){
                        var action = '/community/getPostDetail?post_id='+rowData.post_id;
                        location.href = action;
                    }
                }]
            },
        ],
        //搜索表单配置
        searchFilter: {
            //显示为一个输入框
            "post_id":{"label":"ID"}, 
            "topic_id":{"label":"话题ID"}, 
            //显示为一个下拉列表，默认选择default_value中指定项
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
        
        //批量操作按钮
        batOp:[{
            text:'删除选中行',
            type: 'danger',
            click: function(rows){
                alert('确认要删除这'+rows.length+'条记录?');
            }
        }],
        //组件支持的事件监听
        listeners:{
            //当某一个单元格被点击时，触发cellclick事件
            'cellclick': function(row, colCfg, evt){
                if(colCfg.dataIndex=="post_id"){
                    $('#container input[name="post_id"]').val(row.post_id);
                    this.refresh();
                }
            }
        }
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
__必须__。表格列的配置，为一个数组。数组中的每一项，决定了某一列显示数据及显示方法。各参数如下：
1. header，表头标题；
2. dataIndex，要显示的数据字段名；
3. render，此列内容渲染函数。因为dataIndex指定的数据来自ajax返回，显示的时候想做一些额外的加工，（比如一个图片地址，变成图片html），就可以使用render。它处理完数据，返回想要的html字符串。它接收的参数：
  1. rowData, 当前行的数据
  2. columnConfig, 当前列在columns中的配置 

4. sortable, 当前列是否支持排序，默认为false。sortable为true时，必须配置dataIndex，它会做sort_key，随url一起请求数据；
5. checkbox, 数组。如果有这个配置，此列显示为复选框。如：
  ```
  columns:[
        {
            header: '配件',
            dataIndex: 'addons',
            checkbox: ['shoes', {label:'帽子',value:'hat'},{label:'手套', value:'gloves'}]
        },
        ...
  ]
  ```
  其中，dataIndex对应的数据(为数组)，决定了哪些复选框被选中。如果label和value一样的话，可以缩写成字符串，比如checkbox:[{label:'shoes', value:'shoes'}]，可以写成checkbox:['shoes']
6. radio: 数组，可选。如果有这个配置，此列显示为单选框。如：
  ```
  columns:[
        {
            header: '颜色',
            dataIndex: 'addons',
            radio: ['black', {label:'白色',value:'white'}]
        },
        ...
  ]
  ```
  其中，dataIndex对应的数据，决定了哪个单选框被选中。如果label和value一样的话，可以缩写成字符串，radio:[{label:'black', value:'black'}]，可以写成radio:['black']
7. buttons, 数组。如果有这个配置，此列显示为按钮。 如：
  ```
  columns:[
        {
            header: '操作',
            dataIndex: '',
            buttons:[{
                text: '隐藏',
                type: 'danger',
                cls: 'btn-del',
                visible:function(row)
                {
                    if(row.status != "0")
                        return true;
                    return false;
                },
                click: function(rowData, columnConfig){
                    alert(rowData.comment_id);
                }
            }]
        },
        ...
  ]
  ```
 按钮的参数有：
 
 1. text, 按钮上的文字；
 2. type, 按钮的类型，也是按钮的颜色，可取值的有default\primary\success\info\warning\danger\link，默认值为default
   ![](https://s.wandougongzhu.cn/s/b6/button_e7e4b6.png)
 3. cls, 按钮上附加的样式。多个样式时，用空格隔开。如cls:'t-btn big-btn del-btn'
 4. visible, 决定按钮是否显示，默认为true，显示。有两种配置方式，如果为数组，比如visible:['type', 'big']，就检测此行的数据中，type值是不是'big'，是则显示，否则隐藏；如果为函数，就根据函数的返回值，决定是否显示，true为显示，否则隐藏。
 5. click，点击按钮时触发的函数。它有三个参数：
    * rowData, 按钮所在行的原始数据
    * columnConfig，按钮所在列的column配置
    * evt, 点击事件对象
8. width, 此列宽度。取值如'150px', '30%'
9. style, 此列表头的css样式，如{background:'#eee'}
10. cls, 此列附加的样式，多个样式时，用空格隔开。如cls:'hl strip-line'

#### 简写
如果配置项只有header和dataIndex，并且它们值相同，就可以直接一个header的值就行了。比如：
```
[{header:'name', dataIndex:'name'},{header:'性别', dataIndex:'sex'}]
简写为
['name',{header:'性别', dataIndex:'sex'}]
```
#### 到底显示什么内容
是不是发现，一个单元格显示什么内容，有好几个变量都能决定(dataIndex\render\checkbox\radio\buttons),它们几个同时存在时，渲染谁呢？代码里它们是这样的：

```
buttons>checkbox>radio>render>dataIndex
```
所以，同时配置了buttons和checkbox，只会显示button，不会显示checkbox。
![](http://s.wandougongzhu.cn/s/6e/render_c23c6e.png)

### page
初始显示的页号，默认为1.

### searchFilter
配置顶部的搜索表单。目前支持输入框和下拉框两种。如：
```
searchFilter: {
    //显示为一个输入框
    "post_id":{"label":"ID"}, 
    "topic_id":{"label":"话题ID"}, 
    //显示为一个下拉列表，默认选择default_value中指定项
    "is_del":{
        default_value: '-1',
        label: '删除状态',
        list: {
            '-1': '全部',
            '0': '正常',
            '1': '已删除',
        }
    }
}
```
目前每一个表单项都是以key-value形式配置的，其中key会用作表单项的name，value是个json，它的数据有：

* label，此表单项显示的名字
* list，json数据，如果有此字段，这个表单项显示为下拉框，其中下拉框的取值由list里的配置决定
* default_value，如果是输入框，它就是输入框中默认显示的内容；如果是下拉框，它就决定了哪项被选中。

### batOp
批量操作配置。批量操作显示为左下角的批量操作按钮，它的配置项跟column中的buttons配置是一样的，唯一不同的是，click函数的传入参数：
1. rowsData，数组，选中的所有行的数据；
2. evt，点击事件对象

### listeners
tableSearch组件支持一些事件，想监听这些事件，需要配置listeners, demo请参考上面。它支持的事件有：
* ready, tableSearch组件初化合完成之后触发，此时dom已经可用。注意，container是可用的，但表格中的数据可能是不可用的，因为它是以ajax方式请求的。
* refresh，切换页面、搜索、或调用接口刷新表格时，都会触发这个事件。它有一个参数，就是新页面的数据
* rowclick, 某一行被点击时触发。它有两个参数，一是rowData，此行的数据；二是evt，点击事件对象
* cellclick，某一单元格被点击时触发。它有三个参数，一个rowData，此行的数据；二是columnConfig，此列的配置；三是evt，点击事件对象。


