---
date: 2016-06-16
tags:
    - gcc
    - linux
    - python
    - root
    - autoconf error
---

# Semi-Beginner’s Guide to Reading a Simple TBX file with XML::Twig Part I

I wrote this tutorial for a colleague who is learning Perl and the XML::Twig module.  I wrote this to him in an email, so the tone of this post will be more that of an email.  I leave a lot out and simplify a lot of terms, but I think this will be handy to point to for the next person I need to teach XML::Twig to.

The first thing to understand is that XML::Twig (you can find the detailed information on CPAN here <http://search.cpan.org/dist/XML-Twig/Twig.pm>) is a Perl module that lets you read and edit XML files much easier than you would if you were trying to look for beginning and end tags manually.  I don’t know if you have gotten to modules yet in your Perl studies, but Modules are basically Perl apps that other people have written and put on CPAN (a library of Perl modules) for public use.  In this case, someone wrote a Perl app that reads and writes XML, so you can just use their app by plugging it into your app as a module.  This way you don’t have to create an XML reader/writer yourself and you save countless hours!
So, it is not a separate language (although, there *is* a language called Twig, but that is completely different, so ignore it for now).  Your Perl code in your text editor will just import this module so you can use it in your code (more on this in a moment).
I would recommend using the Module App::cpanminus to install all modules.  So, to install these modules open your terminal and type:
 
```sh
cpan App::cpanminus
```
 
This will install App::cpanminus.  Once that is complete you will use cpanminus (command “cpanm”) to install XML::Twig:

```sh 
cpanm XML::Twig
```
 
If that went well, we will now import XML::Twig into our Perl app:
 
* Open your perl app in the text editor
* at the top, after `use strict;` and `use warnings;` type: `use XML::Twig;` (for an example of this, look at line 9 of this file: <https://github.com/byutrg/p5-Extract-TBX-Min/blob/master/lib/Extract/TBX/Min.pm>)

Now we have told our Perl app to use XML::Twig as a module, which gives us access to the subroutines provided by XML::Twig.  You can see a list of all of the possible subroutines at the XML::Twig CPAN page (the first link in this email).
Ok. If everything went ok up to that point, we can now write a simple program.  I am attaching a TBX-Min file to this email for practice.  The first thing we do is create an instance of XML::Twig and define the rules we want our parser to follow.  We do this by writing the following code in the Perl app.  For this test app, we will simply look for the amount of times the element shows up and print it out.

```perl
my $number_of_terms = 0;    # This will be our counter.

my $twig_instance = XML::Twig->new(    # This creates your twig instance and assigns it to the variable $twig_instance.  You can name it whatever you want though.
    twig_handlers    =>    {  # You may recognize that this is a hash, where "twig_handlers" is a key and everything inside of the { }s is the value.  "twig_handlers" is a key specific to XML::Twig and it lets us choose which elements we want to look for and do something when we find them
        term    =>    sub {  # This means, that when the parser finds the closing tag of a element, it will do the code defined in "sub { }". You can define the code elsewhere, but for now, we'll do it this way.
            $number_of_terms++;    # Increment $number_of_terms, since we found one.
            print "Found $number_of_terms terms so far!\n"; # Print out a progress update.
        }
    }
);
```

Now that we have our incrementor, $number_of_terms, and our Twig instance, $twig_instance, with code predefined to do something when Twig finds a element, we actually have to parse our file with our Twig instance.  We do this by using the Twig subroutine “parsefile()” and feeding it a filepath as a variable:

```perl 
my $path_to_TBX = "filepath/to/TBX-Min-sample.tbxm";    # For this example we will just hard code the filepath into our program. You would more likely have the user provide this information either with or @ARGV

$twig_instance->parsefile($path_to_TBX);    # The "->" is how you access the subroutines in our Twig instance (also called an Object).  It just says, run the subroutine "parsefile" which is a part of the $twig_instance object
``` 

Now the program will use our $twig_instance to parse $path_to_TBX!  We need to do one last thing though: Print out our $number_of_terms total!

```perl 
print "Total Number of Terms in File: $number_of_terms\n";
``` 
 
One really important thing to remember about “twig_handlers” is that they do not “trigger” the handler (meaning, they do not run the instruction code we write) until the program encounters the closing tag of the element.  This means that Twig will not trigger the “term” handler when it encounters a start tag () in the TBX-Min file, but will instead trigger when it encounters the end tag (). It works this way so you have access to all of the contents of the term element.  If it triggered at , we would know we hit a term, but we wouldn’t yet know what was inside of it.  Sometimes this is what we want though, so, in addition to the “twig_handlers” key, there is also the “start_tag_handlers” key.  Any handlers you define using that will trigger as soon as the start tag of a term element is found, not waiting until the end tag is found.
I only covered reading files here, since you don’t need to write files with XML::Twig yet.