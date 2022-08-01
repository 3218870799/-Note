实例：

```js
	tableData = $("#positionChangeTable").DataTable({
		"data": tdList,
		"destroy": true,//每次初始化之前，先销毁之前的
		"searching": false,//是否开启搜索功能
		"ordering":  false,//是否开启排序
		"lengthChange": true,//是否开启转换分页条数
		"pageLength": 100,//默认分页条数
		"scrollX": true,//是否显示横坐标
		"scrollY": height,//是否显示纵坐标，且指定表格高度
		"scrollCollapse": false,//如果数据量少，是否允许缩小表格高度
		"autoWith": true,
		"select": {
			"style": "single",
			"info": false
		},
		"fixedColumns": { //固定列
			leftColumns:2
		},
		"columnDefs": [ {
			"targets": 0,
			"render": function (data, type, row, meta) {
				return "<div style='word-wrap: break-word;word-break: break-all;'>"+data+"</div>";
			}
		},
		{
        			"targets": [7,10,13,16,19,22,25,28,31,34],
        			"render": function (data, type, row, meta) {
        			    if(data>0){
        				    return "<font color='#FF0000'>"+data+"</font>";
        				}else{
        				    return "<font color='#008000'>"+data+"</font>";
        				}
        			}
        }
        ],
		"language": {//语言
			"sProcessing": "处理中...",
			"sLengthMenu": "显示 _MENU_ 项结果",
			"sZeroRecords": "没有匹配结果",
			"sInfo": "显示第 _START_ 至 _END_ 项结果，共 _TOTAL_ 项",
			"sInfoEmpty": "显示第 0 至 0 项结果，共 0 项",
			"sInfoFiltered": "(由 _MAX_ 项结果过滤)",
			"sInfoPostFix": "",
			"sSearch": "搜索:",
			"sUrl": "",
			"sEmptyTable": "表中数据为空",
			"sLoadingRecords": "载入中...",
			"sInfoThousands": ",",
			"oPaginate": {
				"sFirst": "首页",
				"sPrevious": "上页",
				"sNext": "下页",
				"sLast": "末页"
			},
			"oAria": {
				"sSortAscending": ": 以升序排列此列",
				"sSortDescending": ": 以降序排列此列"
			}
		}
	}).page(pageNum);

	if(isIE()){
		$(".DTFC_LeftBodyLiner").css("overflow","hidden");
	}
}
```

列使用保留位数与千分位

```js
		"columnDefs": [
        {
            "targets": [5,6,8,9,11,12,14,15,17,18,20,21,23,24,26,27,29,30,32,33],
            "render": function (data, type, row, meta) {
                    return Number(data).toFixed(2).toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
            }
        }]
```

