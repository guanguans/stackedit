
``` php
<?php
/**  
* @return array 
*/
function getHeader()  
{  
	$GLOBALS['HEADER'] = $_SERVER;  
	array_walk($GLOBALS['HEADER'], function ($value, $key){  
		if (strpos($key, 'HTTP_') !== 0) {  
			unset($GLOBALS['HEADER'][$key]);  
		}  
	});  

	return (array) $GLOBALS['HEADER'];  
}
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTYwMjIxMDc2Nl19
-->