Title:      Have you been pwned?
Topic:      misc
Author:     flabbyrabbit
Date:       01/05/2019
Course:     

Have your passwords been exposed online? Although you **should** be using a password manager with unique passwords generated for each online account not everyone will have the patience to do so or there may still be some accounts floating around that you have not got around to updating.

Troy Hunt

Have I been pwned website. He collects dumps online and collates them. Whenever your email has been exposed it will let you know. Sign up for 

Another aspect of the site is a password checker and API. You can enter a password and the site will inform you if the password has ever been exposed in a dump. This could mean that your password will now been included in dictionary attacks making your account more sereptable to attack. This does not necessarily mean that the password has been comprimised from one of your own accounts, it could also mean that someone else has used the same password and their account has been exposed.

If your password has been exposed then it is no longer safe to use and any account using that password should be updated as soon as possible.

There is an API that is used by some password managers and sites.

## k-anonymity
Entering your password into any website other than the login form of the site you are trying to access is a stupid idea, don't do it! Either through malice or an unintentional vulnerability this is not a good idea. Troy provides a full download of SHA1 hashes but at over 10GB it's not practical to download to just check for 1 or 2 passwords. However this API uses k-anonymity to lookup up vulnerable information on a third party system with out exposing the whole string.

[K-anonymity](https://en.wikipedia.org/wiki/K-anonymity) is a mathematical property of anonymised data that attempts to solve the problem of having practical useful data without exposing the identity of any individual in the data. In this case this is done by only sending the first 5 characters of a hashed password to the server and receiving back all possible hashes that start with that prefix.

* Every hash prefix from 00000 to FFFFF is populated with data (16^5 combinations)
* The average number of hashes returned is 478

This means that the password we are looking for could be one of any of the ~500 responses or it could be something completely different. From the server or any eavesdroppers point of view they can not see if the hash was even found let alone which one it was.

## API
Although this is explained on the site there is no guarantee that the form shown will be following this correctly. Luckily there is also a REST API so you can manually perform these lookups without entering your password into their site.

Full API details can be found at https://haveibeenpwned.com/API/v2#PwnedPasswords.

The API uses k-anonymity as described above using a SHA1 hash of the password. Even though the password is hashed the hash is weak and with the third party having the full list of passwords it would be a trivial job to crack your password with a reverse lookup. Therefore you send the first 5 characters of the hash to the API. The API will then return all hashes that start with those 5 characters. Due to the amount of passwords stored this list will contain hundreds of matches which only lets the server know that the password you are checking could possibly be in that list but also there is no guarantee that it is at all. You can then look through the list locally and find if the full hash is found within the list of hashes.

### Example
Lets take the password "12345" as a basic example. We need to get the SHA1 hash of this string. You can do this online with a number of [websites](http://www.sha1-online.com/) or if you are using Linux you can run the following in terminal. The -n option is used for echo so that a newline isn't passed to the sha1sum command.

    echo -n 12345 | sha1sum

Side note: entering a non-obscured password into the terminal is a bad idea. Any commands you enter will show up in your terminal history in plain text.

This should produce the following hash:

    8cb2237d0679ca88db6464eac60da96345513964

We then take the first 5 charactes `8cb22` and pass these to the API endpoint which in this example would give us https://api.pwnedpasswords.com/range/8cb22.  Either by simply entering the URL in your browser or using CURL if you are using the terminal.

    curl https://api.pwnedpasswords.com/range/8cb22

This returns over 550 matching password hashes. The hashes returned don't include the five character prefix as we already know this. It also returns the number of times this password has been seen in different dumps. Searching the returned data for the remainder of our hash `37d0679ca88db6464eac60da96345513964` we find that it has been seen a whopping 23,332,32 times. Maybe don't use that one...

### Terminal example
Pasting the below in to the terminal with return the number of times exposed if the password is included in the list.

    hash=`echo -n 12345 | sha1sum`
    
    curl -s https://api.pwnedpasswords.com/range/${hash:0:5} | \
        grep -i ${hash:5:35} | \
        awk -F ':' '{ print $2}'

### Online example
[challenge haveibeenpwned.html]