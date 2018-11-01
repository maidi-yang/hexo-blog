---
title: thinkphp5导出excel
date: 2018-11-01 17:59:38
tags: [php,Thinkphp5]
categories: [技术,php]
---

需要用到phpoffice/phpspreadsheet拓展
在composer.json添加

或者:`composer require phpoffice/phpspreadsheet`

````
use PhpOffice\PhpSpreadsheet\Spreadsheet;
use PhpOffice\PhpSpreadsheet\Writer\Xlsx;
````
前台数据传递 ：
````
$('#excel').click(function () {
    var rank = $('#rank').val();
    var special_rank = $('#special_rank').val();
    var start_pv = $('#start_pv').val();
    var end_pv = $('#end_pv').val();
    var keyword = $('#keyword').val();
    var url = $(this).attr('ex_url');

    window.open(url+"?rank="+rank+"&special_rank="+special_rank+"&start_pv="+start_pv+"&end_pv="+end_pv+"&keyword="+keyword);
});

````
后台打印：
````
$result = 数据;

$spreadsheet = new Spreadsheet();
$sheet = $spreadsheet->getActiveSheet();

$grade = config('grade.grade');

$headLetter = ['A','B','C'];
$headName = ['id','真实姓名','身份证号'];

foreach($headLetter as $k => $v){
    $sheet->setCellValue($v.'1', $headName[$k]);、
//自动宽度
   $sheet->getColumnDimension($v)->setAutoSize(true);
}

$j = 2;
foreach ($result['data'] as $k => $v) {
    $sheet->setCellValue('A' . $j, $v['id'])
        ->setCellValue('B' . $j, $v['name'])
        ->setCellValue('C' . $j, $v['id_card'])
    $j++;
}

//请求列表数据接口
//$result = sendGetApi('userExcelList');

//设置文件保存的命名、编码、同时开放保存路径的权限
//命名
$fileName = "打印文件" . date("Y-m-d", time()) . ".xlsx";
//编码
header('Content-Type:application/vnd.ms-excel;charset=utf-8');
//告诉浏览器要输出的名称
header("Content-Disposition:attachment;filename=$fileName");
//禁止浏览器缓存
header('Cache-Control:max-age=0');
//生成excel文件
$writer = new Xlsx($spreadsheet);
//彻底开放保存路径
$writer->save('php://output');
exit();
````
去除科学记数法：在数字字符串前加一个空格使之成为字符串
````
$objPHPExcel = new PHPExcel();
$objPHPExcel->setActiveSheetIndex(0);
$objPHPExcel->getActiveSheet()->setTitle('Simple');$objPHPExcel->getActiveSheet()->setCellValue('D1', ' ' . 123456789033);
````