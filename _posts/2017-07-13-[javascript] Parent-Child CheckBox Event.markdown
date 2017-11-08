---
layout: post
title: "[javascript] Parent-Child CheckBox Event"
date:   2017-07-13 09:00:00 +0900
categories: JAVASCRIPT
---

# jQuery

## 기본 사용법

## CASE별 Practice

### 체크박스 선택 관련 (상위/하위)
~~~html
<table cellspacing="0" border="1" style="width:100%;">
    <colgroup>
        <col width="130px" />
        <col width="*" />
    </colgroup>
    <tbody>
        <tr>
            <th>1 DEPTH <input type="checkbox" name="1depth" checked="checked" class="1depth"></th>
        <tr>
            <th>2 DEPTH <input type="checkbox" checked="checked" class="itemList 2depth"></th>
            <td colspan="6">
                <input type="checkbox" checked="checked" class="item 3depth" value="value1"> 1번
                <input type="checkbox" checked="checked" class="item 3depth" value="value2"> 2번
                <input type="checkbox" checked="checked" class="item 3depth" value="value3"> 3번
                <input type="checkbox" checked="checked" class="item 3depth" value="value4"> 4번
                <input type="checkbox" checked="checked" class="item 3depth" value="value5"> 5번
            </td>
        </tr>
    </tbody>
</table>
~~~

~~~javascript

_initCheckBox : function () {
    var checkBoxList = [
        {upperClass : ".1depth", subClass: [".2depth", ".3depth"]}
    ];

    // underscore
    _.each(checkBoxList, function (classInfo) {
        this.initCheckBoxEvent(classInfo);
    })
},

initCheckBoxEvent : function (classInfo) {
    /** 상위 체크박스 이벤트 설정 */
    $(classInfo.upperClass).on('click', classInfo, onClickUpperCheckBoxEvent);

    /** 하위 체크박스 이벤트 설정 (underscore) */
    _.each(classInfo.subClass, function (subClass) {
        $('#form').on('click', subClass, {"upperClass" : classInfo.upperClass, "subClass" : subClass}, bill.common.util.onClickSubCheckBoxEvent);
    });
},

/**
* 상위 체크박스 선택시 하위체크 박스 모두 변경
*/
onClickUpperCheckBoxEvent : function(event) {
    // (underscore)
    _.each(event.data.subClass, function (subClass) {
        $(subClass).prop("checked", event.target.checked);
    })
},

/**
* 하위 체크박스 클릭시 상위체크박스 변경
* (하위 체크박스 전체 선택 여부에 따른 상위체크 박스의 선택 여부 변경)
**/
onClickSubCheckBoxEvent : function(event) {
    $(event.data.upperClass).prop("checked", ($(event.data.subClass).length == $(event.data.subClass + ":checked").length) ? true : false);
},
~~~
