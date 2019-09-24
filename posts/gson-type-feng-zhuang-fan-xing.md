---
title: 'gson type封装泛型'
date: 2019-09-24 16:33:44
tags: [Android,gson]
published: true
hideInList: false
feature: 
---
## type封装获取泛型的实际类型
    
> 通过getGenericSuperclass方法可以获取当前对象的直接超类的 Type
    
getClass().getGenericSuperclass()返回表示此 Class 所表示的实体（类、接口、基本类型或 void）的直接超类的 Type  
然后将其转换ParameterizedType。。  
getActualTypeArguments()返回表示此类型实际类型参数的 Type 对象的数组。  
[0]就是这个数组中第一个了。。简而言之就是获得超类的泛型参数的实际类型。。
```
        Type genericSuperclass = getClass().getGenericSuperclass();
        if (genericSuperclass instanceof ParameterizedType) {
            this.type = ((ParameterizedType) genericSuperclass).getActualTypeArguments()[0];
        } else {
            this.type = Object.class; 
            // 基本不会走这个判断
        }
```     
        
        
## 解决 gson反序列化自动将int类型转化成double类型
```
    private static Gson gson=new GsonBuilder().
        registerTypeAdapter(Double.class, new JsonSerializer<Double>() {

            @Override
            public JsonElement serialize(Double src, Type typeOfSrc, JsonSerializationContext context) {
                if (src == src.longValue())
                    return new JsonPrimitive(src.longValue());
                return new JsonPrimitive(src);
            }
        }).create();
```