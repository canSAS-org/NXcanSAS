.. $Id$

=======================================
 reStructuredText Markup Specification
=======================================
Author: David Goodger
Contact: dgoodger@bigfoot.com
Version: 0.2
Date: 2001-05-29

.. sidebar:: Help for reST authors
   
   This 
   `document <http://mail.python.org/pipermail/doc-sig/2001-June/001860.html>`_,
   while old, presents information useful
   to authors of reST documents, such as this manual.
   
   This will be removed in the released 
   version of the NeXus documentation.

reStructuredText_ is plain text that uses simple and intuitive constructs
to indicate the structure of a document. These constructs are equally easy
to read in raw and processed forms. This document is itself an example of
reStructuredText (raw, if you are reading the text file, or processed, if
you are reading an HTML document, for example). reStructuredText is a
candidate markup syntax for the `Python Docstring Processing System`_.

Simple, implicit markup is used to indicate special constructs, such as
section headings, bullet lists, and emphasis. The markup used is as minimal
and unobtrusive as possible. Less often-used constructs and extensions to
the basic reStructuredText syntax may have more elaborate or explicit
markup.

The first section gives a quick overview of the syntax of the
reStructuredText markup by example. More details are given in the `Syntax
Details`_ section.

`Literal blocks`_ are used for examples throughout this document.

-----------------------
 Quick Syntax Overview
-----------------------

A reStructuredText document is made up of body elements, and may be
structured into sections. `Section Structure`_ is indicated through title
style (underlines & optional overlines). Sections contain body elements
and/or subsections.

Here are examples of body elements:

