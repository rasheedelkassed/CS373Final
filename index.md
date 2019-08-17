# The Assignment
  For our final assignment, we had to complete a minimum of three challenges that provide a sum of at least 50 points. However, getting to these challenges wasn’t as simple as you might expect. First, the site doesn’t have a standard sign-up page; instead, it has a challenge of sorts to generate your own invite code using what’s on the site.
  
# The Invite Code
  To gain access to the website I needed to generate my own invitation code. To figure this out, I instantly went to the sources tab in my browser’s developer tools:
  
![Sources](/images/Sources.PNG)

  From there, I noticed a JavaScript file called “inviteapi.min.js” that seemed like a good first step towards generating my invite code. Opening this file leads to an obfuscated call to eval(). After pretty-printing it, I went to work figuring out what it did:
  
![inviteapi](/images/inviteapi.PNG)

  Since I wasn’t really looking forward to manually parsing through all this difficult to read code, I decided to see what this eval statement returned. To do this, I placed a breakpoint on line 25 and then refreshed the page. The return value ends up being two functions that are hidden from the user:
  
![breakpoint](/images/breakpoint.PNG)

Since you can’t see the entire return value in the picture, it ends up being: 

```
function verifyInviteCode(code) {
    var formData = {
        "code": code
    };
    $.ajax({
        type: "POST",
        dataType: "json",
        data: formData,
        url: '/api/invite/verify',
        success: function(response) {
            console.log(response)
        },
        error: function(response) {
            console.log(response)
        }
    })
}

function makeInviteCode() {
    $.ajax({
        type: "POST",
        dataType: "json",
        url: '/api/invite/how/to/generate',
        success: function(response) {
            console.log(response)
        },
        error: function(response) {
            console.log(response)
        }
    })
}
```

From here, I see the very interesting function called makeInviteCode. I went into the console and ran it as soon as I saw it:
  
![makeInviteCode](/images/makeInviteCode.PNG)

This provides a long string that is said to be encoded in base64. Using an online tool, I decoded the string and it provided me with a message:
  
![base64](/images/base64.PNG)

After getting this message (and doing some googling to figure out the best way to do this), I opened up my terminal and followed the instructions that were given to me:
  
![curl](/images/curl.PNG)

As you can see, this provided me with yet another encoded string. Going back to the online tool, I tried putting that encoded string into it:
  
![invitecode](/images/invitecode.PNG)

It pulled through and provided me with my new invite code: TLSRS-CIGIF-DXKCU-WHAZG-YIXJS


# Challenge One
For my first challenge, I decided to start easy and get a feel for how HackTheBox worked. I chose the challenge named: “You Can Do It!” by Stylish under the crypto section. This challenge provides the challenger with a file named “you_can_do_it.txt.” Within this file, was the phrase “YHAOANUTDSYOEOIEUTTC!” and nothing else:
  
![Challenge1-1](/images/Challenge1-1.PNG)

This phrase looked like a complete jumble of words, and after trying several things out that didn’t result in anything, I decided to use the title as a hint. I created the sentence “YOU CAN DO IT” out of the letters that were available to me, deleting letters from the original phrase as I went:
  
![Challenge1-2](/images/Challenge1-2.PNG)

From this point, I saw that I could make the word “YOU” a second time, so I did just that and moved the letters down once again:
  
![Challenge1-3](/images/Challenge1-3.PNG)

After that, you can clearly see the word “SEE” in the original phrase, so I moved that down as well:
  
![Challenge1-4](/images/Challenge1-4.PNG)

The last four letters can spell the word “THAT” so I brought that word down as well along with the exclamation point:
  
![Challenge1-5](/images/Challenge1-5.PNG)

Finally, the last thing I did was swap words around until the sentence made grammatical sense:
  
![Challenge1-6](/images/Challenge1-6.PNG)

The flag for this challenge ended up being “HTB{YOUSEETHATYOUCANDOIT!}” after following the flag format. All in all an easy challenge that probably has a much more clever way of going about it. I guess that’s why it’s only worth 10 points though.

# Challenge 2
For my second challenge, I decided to go with a slightly more difficult one. I chose the challenge named: “Keys” by n4ckhcker under the crypto section that provided 40 points on completion. This challenge provides the challenger with a file named “keys.txt.” Within this file, was the two phrases: “hBU9lesroX_veFoHz-xUcaz4_ymH-D8p28IP_4rtjq0=” and “gAAAAABaDDCRPXCPdGDcBKFqEFz9zvnaiLUbWHqxXqScTTYWfZJcz-WhH7rf_fYHo67zGzJAdkrwATuMptY-nJmU-eYG3HKLO9WDLmO27sex1-R85CZEFCU=”:
  
![Challenge2-1](/images/Challenge2-1.PNG)

At first, I thought that this was encoded in base64 due to the equal signs found at the end of both phrases; however, using an online tool to decipher base64, I found this not to be the case as the tool just spit out gibberish:
  
![Challenge2-2](/images/Challenge2-2.PNG)
![Challenge2-3](/images/Challenge2-3.PNG)

From there, I realized I was being a bit daft by forgetting the title of challenge. Base64 doesn’t use any keys, so it can’t possibly be the right answer. With some googling, I came across the wikipedia page for symmetric key algorithms and decided that this was the more probable encryption method as there doesn’t seem to be the multiple keys that public-key encryption provides. Using this information led me to a python cryptography library (https://cryptography.io/en/latest/). This particular library used something known as the Fernet. After googling the terms “Fernet Cipher” I found an online tool that decodes Fernet encryption provided you have the key (https://asecuritysite.com/encryption/ferdecode). Upon opening the page, it had an example token and key that matched the format of the provided phrases in the file.
  
![Challenge2-4](/images/Challenge2-4.PNG)

After using the online tool, it provided the output “HTB{N0t_A_Fl1g!}”:
  
![Challenge2-5](/images/Challenge2-5.PNG)

This challenge was particularly cool as I hadn’t heard of Fernet encryption before.
  


# Challenge 3
For my third challenge, I decided to shake things up a bit by going for a different category. I chose the challenge named: “HDC” by Thiseas under the Web section that provided 30 points on completion. After I completed the challenge, I went back to take pictures for this assignment only to find that it had been retired and inaccessible to me. I simply don’t have enough time to do another one. How fun!
Upon opening up the instance of the website I was introduced to a simple login page that required a username and password. Knowing that this particular challenge had been solved thousands of times, I decided to check if they had hidden input boxes containing the values. To do this, I right-clicked on the form in front of me and inspected the element. From here, I saw that the form had the IDs “name1” and “name2” on both input boxes. Using this ID I went to the source files and searched for the terms. Sure enough, two hidden input boxes that contain the username and password were there. Once I had logged into the website, I went to the email list section and, after finding nothing, checked the source code. In the html, an image on the page had the path “/secret_area_/list.png” or something similar. I instantly went to that web page only to find a text file that contains a bunch of emails. After sending a message to each email through the built-in (and terrible) mail service, the flag popped up. The only reason I knew that I had to send an email through the service is due to the challenge’s description. Without that, I wouldn’t have been able to figure out what to do as fast as I did.


# Proof of Completion:
![proof1](/images/proof1.PNG)
![proof2](/images/proof2.PNG)
