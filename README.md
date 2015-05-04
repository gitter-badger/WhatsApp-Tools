# WhatsApp Tools

[![Join the chat at https://gitter.im/usinadeideia/WhatsApp-Tools](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/usinadeideia/WhatsApp-Tools?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

**Contact with me: me [at] mgp25.com or @_mgp25**

**IMPORTANT: Be sure to use the newer code from my repo [WhatsAPI-Spanish](https://github.com/mgp25/WhatsAPI-Spanish) or WhatsAPI from Shirioko**

- **Check [WA Tools](http://watools.es), a website i made, send messages, check your whatsapp status, see others profile picture, last seen time, status and much more!**

----------
### Instrucciones y líneas básicas 

whatsprot.class.php is required for all scripts (Telling this for beginners...)


**Registering a new number / Registrando un nuevo numero**

```php
  $debug = true;

  $username = '34666554433'; // Telephone number including the country code without '+' or '00'.
  $identity = ''; 
  $nickname = 'WA Tools'; // This is the username displayed by WhatsApp clients.


  // Create a instance of WhastPort.
  $w = new WhatsProt($username, $identity, $nickname, $debug);
  
  $w->codeRequest('sms');
```

Once you have the code similar to 123-456. You have to insert it this way 123456. 
Update the script to validate your code with:

```php
  $w->codeRegister('123456'); 
```



**Logging in / Iniciando sesión**

```php
 $username = ''; // Number with country code
 $password = ''; // Password obtained with WART or WhatsAPI
 $identity = ''; // $identity is no longer necessary
 $debug = false; // You can set true, for more details

 $nickname = "WA Tools"; // This is the username (or nickname) displayed by WhatsApp clients.
  

$w = new WhatsProt($username, $identity, $nickname, $debug);
$w->connect();

// Now loginWithPassword function sends Nickname and (Available) Presence
$w->loginWithPassword($password);
```

**Sending Messages / Enviando mensajes**

```php

  $dst = '34666554433'; // The number of the person you are sending the message

  $w->sendMessage($dst , "Este es un mensaje de prueba :D");
  
  // You can also add a $message and use it this way:
  $w->sendMessage($dst , $message);
```

**Requesting Last Seen / Viendo la última vez en linea**

```php
 $msgid = time()."-1";

//The function retrive $seconds in UNIX timestamp, you can format that for a easier function :P
function onGetRequestLastSeen($username, $msgid, $seconds)
{
	//echo "Received last seen seconds: '$seconds'";
    //$now = time();
    //$lastSeen = $now - $seconds;
   
    $secondsInAMinute = 60;
    $secondsInAnHour  = 60 * $secondsInAMinute;
    $secondsInADay    = 24 * $secondsInAnHour;

    // extract days
    $days = floor($seconds / $secondsInADay);

    // extract hours
    $hourSeconds = $seconds % $secondsInADay;
    $hours = floor($hourSeconds / $secondsInAnHour);

    // extract minutes
    $minuteSeconds = $hourSeconds % $secondsInAnHour;
    $minutes = floor($minuteSeconds / $secondsInAMinute);

    // extract the remaining seconds
    $remainingSeconds = $minuteSeconds % $secondsInAMinute;
    $seconds = ceil($remainingSeconds);

    // return the value
    if (($seconds==null) & ($minutes==null) & ($hours==null) & ($days==null))
    	echo "El contacto tiene desactivado esta función";
    else if (($seconds==0) & ($minutes==0) & ($hours==0) & ($days==0))
    	echo "En línea";
    else
    	echo $days . " días " . $hours . " horas " . $minutes . " minutos";
  
}

$w->eventManager()->bind('onGetRequestLastSeen', 'onGetRequestLastSeen');
$w->sendGetRequestLastSeen($dst);

// See Event Manager in WhatsAPI-Spanish repo for more info.
```

**Requesting profile picture / Obteniendo la imagen de perfil**

```php
function onGetProfilePicture($from, $target, $type, $data)
{
    if ($type == "preview") {
        $filename = "preview_" . $target . ".jpg";
    } else {
        $filename = $target . ".jpg";
    }
    $filename = WhatsProt::PICTURES_FOLDER."/" . $filename;
    
    $fp = @fopen($filename, "w");
    if ($fp) {
        fwrite($fp, $data);
        fclose($fp);
    
    }
    
    echo '<a href="'.$filename.'"><center><img src="'.$filename.'" height="250" width="250"></center></a><br><br>';
      
}
//Create the whatsapp object and setup a connection.
//Retrieve large profile picture. Output is in /src/php/pictures/ (you need to bind a function
//to the event onProfilePicture so the script knows what to do.
$w->eventManager()->bind("onGetProfilePicture", "onGetProfilePicture");
$w->sendGetProfilePicture($target, true);
```

**Create a group chat / crear un chat de grupo**

```php
$participants = array("34123456789", "34987654321");
$w->sendGroupsChatCreate("My new Group chat", $participants);
```

**Sending messages to a group chat / mandar mensajes a un chat de grupo**

```php
// $gId should be like MyNumber-1405367897, the last number you can capture with and event and $w->sendGetGroups();
$w->sendMessage($gId, $msg);
```

**Send broadcast message / mandar un difundido**

```php
// Only synced user will receive broadcast messages
$targets = array("34123456789", "34987654321");
$message = "This is broadcast message";
$w->sendBroadcastMessage($targets, $message);
```

**Send video message / mandar un video**

```php
$target = "34123456789";
$pathToVideo = ""; // This could be url or path to video.
$w->sendMessageVideo($target, $pathToVideo);
// If you have already the video size and hash, you can send it without re-uploading it to whatsapp servers
$w->sendMessageVideo($to, $filepath, false, $fsize, $fhash);

// This also works with:
// $w->sendMessageAudio and $w->sendMessageImage
```