- Paragraphs_ (and `inline markup`_)::

      Paragraphs contain text and may contain inline markup: *emphasis*,
      **strong emphasis**, `interpreted text`, ``inline literals``,
      standalone hyperlinks (http://www.python.org), indirect hyperlinks
      (Python_), internal cross-references (example_), footnote references
      ([1]_).

      Paragraphs are separated by blank lines and are flush left.

- Three types of lists:

  1. `Bullet lists`_::

         - This is a bullet list.

         - Bullets can be '-', '*', or '+'.

  2. `Enumerated lists`_::

         1. This is an enumerated list.

         2. Enumerators may be arabic numbers, letters, or roman numerals.

  3. `Definition lists`_::

         what
             Definition lists associate a term with a definition.

         how
             The term is a one-line phrase, and the definition is one or
             more paragraphs or body elements, indented relative to the
             term.

- `Literal blocks`_::

      Literal blocks are indented, and indicated with a double-colon ('::')
      at the end of the preceeding paragraph::

          if literal_block:
              text = 'is left as-is'
              spaces_and_linebreaks = 'are preserved'
              markup_processing = None

- `Block quotes`_::

      Block quotes consist of indented body elements:

          This theory, that is mine, is mine.

          Anne Elk (Miss)

- Tables_::

      +------------------------+------------+----------+
      | Header row, column 1   | Header 2   | Header 3 |
      +========================+============+==========+
      | body row 1, column 1   | column 2   | column 3 |
      +------------------------+------------+----------+
      | body row 2             | Cells may span        |
      +------------------------+------------+----------+

- Comments_::

      .. Comments begin with two dots and a space. Anything may follow,
         except for the syntax of directives, footnotes, and hyperlink
         targets, described below.

- Directives_::

      .. graphic:: mylogo.png

- Footnotes_::

      .. _[1] A footnote contains indented body elements.

         It is a form of hyperlink target.

- `Hyperlink targets`_::

      .. _Python: http://www.python.org

      .. _example:

      The '_example' target above points to this paragraph.

.. _syntax details:

----------------
 Syntax Details
----------------

Below is a diagram of the hierarchy of element types in reStructuredText.
Elements may contain other elements below them. Element types in
parentheses indicate recursive or one-to-many relationships: sections may
contain (sub)sections, tables contain further body elements, etc. ::

    +------------------------------------------------------------------+
    | document                                                         |
    |                           +--------------------------------------+
    | [root element]            |                           +-------+  |
    |                           | sections  [begin with one | title |] |
    |                           |                           +-------+  |
    |---------------------------+-------------------------+------------|
    | [body elements:]                                    | (sections) |
    |         |         | - lists  |       | - comments   +------------+
    |         |         | - tables |       | - directives |
    | para-   | literal | - block  | foot- | - hyperlink  |
    | graphs  | blocks  |   quotes | notes |   targets    |
    +---------+---------+----------+-------+--------------+
    | [text]+ | [text]  | (body elements)  | [text]       |
    | (inline +---------+------------------+--------------+
    | markup) |
    +---------+

For definitive element hierarchy details, see the "Generic Plaintext
Document Interface DTD" XML document type definition, gpdi.dtd_.
Descriptions below list 'DTD elements' (XML 'generic identifiers')
corresponding to syntax constructs.

.. _whitespace:

Whitespace
==========
Blank lines are used to separate paragraphs and other elements. Blank lines
may be omitted when the markup makes element separation unambiguous.

Indentation is used to indicate, and is only significant in indicating:

- multiple body elements within a list item (including nested lists),
- the definition part of a definition list item,
- block quotes, and
- the extent of literal blocks.

Although spaces are recommended for indentation, tabs may also be used.
Tabs will be converted to spaces. Tab stops are at every 8th column.

.. _escaping mechanism:

Escaping Mechanism
==================
The character set available in plain text documents, 7-bit ASCII, is
limited. No matter what characters are used for markup, they will already
have multiple meanings in written text. Therefore markup characters *will*
sometimes appear in text **without being intended as markup**.

Any serious markup system requires an escaping mechanism to override the
default meaning of the characters used for the markup. In reStructuredText
we use the backslash, commonly used as an escaping character in other
domains.

A backslash followed by any character escapes the character. The escaped
character represents the character itself, and is prevented from playing a
role in any markup interpretation. The backslash is removed from the
output. A literal backslash is represented by two backslashes in a row.

There are two contexts in which backslashes have no special meaning:
literal blocks and inline literals. In these contexts, a single backslash
represents a literal backslash.

.. _section structure:

Section Structure
=================
DTD elements: section, title.

Sections are identified through their titles, which are marked up with
'underlines' below the title text (and, in some cases, 'overlines' above
the title). An underline/overline is a line of non-alphanumeric characters
that begins in column 1 and extends at least as far as the right edge of
the title text. When there an overline is used, the length and character
used must match the underline. There may be any number of levels of section
titles.

Rather than imposing a fixed number and order of section title styles, the
order enforced will be the order as encountered. The first style
encountered will be an outermost title (like HTML H1), the second style
will be a subtitle, the third will be a subsubtitle, and so on.

Below are examples of section title styles::

    ===============
     Section Title
    ===============

    ---------------
     Section Title
    ---------------

    Section Title
    =============

    Section Title
    -------------

    Section Title
    .............

    Section Title
    ~~~~~~~~~~~~~

    Section Title
    *************

    Section Title
    +++++++++++++

    Section Title
    ^^^^^^^^^^^^^

When a title has both an underline and an overline, the title text may be
inset, as in the first two examples above. This is merely aesthetic and not
significant. Underline-only title text may not be inset.

A blank line after a title is optional. All text blocks up to the next
title of the same or higher level are included in a section (or subsection,
etc.).

All section title styles need not be used, nor must any specific section
title style be used. However, a document must be consistent in its use of
section titles: once a hierarchy of title styles is established, sections
must use that hierarchy.

.. _body elements:

Body Elements
=============

.. _paragraphs:

Paragraphs
----------
DTD element: paragraph.

Paragraphs consist of blocks of left-aligned text with no markup indicating
any other body element. Blank lines separate paragraphs from each other and
from other body elements. Paragraphs may contain `inline markup`_.

Syntax diagram::

    +------------------------------+
    | paragraph                    |
    |                              |
    +------------------------------+
    +------------------------------+
    | paragraph                    |
    |                              |
    +------------------------------+

.. _bullet lists:

Bullet Lists
------------
DTD elements: bullet_list, list_item.

A text block which begins with a '-', '*', or '+', followed by whitespace,
is a bullet list item (a.k.a. 'unordered' list item). For example::

    - This is the first bullet list item. The blank line above the first
      list item is required; blank lines between list items (such as below
      this paragraph) are optional. Text blocks must be left-aligned,
      indented relative to the bullet.

    - This is the first paragraph in the second item in the list.

      This is the second paragraph in the second item in the list. The
      blank line above this paragraph is required. The left edge of this
      paragraph lines up with the paragraph above, both indented relative
      to the bullet.

      - This is a sublist. The bullet lines up with the left edge of the
        text blocks above. A sublist is a new list so requires a blank line
        above and below.

    - This is the third item of the main list.

    This paragraph is not part of the list.

Here are examples of **incorrectly** formatted bullet lists::

    - This first line is fine.
    A blank line is required between list items and paragraphs. (Warning)

    - The following line appears to be a new sublist, but it is not:
      - This is a paragraph contination, not a sublist (no blank line).
      - Warnings may be issued by the implementation.

Syntax diagram::

    +------+-----------------------+
    | '- ' | list item             |
    +------| (body elements)+      |
           +-----------------------+

.. _enumerated lists:

Enumerated Lists
----------------
DTD elements: enumerated_list, list_item.

Enumerated lists (a.k.a. 'ordered' lists) are similar to bullet lists, but
use enumerators instead of bullets. An enumerator consists of an
enumeration sequence member and formatting, followed by whitespace. The
following enumeration sequences are recognized:

- arabic numerals: 1, 2, 3, ... (no upper limit).
- uppercase alphabet characters: A, B, C, ..., Z.
- lower-case alphabet characters: a, b, c, ..., z.
- uppercase Roman numerals: I, II, III, IV, ... (no upper limit).
- lowercase Roman numerals: i, ii, iii, iv, ... (no upper limit).

The following formatting types are recognized:

- suffixed with a period: '1.', 'A.', 'a.', 'I.', 'i.'.
- surrounded by parentheses: '(1)', '(A)', '(a)', '(I)', '(i)'.
- suffixed with a right-parenthesis: '1)', 'A)', 'a)', 'I)', 'i)'.

For an enumerated list to be recognized, the following must hold true:

1. The list must consist of multiple adjacent list items (2 or more).
2. The enumerators must all have the same format and sequence type.
3. The enumerators must be in sequence (i.e., '1.', '3.' is not allowed).

It is recommended that the enumerator of the first list item be ordinal-1
('1', 'A', 'a', 'I', or 'i'). Although other start-values will be
recognized, they may not be supported by the output format.

Nested enumerated lists must be created with indentation. For example::

    1. Item 1.

       a) item 1a.
       b) Item 1b.

