# htmlMimeMail5
Source was from http://www.phpguru.org

It's upgradin' time! With the advent of PHP5 cometh a whole new era of rewriting old classes to make use of the new object system. Perhaps in many cases, for no great reason other than to make use of the new object system.
Fun though.

So, in true "pointless upgrade" style, I've upgraded the ever popular HTML Mime Mail class to use the PHP5 object system. Yes, it's a bit of a pants name, but such is life. This article is going to document the new version, now called, htmlMimeMail5. Cute huh?

Not much has changed; the properties and methods make use of the private/public access modifiers, embedded images and attachments are handled slightly differently, and the class now supports setting mail priorities. The class also now always sets a text part to the email, whether there's any text or not.

So off we go down the yellow brick road. Get ready Oz.

```php
<?php
    require_once("htmlMimeMail5.php");
    
    $mail = new htmlMimeMail5();
?>
```

Not much different here. Include the file using require_once(), and create a new instance of the class. You should note that all the .php files in the zip file should be held in the same directory. The code will handle dragging them in at the appropriate time.

```php
<?php
    $mail->setFrom("Richard <richard@example.com>");
?>
```

Sets the From: address of the email. The method can accept email addresses of the above format (friendly name portion and address), and also just addresses.

```php
<?php
    $mail->setSubject('Test email');
?>
```

Sets the subject of the email. Pretty straight forward.

```php
<?php
    $mail->setPriority('high');
?>
```

Sets the priority of the email. Acceptable arguments are "high", "normal" or "low". It can also accept either 1, 3 or 5, which correspond respectively with the former.

```php
<?php
    $mail->setText('Sample text');
?>
```

Sets the text part of the email.

```php
<?php
    $mail->setHTML('<body background="background.gif"><b>Sample HTML</b></body>');
?>
```

Sets the HTML part of the email. Naturally this is totally optional, if you don't want to send an HTML email, don't set any HTML. Here, there is a background attribute on the body tag. This can contain an absolute path to an image held on the internet, or as we're going to do here, a filename of an image file which will be embedded into the email. This makes the email larger, but increases greatly the potential of the end user seeing the image(s).

```php
<?php
    $mail->addEmbeddedImage(new fileEmbeddedImage('background.gif', 'image/gif', new Base64Encoding()));
?>
```

This is the first significant difference from previous versions of the class. This method used to be called addHTMLImage(), howeever I thought this was better. Has a nicer ring to it too. And then there's the argument. Previously it took three arguments, however now it takes just the one, which would be fileEmbeddedImage object, or a stringEmbeddedImage object. These objects in turn handle either loading the image from disk, or taking it from a variable. The fileEmbeddedImage object as you can see takes three arguments, being the filename, the content type and an encoding type. The encoding type should also be an object, either a Base64Encoding object, or a QPrintEncoding object. The latter should rarely be used. In fact, the last two arguments of the fileEmbeddedImage object are optional, defaulting to "application/octet-stream" and base64 encoding. The stringEmbeddedImage differs in that it takes the image data as the first argument instead of a filename. The second argument is the name to use as the file name of the image (this is taken from the filename with the fileEmbeddedImage object). The third and fourth arguments are the content type and encoding type, as with the fileEmbeddedImage object.

```php
<?php
    $mail->addAttachment(new fileAttachment('example.zip', 'application/zip', new Base64Encoding()));
?>
```

This is the second significant difference from prior versions. This method has been changed to work in the same way as addEmbeddedImage(), in that it now takes a single argument instead of three. The objects that should be used however, are named slightly differently, being fileAttachment for a file on disk, and stringAttachment for a file which you have loaded into a variable. These objects take the same objects to their constructors as the embedded image objects.

```php
<?php
    $mail->send(array('richard@example.com'));
?>
```

Hurrah. Now we send the email. The send() method takes two arguments, the first being an array of recipients ( again, the email addresses can be specified with or without the friendly name part), and the optional second being the method of sending to use. This defaults to "mail", which uses the built in PHP mail() function, or it can also be "sendmail" or "smtp". If using either "sendmail" or "smtp", you should check the reference for methods which change either the sendmail path, or SMTP parameters.

That's it. We have reached the mighty city of Oz. Time to disembark until next time. Last thing is the method reference, which lists all available public methods, and what they do.

