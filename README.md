# CSAW 2016 Quals
# mfw 
#### Cassandra Stavros -- USF Whitehatter's Computer Security Club
#### web -- 125 points
## Description

Hey, I made my first website today. It's pretty cool and web7.9.

http://web.chal.csaw.io:8000/

## Solution
The website gives us the following information:
I wrote this website all by myself in under a week!

I used:

* Git
* PHP
* Bootstrap
---
From the html source code, we can see that there is a hidden flag.php page:
![screen shot](https://cloud.githubusercontent.com/assets/22091364/18804797/22dd372c-81ce-11e6-96b0-a034f3d2d970.jpg)

The website author also hints that he used the above sources to create the website. A quick check of the challenge website reveals that they used github as the source control repository. We can use the publically exposed .git/ directory to retreive the website source code:

    http://web.chal.csaw.io:8000/.git/

Information security professionals warn that publicly revealing a website's source code leaves it open to vulnerabilities (https://en.internetwache.org/dont-publicly-expose-git-or-how-we-downloaded-your-websites-sourcecode-an-analysis-of-alexas-1m-28-07-2015/).

We can use this publically exposed .git directory to retreive the website source code.
We used wget to download that .git/ directory of, (or use curl for Mac users):

    wget --mirror -I .git http://web.chal.csaw.io:8000/.git/
---
Now that we have the .git/ directory, we can explore the files in the Git repository. 

By typing in the following command we can get the status of proposed and deleted changes:

    $ git status | head -n 10
    
Which retrieves the following file statuses:

    deleted:    index.php
    deleted:    templates/about.php
    deleted:    templates/contact.php
    deleted:    templates/flag.php
    deleted:    templates/home.php
---

we ran "git checkout -- " . to obtain the source files from the git repo

Next, we looked  at the current files in the directory:

    $ ls

Which shows that the following files exist:

    index.php
    templates
---

We examined the index.php file and found that it used assert:

    <?php

    if (isset($_GET['page'])) {
	    $page = $_GET['page'];
    } else {
	    $page = "home";
    }

   $file = "templates/" . $page . ".php";

    // I heard '..' is dangerous!
    assert("strpos('$file', '..') === false") or die("Detected hacking attempt!");

    // TODO: Make this look nice
    assert("file_exists('$file')") or die("That file doesn't exist!");

Per aaraonasterling on StackOverflow: 
>The rule of thumb which is applicable across most languages (all that I vaguely know) is that an assert is used to assert that a >condition is always true whereas an if is appropriate if it is conceivable that it will sometimes fail. (http://stackoverflow.com/questions/4516419/should-i-be-using-assert-in-my-php-code#4516444)

With this in mind, we knew the way to capture the flag was to use `assert` to our advantage.
---    