.. _definition lists:

Definition Lists
-----------------
DTD elements: definition_list, definition_list_item, term, definition.

Each definition list item contains a term and a definition. A term is a
simple one-line paragraph. A definition is a block indented relative to the
term, and may contain multiple paragraphs and other body elements. Blank
lines are required before the term and after the definition, but there may
be no blank line between a term and a definition (this distinguishes
definition lists from `block quotes`_). ::

    term 1
        Definition 1.

    term 2
        Definition 2, paragraph 1.

        Definition 2, paragraph 2.

Syntax diagram::

    +------+
    | term |
    +--+---+-----------------------+
       | defninition               |
       | (body elements)+          |
       +---------------------------+

.. _field lists:

Field Lists
-----------
DTD elements: field_list, field, field_name, field_argument, field_body.

.. XXX Syntax under construction. Comments and suggestions welcome.

Field lists are mappings from field names to field bodies, modeled on
RFC822_ headers. A field name is made up of one or more letters, numbers,
and punctuation, except colons (':') and whitespace. A single colon and
whitespace follows the field name, and this is followed by the field body.
The field body may contain multiple body elements.

Applications of reStructuredText may recognize field names and transform
fields or field bodies in certain contexts. Field names are
case-insensitive. Any untransformed fields remain in the field list as the
document's first body element.