Ciao.

## Method Reference

| Method | Description |
|--------|-------------|
| Constructor | Doesn't take any arguments. Not much to say I guess. |
| `setCRLF(string $crlf)` | Sets the CRLF line ending type to use. You should not use this unless you're sure you know what you're doing, as the code will correctly (most of the time) determine which type to use by itself. |
| `setSMTPParams(string $host, int $port, string $helo, bool $auth, string $user, string $pass)` | Set SMTP parameters when using SMTP to send the email. All optional, though not specifying any would be pointless... Pretty self explanatory really - hostname to connect to, port to connect to, text to send as part of the HELO command, whether to use SMTP authentication, and if so the username and password to use. |
| `setSendmailPath(string $path)` | 	Sets the path to use when using sendmail method of sending the email. This should be the full path to the sendmail binary on your system, along with any command line parameters. Defaults to "/usr/lib/sendmail -ti". |
| `setTextEncoding(object $encoding)` | Sets the encoding to use (if any) on the plain text part of the email. The argument should be one of the encoding objects, ie Base64Encoding, QPrintEncoding, SevenBitEncoding or EightBitEncoding. Bit pants about the 7bit/8bit names, but identifiers in PHP can't start with a number. Tits. |
| `setHTMLEncoding(object $encoding)` | Sets the encoding to use (if any) on the HTML part of the email. The argument should be one of the encoding objects, ie Base64Encoding, QPrintEncoding, SevenBitEncoding or EightBitEncoding. |
| `setTextCharset(string $charset)` | Sets the characterset to use on the plain text portion of the email. Defaults to "ISO-8859-1". |
| `setHTMLCharset(string $charset)` | Sets the characterset to use on the plain text portion of the email. Defaults to "ISO-8859-1". |
| `setHeadCharset(string $charset)` | Sets the characterset to use on the headers of the email. Defaults to "ISO-8859-1". |
| `setTextWrap(int $count)` | Set the point at which text is wrapped in the email. Defaults to 998, which is a limit imposed by the SMTP standard. |
| `setHeader(string $name, string $value)` | Sets a header of the email. First argument if the name of the email, second is the value. |
| `setSubject(string $subject)` | Sets the subject of the email. |
| `setFrom(string $from)` | Sets the From: header of the email. |
| `setPriority(mixed $priority)` | Sets the priority of the email. Defaults to "normal". Argument can be either "high", "normal" or "low", or 1, 3 or 5 respectively. |
| `setReturnPath(string $return_path)` | Sets the Return-Path: of the email. This is the address to which bounces will be sent if the email fails to be delivered. |
| `setCc(string $cc)` | Sets the Cc: header of the email. The email will be delivered to these addresses as well as those specified in the send() method. |
| `setBcc(string $bcc)` | Sets the Bcc: header of the email. The email will be delivered to these addresses as well as those specified in the send() method. However, the Bcc: header will be removed from email before it is sent, so those addresses in the To: and Cc: headers will not know it has also been sent to these addresses. Hence, "Blind" Carbon Copy. |
| `setText(string $text)` | Sets the text part of the email. |
| `setHTML(string $html[, string $images_dir])` | Sets the HTML part of the email. The optional second argument specifies a directory where the code should look for images referenced by the HTML. If found, these images will automatically be embedded into the email. |
| `addEmbeddedImage(object $embeddedImage)` | Adds an embeddded image to the email. The argument should be on of the fileEmbeddedImage or stringEmbeddedImage objects, depending on the source of the image. |
| `addAttachment(object $attachment)` | Adds an attachment to the email. The argument should be one of the fileAttachment or stringAttachment objects, depending on the source of the attachment. |
| `send(array $recipients[, string $type])` | Sends the email. The first argument is an array of recipients to whom the email will be sent to. The second is how to send the email. This can be one of "mail", "sendmail" or "smtp", and defaults to "mail". |
| `getRFC822(array $recipients[, string $type])` | email to another email. This could be done like so: $mail_2->addAttachment(new stringAttachment($mail_1->getRFC822(array('richard@example.com')), 'message/rfc822')) The first argument is an array of recipients to place in the To: header, whilst the second, optional, $type argument should be the same as that you intend to use eventually for the send() method. This is to ensure the correct line endings are used. |
