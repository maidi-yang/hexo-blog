---
layout: w
title: php 数组驼峰下划线互换
date: 2019-01-30 16:37:09
tags: [php]
categories: [技术,php]
---
下划线转驼峰
````
   function convertUnderline($str)
   {
       $str = preg_replace_callback('/([-_]+([a-z]{1}))/i',function($matches){
           return strtoupper($matches[2]);
       },$str);
       return $str;
   }
````

驼峰转下划线
````
   function humpToLine($str){
       $str = preg_replace_callback('/([A-Z]{1})/',function($matches){
           return '_'.strtolower($matches[0]);
       },$str);
       return $str;
   }
````

数组更换键名
````
   function convertHump($data, $hump = 0){
       $result = [];
       foreach ($data as $key => $item) {
           $keyName = $hump == 0 ? $this->convertUnderline($key) : $this->humpToLine($key);
           if (is_array($item) || is_object($item)) {
               $result[$keyName] = $this->convertHump((array)$item, $hump);
           } else {
               $result[$keyName] = $item;
           }
       }
       return $result;
   }
````
##首字母大写的互换

下划线转驼峰
````
   function convertUnderline($str)
   {
       $str = ucfirst(ucwords(str_replace('_', ' ', $str)));
       $str = str_replace(' ', '', $str);
       return $str;
   }
````

驼峰转下划线
````
   function humpToLine($camelCaps,$separator='_'){
       return strtolower(preg_replace('/([a-z])([A-Z])/', "$1" . $separator . "$2", $camelCaps));
   }
````