The syntax for field lists has not been finalized. Syntax alternatives:

1. Unadorned RFC822_ everywhere::

       Author: Me
       Version: 1

   Advantages: clean, precedent. Disadvantage: ambiguous (these paragraphs
   are a prime example).

   Conclusion: rejected.

2. Special case: use unadorned RFC822_ for the very first or very last text
   block of a docstring::

       """
       Author: Me
       Version: 1
  
       The rest of the docstring...
       """

   Advantages: clean, precedent. Disadvantages: special case, flat
   (unnested) field lists only.

   Conclusion: accepted, see below.

3. Use a directive::

       .. fields::

          Author: Me
          Version: 1

   Advantages: explicit and unambiguous. Disadvantage: cumbersome.

4. Use Javadoc-style::

       @Author: Me
       @Version: 1
       @param a: integer

   Advantages: unambiguous, precedent, flexible. Disadvantages:
   non-intuitive, ugly.

One special context is defined for field lists. A field list as the very
first non-comment block, or the second non-comment block immediately after
a title, is interpreted as document bibliographic data. No special syntax
is required, just unadorned RFC822_. The first block ends with a blank
line, therefore field bodies must be single paragraphs only and there may
be no blank lines between fields. The following field names are recognized
and transformed to the corresponding DTD elements listed, child elements of
the 'document' element. No ordering is imposed on these fields:

- Title: title
- Subtitle: subtitle
- Author/Authors: author
- Organization: organization
- Contact: contact
- Version: version
- Status: status
- Date: date
- Copyright: copyright

This field-name-to-element mapping can be extended, or replaced for other
languages. See the implementation documentation for details.

.. _literal blocks:

Literal Blocks
--------------
DTD element: literal_block.

Two colons ('::') at the end of a paragraph signifies that all following
**indented** text blocks comprise a literal block. No markup processing is
done within a literal block. It is left as-is, and is typically rendered in
a monospaced typeface::

    This is a typical paragraph. A literal block follows::

        for a in [5,4,3,2,1]:   # this is program code, formatted as-is
            print a
        print "it's..."
        # a literal block continues until the indentation ends

    This text has returned to the indentation of the first paragraph, is
    outside of the literal block, and therefore treated as an ordinary
    paragraph.

When '::' is immediately preceeded by whitespace, both colons will be
removed from the output. When text immediately preceeds the '::', *one*
colon will be removed from the output, leaving only one (i.e., '::' will be
replaced by ':'). When '::' is alone on a line, it will be completely
removed from the output; no empty paragraph will remain.

In other words, these are all equivalent:

1. Minimized::

      Paragraph::

          Literal block

2. Partly expanded::

      Paragraph: ::

          Literal block

3. Fully expanded::

      Paragraph:

      ::

          Literal block

The minimum leading whitespace will be removed from each line of the
literal block. Other than that, all whitespace (including line breaks) is
preserved. Blank lines are required before and after a literal block, but
these blank lines are not included as part of the literal block.

Syntax diagram::

    +------------------------------+
    | paragraph                    |
    | (ends with '::')             |
    +------------------------------+
       +---------------------------+
       | literal block             |
       +---------------------------+

.. _block quotes:

Block Quotes
------------
DTD element: block_quote.

A text block that is indented relative to the preceeding text, without
markup indicating it to be a literal block, is a block quote. All markup
processing (for body elements and inline markup) continues within the block
quote::

    This is an ordinary paragraph, introducing a block quote:

        "It is my business to know things. That is my trade."

        --Sherlock Holmes

Blank lines are required before and after a block quote, but these blank
lines are not included as part of the block quote.

Syntax diagram::

    +------------------------------+
    | (current level of            |
    | indentation)                 |
    +------------------------------+
       +---------------------------+
       | block quote               |
       | (body elements)+          |
       +---------------------------+

