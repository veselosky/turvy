# Turvy: An inside-out template language

Turvy is a concept of an inside-out template language. It implements templates
in terms of a prototype and a transformation sheet written in a CSS-like syntax.

## Theory

The process of producing HTML from software involves three things: some data
(the Context), some HTML, and some instructions for how to combine the Context
and the HTML. In the olden days, the data and instructions were written as
imperative code in your programming language (Perl!), and the HTML was held as
strings in variables. It didn't take long for folks to learn to hate maintaining
this.

To solve this problem, template systems were invented. Templates hold the HTML
in separate files, and *embed the instructions within the HTML*. Programmers
would take their data, hand it to the template system, and the template system
would execute the instructions.

This only solved half the problem. Although the HTML had now been separated from
the code implementing the data structure (good), it still had not been separated
from the *instructions* for binding the data to HTML. In most template systems,
this leaves you with HTML that is basically broken, because it is interrupted by
template instructions that interfere with its proper function as a declarative
description of the document structure. This arrangement creates workflow
problems, because it is often difficult to view this broken HTML in a browser.
Also, the person styling the web site is often not the same person charged with
writing the template instructions, and these two people tend to roadblock each
other.

Turvy aims to complete the separation of data binding in HTML templates by
moving the instructions *outside* the HTML, much the same way presentation was
separated by moving style information out of the HTML and into a CSS stylesheet.
In fact, Turvy uses the CSS syntax to define its data binding.

## Turvy Transformation Sheets

Unlike generic text template systems, Turvy intentionally confines itself only
to the web domain. In doing so, it leverages existing technology and syntax,
making it very easy to understand and apply to web projects.  Turvy treats
templates (which it calls prototypes) not as strings, but as a hierarchy of
element nodes forming a document. Turvy takes a "template context" in the form
of a JSON object, and binds it to the prototype using a Turvy Transformation
Sheet written in CSS syntax.

Let's discuss this small example:

    /* This is a Turvy Transformation Sheet */
    header .site-title {content: $.site.name;}

Here's the data that we are binding:

    {"site": {"name": "My Example Site", "logo": "http://example.com/logo.jpg"}}

If you speak the language of the web, you probably can guess what will happen
when Turvy combines these with a prototype HTML page, but here's the run down.

The transformation sheet is made up of a series of Rule Sets (in this case just
one of them). A Rule Set consists of a Selector and a Declaration Block. The
Selector instructs Turvy how to find the document node(s) on which to operate.
The Declaration Block instructs Turvy how to bind data to the node.

In this example, the rule selects any element(s) inside a header element and
having a class of "site-title".

The Declaration Block may have a series of declarations (here just one)
describing data to be bound to the element, or operations to be performed on the
element. Data is described using JSONPath, which any JavaScript developer should
be able to understand. In this rule, the content of the selected element will be
set to the value of the site.name attribute in the data object (replacing any
existing content).

Declarations of this form are the most common. The declaration names a property
of the element to be manipulated on the left side of the colon, and a new value
for that property on the right side.

Here's another example:

    img#logo {
        src: url($.site.logo);
        alt: "Company Logo"
    }

In this example, the img tag with ID "logo" has its src and alt properties
modified. Note that the logo URL comes from the data object, but the alt text is
hard coded into the transformation sheet. The url() function is borrowed from
CSS.

For more discussion, take a look at the example transform.css in this directory.

## Status: Does this work?

This does not work *yet*. I'm currently playing with the syntax to discover what
transformations and functions are really necessary. The outcome of this will be
a specification for Turvy. Following the specification, there will be an
implementation. Please send me pull requests if you have some ideas or use cases
that could be used to inform the specification.
