---
date: 2016-12-24
categories:
    - perl
    - xml
    - xml::twig
---

# Semi-Beginner’s Guide to Reading a Simple TBX file with XML::Twig Part II

This post covers how to get the text value of an element, and how to get the attributes of an element.

You can get the contents of a tag quite easily. When Twig runs into one of your twig handlers (like `term`), it runs the subroutine you define next to your handler (like we covered last time). However, it is also important to know that it passes a couple arguments into your subroutine using the `@_` array. The first is your `$twig` object itself, and the second is the Element (Elt) object. The element object has a lot of methods tied to it specifically (read about them here http://search.cpan.org/dist/XML-Twig/Twig.pm#XML::Twig::Elt). The one you want is the `XML::Twig::Elt->text_only()` method. This method grabs the text inside of your tags, but ignores the text inside of a nested element.

So, if this is our XML to parse:

```xml
<term> This is a great sentence! <i> It sure is!</i> <term>
```

And this is our code:

```perl
my $twig_instance = XML::Twig->new(
  twig_handlers => {
    term => sub { # Twig automatically passes a couple variables into these subroutines for us in the @_ variable. These are:
      my ($twig, $elt) = @_; # the $twig itself (this is the entire twig object) and the $elt (element, or node).
      print $elt->text_only()."\n"; # In this case, the value of $elt is the 'term' element object. You can get the content of the term element using
# the $elt->text() or $elt->text_only() methods. you can read about the difference in XML::Twig documentation
    }
  }
);
```

This will only output “This is a great sentence!”, but ignore the “It sure is!”. If you want to grab it all and output “This is a great sentence! It sure is!”, you would use `$elt->text()`, rather than `$elt->text_only()`.

To handle attributes:

XML to parse:

```xml
<languages source="en" target="fr" />
```

This means:

* The element is the `languages` element
* It has two `attributes` or values which are declared within the opening tag of an element. They are:
    * `source` attribute, with a value of `en` (for english)
    * `target` attribute, with a value of `fr` (for french)
 

Using the `$elt` object which `XML::Twig` gives us, we can get the values of the attributes:

```perl
my $source = $elt->att("source");
my $target = $elt->att("target");
```

The value of $source will be `en` and the value of `$target` will be `fr`!