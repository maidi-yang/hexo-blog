---
title: 微信公众号迁移openid转换
date: 2018-11-26 17:48:07
tags: [微信公众号]
categories: [技术,微信]
---

## 获取原微信公众号用户ID
用微信公众号获取用户列表api

[https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140840](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140840)

或者在数据查询
````
$MemberModel = new MemberModel();
$openid = $MemberModel->order('id','asc')->limit(300,100)->column('wechat_openid');
````
## 获取新公众号accessToken
官方api文档
[https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140183](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1421140183)

php获取代码
````
$ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, 'https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=appid&secret=appsecret');
        curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, FALSE);
        curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, FALSE);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        $output = curl_exec($ch);
        curl_close($ch);
        $jsoninfo = json_decode($output, true);
````

## 获取新公众号openid
官方说明文档
[http://kf.qq.com/faq/170221aUnmmU170221eUZJNf.html](http://kf.qq.com/faq/170221aUnmmU170221eUZJNf.html)

php获取代码
````
//获取新openid
        $arr = array(
            'from_appid'=>'原公众号appid',
            'openid_list'=>["oEmYbwN-n24jxvk4Sox81qedINkQ","oEmYbwH9uVd4RKJk7ZZg6SzL6tTo"],
        );


        $data_string =  json_encode($arr);
        //echo $data_string;
        //curl验证成功
        $ch = curl_init("http://api.weixin.qq.com/cgi-bin/changeopenid?access_token=".$jsoninfo['access_token']);
        curl_setopt($ch, CURLOPT_CUSTOMREQUEST, "POST");
        curl_setopt($ch, CURLOPT_POSTFIELDS,$data_string);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER,true);
        curl_setopt($ch, CURLOPT_HTTPHEADER, array(
            'Content-Type: application/json',
            'Content-Length: ' . strlen($data_string)
        ));

        $result = curl_exec($ch);
        if (curl_errno($ch)) {
            print curl_error($ch);
        }
        curl_close($ch);
        $result = json_decode($result, true);
````
需要注意错误返回，一般只有63178这个错误码
原因有多样：

1.传入参数有误，openid_list数目过多（最多100条）

2.没有使用post json格式传参

## 把新openid写入数据库

````
foreach ($result['result_list'] as $k=>$v){
            if($v['ori_openid'] && $v['new_openid']) {
                $update = $MemberModel->where('wechat_openid', $v['ori_openid'])->update(['wechat_openid' => $v['new_openid']]);
            }
        }
````