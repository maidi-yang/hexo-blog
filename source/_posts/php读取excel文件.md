---
title: php读取excel文件
date: 2019-04-10 17:01:35
tags: [php，读取excel]
categories: [技术,php]
---
新的业务需求，需要读取指定文件夹下的excel文件，导入数据库。
用的是box/spout的库
下面是读取代码：
````
public function readCsv(){
		$dirName = ROOT.DS.APP_DIR.DS."/tmp/files"; //遍历这个文件夹
		$usedDir = ROOT.DS.APP_DIR.DS."/tmp/UsedFiles"; //遍历完后移动的文件夹
		if (!file_exists($usedDir)) {
			mkdir($usedDir, 0777, true);
		}
		$handle1 = opendir($dirName);
		$csvData = [];

		//遍历文件夹
		while(($res = readdir($handle1)) !== false){
			if($res != '.' && $res != '..') {
				//如果是文件，提出文件内容，写入目标文件
				if (is_file($dirName . '/' . $res)) {
					$option = array('delimiter' => '', 'enclosure' => '', 'header' => true);
					$hkjcfile = $dirName . '/' . $res;
					$suffix = substr($res, strrpos($res, '.') + 1);
					$fileName = substr($res, 0, strrpos($res, '.'));
					//使用box/spou库
					list($imp, $status) = fileImport($hkjcfile, '', $suffix, $option);
					if ($status) {
						if (empty($imp)) {
							echo $hkjcfile . 'File content is empty';
						}

						foreach ($imp as $k => $v) {
							if (count($v) > 1 and !empty($v[1])) {
								$csvData[] = [
									'cardID' => isset($v[0]) ? $v[0] : '',
									'badgeID' =>isset($v[1]) ? $v[1] : '',
								];
							} else {
								echo  $hkjcfile . 'Data_missing';
							}
						}
                        //调用存入数据库的方法
						$returnDate = $this->importSave($csvData, $fileName);
						if(is_array($returnDate)) {
							echo $res . ' Results of the ' . 'deleted rows:' . $returnDate['delectRows'] . '.' . 'update rows:' . $returnDate['updateRows'] . '.' . 'add rows:' . $returnDate['addRows'] . '.' . 'deleted error rows:' . $returnDate['delectErrorRows'] . '.' . 'update error rows:' . $returnDate['updateErrorRows'] . '.' . 'add error rows:' . $returnDate['addErrorRows'] . '.' . 'ignore rows:' . $returnDate['ignoreRows'];

							copy($dirName . '/' . $res, $usedDir . '/' . $res); //拷贝到新目录
							unlink($dirName . '/' . $res); //删除旧目录下的文件
						}else{
							echo $returnDate;
						}

					} else {
						echo $hkjcfile . 'File Content Reading Failed';
					}
				}
			}
		}
		echo true;
````

调用box/spout的库代码：
````
use Box\Spout\Reader\ReaderFactory;
use Box\Spout\Common\Type;
    /**
     * csv/xlsx上传文件处理器
     * @param string $fileName
     * @param array $fieldvalidate 字段数据验证
     * @param string $t
     * @param array $option
     * @throws code 101 type format error
     * @throws code 102 {$field} field colum not does exist
     * @throws code 103 {$rowIndex} lines {$field} field is empty
     * @return array
     */
    public function fileImport($fileName, $fieldvalidates = [], $t = 'xlsx', $option = []) {
        if (empty($fileName)) return ['name is empty',false];
        switch ($t) {
            case 'xlsx': $type = Type::XLSX;break;
            case 'csv': $type = Type::CSV;break;
            default: return [[101,'type format error'],false];
        }

        $reader = ReaderFactory::create($type);
        if($t == 'csv'){
            $reader->setFieldDelimiter($option['delimiter']?:',');
            $reader->setFieldEnclosure($option['enclosure']?:'"');
            $reader->setEndOfLineCharacter("\r");
        }
        $reader->setShouldFormatDates(true); // will return formatted dates
        $reader->open($fileName);
        $iterator = $reader->getSheetIterator();
        $iterator->rewind();
        $sheet1 = $iterator->current();
        $rowIter = $sheet1->getRowIterator();
        $fields = [];
        $data = [];

        if(!isset($option['header'])){
            foreach ($rowIter as $rowIndex => $row) {
                if($rowIndex == 1){
                    $fields = array_map(function ($val){return trim($val);},$row);
                    if (!empty($fieldvalidates) && $diff = array_diff($fieldvalidates, $fields)) {
                        return [[102,current($diff)." field colum not does exist"],false];
                    }
                } else {
                    $newRow = [];
                    if(count($fields) == count($row)) {
                        $newRow = @array_combine($fields, $row);
                    } else {
                        foreach ($fields as $k => $v) {
                            @$newRow[$v] = $row[$k];
                        }
                    }

                    if (!empty($fieldvalidates)) {
                        foreach ($fieldvalidates as $field) {
                            if(empty($newRow[$field])) {
                                return [[103,"{$rowIndex} lines {$field} field is empty"],false];
                            }
                        }
                    }
                    $data[] = $newRow;
                }
            }
        } else {
            foreach ($rowIter as $rowIndex => $row) {
                $data[] = $row;
            }
        }

        $reader->close();
        return [$data,true];
    }
````