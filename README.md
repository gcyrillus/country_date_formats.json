This a file created from this other file : https://gist.github.com/mlconnor/1887156#file-country_date_formats-csv 

I needed to avoid Class "IntlDateFormatter" for a script running in a plugin for PluXml.

Extraction from the CSV file to JSON with only the language and format that can be reused in php has been made with this script

```
<pre><?php
	$file = fopen('country_date_formats.csv', 'r');
	$dateFormatArray=array();
	$phpFormat=array('Y','m','d');
	$format =array('yyyy','MM','dd');
	while (($line = fgetcsv($file)) !== FALSE) {
		echo $line[1].' '.$line[4].PHP_EOL;
		if(strtolower($line[1]) != $line[4]) $lang= $line[4].'_'.$line[1];
		else $lang = $line[4];
		$dateFormatArray[$lang]=str_replace($format,$phpFormat,$line[6]);
	}
	fclose($file);
	file_put_contents('country_date_formats.json',json_encode($dateFormatArray,true | JSON_PRETTY_PRINT) );	
	# let's see what we got
	var_dump($dateFormatArray);
```

then the average converted function avoiding use of Class "IntlDateFormatter".

```
		public function timeToLocalDate($time){
			$plxMotor = plxMotor::getInstance();
			$locale = $plxMotor->aConf['default_lang']; // Locale
			$timezone = $plxMotor->aConf['timezone']; // Fuseau horaire
			/*$format = new IntlDateFormatter($locale,IntlDateFormatter::SHORT,IntlDateFormatter::SHORT,$timezone	);
			$date_locale = $format->format($time);
			return $date_locale;	*/
			$formats=json_decode( file_get_contents(PLX_PLUGINS.basename(__DIR__).'/lang/country_date_formats.json') );
			$format = $formats->$locale;
			return date($format,$time);
		}

```

Feel free to reuse and improve via pull request
