---
title: php 压缩文件下载
date: 2019-02-20 11:56:13
tags: [php，文件下载，压缩]
categories: [技术,php]
---

压缩文件并下载
````
//拼接获取完整的域名链接
$info = ‘数据’
$httpHost=$httpType.$header['host'];

//检查文件是否存在，存在删除旧文件夹
$dir = Env::get('root_path') .'public/qrcode/goods_'.$id.'/';
if (file_exists($dir)){
    deldir($dir);
}
mkdir ($dir,0777,true);
foreach ($info as $v){
    $url = $httpHost . '/mobile2/GoodsQrcode/writeoff?id=' . $v['id'];
    $qrCode = new QrCode($url);
    $qrCode->writeFile($dir. $v['id'] . '.png');
}

//获取列表
$datalist=list_dir($dir);
$filename = Env::get('root_path') ."public/qrcode/qrcode.zip"; //最终生成的文件名（含路径）

//检查文件是否存在，存在删除旧文件
if (file_exists($filename)) unlink($filename);

//重新生成文件
  $zip = new \ZipArchive();//使用本类，linux需开启zlib，windows需取消php_zip.dll前的注释
  if ($zip->open($filename, \ZipArchive::CREATE)!==TRUE) {
    exit('无法打开文件，或者文件创建失败');
  }
  foreach($datalist as $val){
    if(file_exists($val)){
      $zip->addFile( $val, basename($val));//第二个参数是放在压缩包中的文件名称，如果文件可能会有重复，就需要注意一下
    }
  }
  $zip->close();//关闭

if(!file_exists($filename)){
  exit("无法找到文件"); //即使创建，仍有可能失败。。。。
}

header("Cache-Control: public");
header("Content-Description: File Transfer");
header('Content-disposition: attachment; filename='.basename($filename)); //文件名
header("Content-Type: application/zip"); //zip格式的
header("Content-Transfer-Encoding: binary"); //告诉浏览器，这是二进制文件
header('Content-Length: '. filesize($filename)); //告诉浏览器，文件大小
@readfile($filename);

//下载完成后临时文件夹
deldir($dir);
````
获取文件列表
````
function list_dir($dir){
    $result = array();
    if (is_dir($dir)){
        $file_dir = scandir($dir);
        foreach($file_dir as $file){
            if ($file == '.' || $file == '..'){
                continue;
            }
            elseif (is_dir($dir.$file)){
                $result = array_merge($result, list_dir($dir.$file.'/'));
            }
            else{
                array_push($result, $dir.$file);
            }
        }
    }
    return $result;
}
````

删除一个文件夹和里面所有文件
````
function deldir($path){
    //如果是目录则继续
    if(is_dir($path)){
        //扫描一个文件夹内的所有文件夹和文件并返回数组
        $p = scandir($path);
        foreach($p as $val){
            //排除目录中的.和..
            if($val !="." && $val !=".."){
                //如果是目录则递归子目录，继续操作
                if(is_dir($path.$val)){
                    //子目录中操作删除文件夹和文件
                    deldir($path.$val.'/');
                    //目录清空后删除空文件夹
                    @rmdir($path.$val.'/');
                }else{
                    //如果是文件直接删除
                    unlink($path.$val);
                }
            }
        }
        @rmdir($path);
    }
}
````