.. _tables:

Tables
------
DTD elements: table, tgroup, colspec, thead, tbody, row, entry.

Tables are described with a visual outline made up of the characters '-',
'=', '|', and '+'. The hyphen ('-') is used for horizontal lines (row
separators). The equals sign ('=') may be used to separate optional header
rows from the table body. The vertical bar ('|') is used for vertical lines
(column separators). The plus sign ('+') is used for intersections of
horizontal and vertical lines.

Each cell contains zero or more body elements. Example::

    +------------------------+------------+----------+----------+
    | Header row, column 1   | Header 2   | Header 3 | Header 4 |
    | (header rows optional) |            |          |          |
    +========================+============+==========+==========+
    | body row 1, column 1   | column 2   | column 3 | column 4 |
    +------------------------+------------+----------+----------+
    | body row 2             | Cells may span columns.          |
    +------------------------+------------+---------------------+
    | body row 3             | Cells may  | - Table cells       |
    +------------------------+ span rows. | - contain           |
    | body row 4             |            | - body elements.    |
    +------------------------+------------+---------------------+

As with other body elements, blank lines are required before and after
tables. Tables' left edges should align with the left edge of preceeding
text blocks; otherwise, the table is considered to be part of a block
quote.

Comment Blocks
--------------
A comment block is a text block:

- whose first line begins with '.. ' (the 'comment start'),
- whose second and subsequent lines are indented relative to the first, and
- which ends with an unindented line.

Comments are analogous to bullet lists, with '..' as the bullet. Blank
lines are required between comment blocks and other elements, but are
optional between comment blocks where unambiguous.

The comment block syntax is used for comments, directives, footnotes, and
hyperlink targets.

.. _comments:

Comments
........
DTD element: comment.

Arbitrary text may follow the comment start and will be processed as a
comment element, possibly being removed from the processed output. The only
restriction on comments is that they not use the same syntax as directives,
footnotes, or hyperlink targets.

Syntax diagram::

    +-------+----------------------+
    | '.. ' | comment block        |
    +--+----+                      |
       |                           |
       +---------------------------+

.. _directives:

Directives
..........
DTD element: directive.

Directives are indicated by a comment start followed by a single word (the
directive type, regular expression '`[a-zA-Z][a-zA-Z0-9_-]*`'), two colons,
and whitespace. Two colons are used for these reasons:

- To avoid clashes with common comment text like::

      .. Danger: modify at your own risk!

- If an implementation of reStructuredText does not recognize a directive
  (i.e., the directive-handler is not installed), the entire directive
  block (including the directive itself) will be treated as a literal
  block, and a warning generated. Thus '::' is a natural choice.

Directive names are case-insensitive. Actions taken in response to
directives and the interpretation of data in the directive block or
subsequent text block(s) are directive-dependent.

No directives have been defined by the core reStructuredText specification.
The following are only examples of *possible uses* of directives.

Directives can be used as an extension mechanism for reStructuredText. For
example, here's how a graphic could be placed::

    .. graphic:: mylogo.png

A figure (a graphic with a caption) could be placed like this::

    .. figure:: larch.png

       The larch.

Directives can also be used as pragmas, to modify the behavior of the
parser, such as to experiment with alternate syntax.

Syntax diagram::

    +-----------------+------------+
    | '.. ' type '::' | directive  |
    +--+--------------+ block      |
       |                           |
       +---------------------------+

.. _hyperlink targets:

Hyperlink Targets
.................
DTD element: target.

Hyperlink targets consist of a comment start ('.. '), an underscore, the
hyperlink name (no trailing underscore), a colon, whitespace, and a link
block. Hyperlink targets go together with `indirect hyperlinks`_ and
`internal hyperlinks`_. Internal hyperlink targets have empty link blocks;
they point to the next element. Indirect hyperlink targerts have an
absolute or relative URI in their link blocks.

If a hyperlink name contains colons, either:

- the phrase must be enclosed in backquotes::

      .. _`FAQTS: Computers: Programming: Languages: Python`:
         http://python.faqts.com/

