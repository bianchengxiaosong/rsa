<?php
header('content-type:text/html;charset=utf-8');
error_reporting(E_ALL^E_NOTICE);
function add($num1,$num2){
	$arr1 = numToArr($num1);
	$arr2 = numToArr($num2);
	$arr3 = array();

	$prev_num = 0;
	if(count($arr1) < count($arr2)){
		$temp_arr = $arr2;
		$arr2 = $arr1;
		$arr1 = $temp_arr;
	}

	foreach($arr1 as $k => $v){
		if($arr1[$k] + (int)$arr2[$k] + $prev_num > 9){
			$arr3[$k] = $arr1[$k] + (int)$arr2[$k] + $prev_num - 10;
			$prev_num = 1;
		}else{
			$arr3[$k] = $arr1[$k] + (int)$arr2[$k] + $prev_num;
			$prev_num = 0;
		}
	}

	if($prev_num == 1){
		$arr3[] = 1;
	}

	$num3 = strrev(implode('',$arr3));
	return  $num3;
}

function reduce($num1,$num2){
	$arr1 = numToArr($num1);
	$arr2 = numToArr($num2);
	$arr3 = array();

	$arr1_count = count($arr1);
	$arr2_count = count($arr2);

	if($arr1_count > $arr2_count){
		//正数
		$symbol = '';
	}else if($arr1_count == $arr2_count){
		for($i = ($arr2_count - 1);$i >= 0;$i--){
			if($arr1[$i] > $arr2[$i]){
				$symbol = '';
				break;
			}else if($arr1[$i] < $arr2[$i]){
				$symbol = '-';
				break;
			}
		}
	}else{
		//负数
		$symbol = '-';
	}

	//判断为正数还是负数
	if($symbol == '-'){
		$temp_arr = $arr2;
		$arr2 = $arr1;
		$arr1 = $temp_arr;
	}

	$arr1_count = count($arr1);
	$arr2_count = count($arr2);

	for($i = $arr2_count;$i < $arr1_count;$i++){
		$arr3[$i] = $arr1[$i];
	}

	for($i = ($arr2_count - 1);$i >= 0;$i--){
		if($arr1[$i] - $arr2[$i] < 0){
			for($j = ($i + 1);$j < $arr1_count;$j++){
				if($arr3[$j] > 0){
					$arr3[$j] -= 1;
					break;
				}else{
					$arr3[$j] = 9;
				}
			}
			$arr3[$i] = 10 + $arr3[$i] - $arr2[$i];
		}else{
			$arr3[$i] = $arr1[$i] - $arr2[$i];
		}
	}

	$arr3_count = count($arr3);
	for($i = (count($arr1) - 1);$i > ($arr3_count - 1);$i--){
		$arr3[$i] = $arr1[$i];
	}

	ksort($arr3);

	$num = '';
	foreach($arr3 as $v){
		$num = $v . $num;
	}
	$num = $symbol.ltrim($num,'0');
	return $num;
}

function multi($num1,$num2){
	$arr1 = numToArr($num1);
	$arr2 = numToArr($num2);
	$arr3 = array();

	if(count($arr1) < count($arr2)){
		$temp_arr = $arr2;
		$arr2 = $arr1;
		$arr1 = $temp_arr;
	}

	$arr2_count = count($arr2);
	for($i = ($arr2_count - 1);$i >= 0;$i--){
		$arr3[$i] = array();
		$prev_num = 0;
		foreach($arr1 as $k => $v){
			if($v * $arr2[$i] + $prev_num > 9){
				$arr3[$i][] = (int)(($v * $arr2[$i] + $prev_num) % 10);
				$prev_num = (int)(($v * $arr2[$i] + $prev_num) / 10);
			}else{
				$arr3[$i][] = $v * $arr2[$i] + $prev_num; 
				$prev_num = 0;
			}
		}

		if($prev_num != 0){
			$arr3[$i][] = $prev_num;
		}
	}

	$arr4 = array();
	foreach($arr3 as $k => $v){
		ksort($v);
		$v = array_reverse($v);
		if($k > 0){
			$arr4[$k] = implode('',$v).str_repeat('0',$k);
		}else{
			$arr4[$k] = implode('',$v);
		}
	}

	$num = '0';
	foreach($arr4 as $k => $v){
		$num = add($v,$num);
	}

	return $num;
}

//除法
function except($num1,$num2){
	$arr1 = numToRevArr($num1);
	$num2 = (int)$num2;

	$temp_except = '';
	$result = '';

	foreach($arr1 as $k => $v){
		$temp_except .= $v;

		if($num2 <= $temp_except){
			$result .= (int)($temp_except / $num2);
			$temp_except = $temp_except % $num2;
		}
	}

	return $result;
}

//取余数
function takeOver($num1,$num2){
	$arr1 = numToRevArr($num1);
	$num2 = (int)$num2;

	$temp_except = '';
	$result = '';

	foreach($arr1 as $k => $v){
		$temp_except .= $v;

		if($num2 <= $temp_except){
			$result .= (int)($temp_except / $num2);
			$temp_except = $temp_except % $num2;
		}
	}

	return $temp_except;
}

$p = 103;
$q = 19;
$n = $p * $q;
$t = ($p - 1) * ( $q - 1);

$arr = array();
for($i = $t;$i > 0;$i--){
	$arr[$i] = $i; 
	for($j = 2;$j < $i;$j++){
		if($t % $j == 0 && $i % $j == 0){
			unset($arr[$i]);
			break;
		}
	}
}
$e = array_rand($arr);

$i = 0;
while(TRUE){
	$i++;
	if(($e * $i) % $t == 1){
		break;
	}
}
$d = $i;

var_dump('公钥:('.$n.','.$e.')');
var_dump('私钥:('.$n.','.$d.')');

// 公钥 （143,7）
// 私钥 （143,103）

$m = '2';
$c = $m;
for($i=0;$i<($d - 1);$i++){
	$c = multi($m,$c);
}

$c = takeOver($c,$n);
var_dump($c);

$temp_c = (string)$c;
for($i=0;$i<($e - 1);$i++){
	$c = multi($c,$temp_c);
}

$m = takeOver($c,$n);
var_dump($m);

//获取数的数组格式
function numToArr($num){
	$num = (string)ltrim($num,'0');

	$num_len = strlen($num);
	$arr = array();
	for($i = ($num_len - 1),$j = 0;$i >= 0;$i--,$j++){
		$arr[$j] = $num[$i];  
	}

	return $arr;
}

//获取数的数组格式 倒叙
function numToRevArr($num){
	$num = (string)ltrim($num,'0');

	$num_len = strlen($num);
	$arr = array();
	for($i = 0;$i < $num_len;$i++){
		$arr[$i] = $num[$i];  
	}

	return $arr;
}

