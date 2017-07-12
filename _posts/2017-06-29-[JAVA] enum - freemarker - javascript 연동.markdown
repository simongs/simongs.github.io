---
layout: post
title: "[JAVA] enum - freemarker - javascript 연동"
date:   2017-06-29 09:00:00 +0900
categories: JAVA
---

## enum - freemarker - javascript 연동
 - Javascipt에서 Enum 값을 참조하고 싶다.
 - Freemarker를 통해서 Javascipt의 배열로 Enum 정보를 미리 만들어 놓고
 - Javascipt 코드를 통해서 해당 배열에 접근하는 방식으로 참조한다.
 - Enum 은 getText()를 가진 interface를 구현하는 것을 전제로 한다. (TextEnum)

### Java단 소스

~~~java 
package com.dasolute;

public class EnumConstants {
    private static Map<String, Map<String, String>> enumMap = Maps.newHashMap();

    static {
        enumMap.put(ENUM1.class.getSimpleName(), getTextEnumMap(ENUM1.class));
        enumMap.put(ENUM2.class.getSimpleName(), getTextEnumMap(ENUM2.class));
        enumMap.put(ENUM3.class.getSimpleName(), getTextEnumMap(ENUM3.class));
        enumMap.put(ENUM4.class.getSimpleName(), getTextEnumMap(ENUM4.class));
        
        MapUtils.unmodifiableMap(enumMap);
    }

    
   private static Map<String, String> getTextEnumMap(Class<? extends Enum<?>> clz) {
        Map<String,String> nameMap = Maps.newHashMap();
        for(Object obj : clz.getEnumConstants()) {
            TextEnum textEnum = (TextEnum) obj; // Enum implements TextEnum
            Enum en = (Enum) obj;
            nameMap.put(en.name(), textEnum.getText());
        }
        return nameMap;
    }

    public static Map<String, Map<String, String>> getEnumMap() {
        return enumMap;
    }
}
~~~

### Freemarker 단 소스
~~~
<#assign JacksonUtil=statics["com.dasolute.JacksonUtil"]>
<#assign EnumConstants=statics["com.dasolute.EnumConstants"]>

<script type="text/javascript">
    if (typeof bill == "undefined"){ bill = {};}
    if (typeof bill.common == "undefined"){ bill.common = {};}
    if (typeof bill.common.enum == "undefined"){ bill.common.enum = {};}

    bill.common.enum.data = ${(JacksonUtil.toJson(EnumConstants.getEnumMap()))!};
</script>
~~~

### Javascript 단 소스

~~~javascript
if (typeof com == "undefined"){ com = {};}
if (typeof com.dasolute == "undefined"){ com.dasolute = {};}

com.dasolute.formatter = {
	code : {
		getText : function(codeTypeName, code) {
			if(bill.common.enum.data[codeTypeName] && bill.common.enum.data[codeTypeName][code]) {
				return bill.common.enum.data[codeTypeName][code];
			}
			return "";
		}
	}
}
~~~

### Javascript 단 사용소스
~~~
var text = com.dasolute.formatter.code.getText('Enum1', 'code');
~~~