- or the colon(s) must be backslash-escaped in the link target::

      .. _Chapter One\: 'Tadpole Days':

      It's not easy being green...

Whitespace is normalized within hyperlink names, which are
case-insensitive.

Syntax diagram::

    +-----------------+------------+
    | '.. _' name ':' | link       |
    +--+--------------+ block      |
       |                           |
       +---------------------------+

.. _footnotes:

Footnotes
.........
DTD elements: footnote, label.

Footnotes are similar to hyperlink targets: a comment start, an underscore,
open square bracket, footnote label, close square bracket, and whitespace.
To differentiate footnotes from hyperlink targets:

- the square brackets are used,
- the footnote label may not contain whitespace,
- no colon appears after the close bracket.

Footnotes may occur anywhere in the document, not necessarily at the end.
Where or how they appear in the processed output depends on the output
formatter. Here is a footnote, referred to in `Footnote References`_::

    .. _[GVR2001] Python Documentation, van Rossum, Drake, et al.,
       http://www.python.org/doc/

Syntax diagram::

    +-------------------+----------+
    | '.. _[' label ']' | footnote |
    +--+----------------+          |
       | (body elements)+          |
       +---------------------------+

.. _inline markup:

Inline Markup
=============
Inline markup is the markup of text within a text block. Inline markup
cannot be nested.

There are six inline markup constructs. Four of the constructs (emphasis_,
`strong emphasis`_, `interpreted text`_, and `inline literals`_) use
start-strings and end-strings to indicate the markup. The 
`indirect hyperlinks`_ construct 
(shared by `internal hyperlinks`_) uses an end-string
only. `Standalone hyperlinks`_ are interpreted implicitly, and use no extra
markup.

The inline markup start-string and end-string recognition rules are as
follows:

1. Inline markup start-strings must be immediately preceeded by whitespace
   and zero or more of single or double quotes, '(', '[', or '{'.

2. Inline markup start-strings must be immediately followed by
   non-whitespace.

3. Inline markup end-strings must be immediately preceeded by
   non-whitespace.

4. Inline markup end-strings must be immediately followed by zero or more
   of single or double quotes, '.', ',', ':', ';', '!', '?', '-', ')', ']',
   or '}', followed by whitespace.

5. If an inline markup start-string is immediately preceeded by a single or
   double quote, '(', '[', or '{', it must not be immediately followed by
   the corresponding single or double quote, ')', ']', or '}'.

6. An inline markup end-string must be separated by at least one character
   from the start-string.

7. Except for the end-string of `inline literals`_, an unescaped backslash
   preceeding a start-string or end-string will disable markup recognition.
   See `escaping mechanism`_ above for details.

For example, none of the following are recognized as inline markup
start-strings: ' * ', '"*"', "'*'", '(*)', '(* ', '[*]', '{*}', '\*', ' `
', etc.

.. _emphasis:

Emphasis
--------
DTD element: emphasis.

Text enclosed by single asterisk characters (start-string = end-string =
'*') is emphasized::

    This is *emphasized text*.

Emphasized text is typically displayed in italics.

.. _strong emphasis:

Strong Emphasis
---------------
DTD element: strong.

Text enclosed by double-asterisks (start-string = end-string = '**') is
emphasized strongly::

    This is **strong text**.

Strongly emphasized text is typically displayed in boldface.

.. _interpreted text:

Interpreted Text
----------------
DTD element: interpreted.

Text enclosed by single backquote characters (start-string = end-string =
'`') is interpreted::

    This is `interpreted text`.

The semantics of interpreted text are domain-dependent. It can be used as
implicit or explicit descriptive markup (such as for program identifiers,
as in the `Python Extensions`_ to reStructuredText), for cross-reference
interpretation (such as index entries), or for other applications where
context can be inferred. The role of the interpreted text may be inferred
implicitly. The role of the interpreted text may also be indicated
explicitly, either a prefix (role + colon + space) or a suffix (space +
colon + role), depending on which reads better::

    `role: interpreted text`

    `interpreted text :role`

