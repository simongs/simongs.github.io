---
layout: post
title: "[javascript] handlebars 템플릿과 레이어팝업"
date:   2017-07-16 09:00:00 +0900
categories: JAVASCRIPT
---

# handlebars

## 레이어 정의 및 handlebars 템플릿 정의
~~~html
<!-- 레이어 division-->
<div class="layer" id="new_layer"  style="width:800px;display:none;z-index:100 ">
    <div class="layer_header">
        <strong class="tit">HEADER</strong>
    </div>
    <div class="layer_content"> 
        <div class="table" id="table" >
		    <table cellspacing="0">
                <col width="20%">
                <col width="35%">
                <col width="10%">
                <col width="35%">
                <thead>
                	<tr>
	                    <th>Colume 1</th>
	                    <th>Colume 2</th>
	                    <th>Colume 3</th>
	                    <th>Colume 4</th>
                	</tr>
                </thead>
		        <tbody id="tempatePlace">
		        </tbody>
		    </table>
		</div>
    </div>
    <div class="layer_footer">
        <a href="javascript:;" class="btn_layer_close"><span>닫기</span></a>
    </div>
</div>

<!-- handlebars template -->
<script id="layer-template" type="text/x-handlebars-template">
        {{#dataList}}
            <tr>
                <td>{{column1}}</td>
                <td>{{column2}}</td>
                <td>{{column3}}</td>
                <td>{{column4}}</td>
            </tr>
        {{/dataList}}
</script>
~~~

## javascript 코드

~~~javascript
/** 레이어 닫기 버튼 초기화 */
$('.btn_layer_close').bind("click", function(e) {
    resetLayer();
});

resetLayer : function() {
    $("#new_layer").hide();
    $("#tempatePlace").html("");
},

/** 클릭 링크마다 생성 */
var clickFunction = 'clickText("' + sequence + '")';
return '<a href="javascript:;" id="layer_"' + sequence + '" onclick="' + clickFunction + '" ><span>항목</span></a>';

/** 링크 클릭시 각 항목을 ajax를 통해 가져온다.*/
clickText : function (sequence, e) {
    var url = AJAX_URL + "/" + sequence;
    util.requestAjax(url, "GET", null, responseLayerAjax);
},

/** 응답받은 결과로 레이어의 데이터를 구성한다. */
responseLayerAjax : function (result) {
    resetLayer();

    var source = $("#layer-template").html();
    var template = Handlebars.compile(source);

    // 데이터 형식
    var data = {
        "dataList" : [
            {column1:"data1", column2:"data2", column3:"data3", column4:"data4"}
        ]
    }
    var html = template(data); // result.data
    $("#tempatePlace").append(html);
    $("#new_layer").show();

    // 클릭한 위치에 따라 레이어의 위치를 변경해준다.
    var offset = $("#layer_" + result.sequence).offset();
    $("#new_layer").offset({top : offset.top + 6, left : offset.left + 25});
},
~~~