.. _inline literals:

Inline Literals
---------------
DTD element: literal.

Text enclosed by double-backquotes (start-string = end-string = '``') is
treated as inline literals::

    This text is an example of ``inline literals``.

Inline literals may contain any characters except two adjacent backquotes
in an end-string context (according to the recognition rules above). No
markup interpretation (including backslash-escape interpretation) is done
within inline literals. Line breaks are *not* preserved; other whitespace
is not guaranteed to be preserved.

Inline literals are useful for short code snippets. For example::

    The regular expression ``[+-]?(\d+(\.\d*)?|\.\d+)`` matches
    non-exponential floating-point numbers.

.. _hyperlinks:

Hyperlinks
----------
Hyperlinks are indicated by a trailing underscore, '_', except for
`standalone hyperlinks`_ which are recognized independently.

.. _standalone hyperlinks:

Standalone Hyperlinks
.....................
DTD element: link.

An absolute URI_ within a text block is treated as a general external
hyperlink with the URI itself as the link's text (start-string = end-string
= '', the empty string). For example::

    See http://www.python.org for info.

would be marked up in HTML as::

    See <A HREF="http://www.python.org">http://www.python.org</A> for info.

.. _URI:

Uniform Resource Identifier:
	URIs are a general form of URLs (Uniform Resource Locators). 
	For the syntax of URIs see RFC2396_.

.. _indirect hyperlinks:

Indirect Hyperlinks
...................

DTD element: link.

Indirect hyperlinks consist of two parts. In the text body, there is a
source link, a name with a trailing underscore (start-string = '',
end-string = '_'; start-string = '\`', end-string = '\`_')::

    See the Python_ home page for info.

Somewhere else in the document is a target link containing a URI (see
`Hyperlink Targets`_ for a full description)::

    .. _Python: http://www.python.org

After processing into HTML, this should be expressed as::

    See the <A HREF="http://www.python.org">Python</A> home page for info.

--------------------

See the Python_ home page for info.

.. _Python: http://www.python.org

--------------------

Phrase-links (a hyperlink whose name is a phrase, two or more
space-separated words) can be expressed by enclosing the phrase in
backquotes and treating the backquoted text as a link name::

    Want to learn about `my favorite programming language`_?

    .. _my favorite programming language: http://www.python.org

--------------------

Want to learn about `my favorite programming language`_?

.. _my favorite programming language: http://www.python.org

--------------------

Whitespace is normalized within hyperlink names, which are
case-insensitive.

.. _internal hyperlinks:

Internal Hyperlinks
...................
DTD element: link.

Internal hyperlinks connect one point to another within a document. They
are identical to indirect hyperlinks (start-string = '', end-string = '_';
start-string = '\`', end-string = '\`_') except that there is no URI in the
target link. See `Hyperlink Targets`_ for a full description. For example::

    Clicking on this internal hyperlink will take us to the target_ below.

    .. _target:

    The hyperlink target above points to this paragraph.

--------------------

Clicking on this internal hyperlink will take us to the target_ below.

.. _target:

The hyperlink target above points to this paragraph.

--------------------

.. _footnote references:

Footnote References
...................
DTD element: footnote_reference.

Footnote references consist of a square-bracketed label (no whitespace),
with a trailing underscore (start-string = '[', end-string = ']_')::

    Please refer to the fine manual [GVR2001]_.

See Footnotes_ for the footnote itself.

.. _reStructuredText: http://structuredtext.sf.net
.. _Python Docstring Processing System: http://docstring.sf.net
.. _Doc-SIG: http://www.python.org/sigs/doc-sig/
.. _gpdi.dtd: http://docstring.sf.net/spec/gpdi.dtd
.. _RFC822: http://www.rfc-editor.org/rfc/rfc822.txt
.. _RFC2396: http://www.rfc-editor.org/rfc/rfc2396.txt
.. _Python Extensions: http://structuredtext.sf.net/spec/pyextensions.txt

