This is a guide to how character encodings are used within WeeWX. 

## What are encodings? 

If you're a seasoned hands at character encodings, you can skip this section. If not, you should
stop and read this classic 20-year-old article: [_The Absolute Minimum Every Software
Developer Absolutely, Positively Must Know About Unicode and Character Sets (No
Excuses!)_](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/).

Fortunately, since the article was written, the rainbow of encodings has collapsed down to pretty
much one, UTF-8, making life much simpler.

## WeeWX encodings

There are four places where encodings come into play within WeeWX:

1. In the Python code;
2. The encoding of text created by the Cheetah generator and inserted into templates;
3. The encoding in the templates themselves; and
4. The encoding of the resultant HTML file.

### Python code

Back in the old days, if you used an encoding other than simple ASCII in your Python source code,
you had to tell the Python interpreter what that encoding was. However, with Python 3, that problem
has gone away: by default, Python 3 source code uses UTF-8. Nevertheless, it's useful to understand
that if your program has a line such as this

    label = "°C"

the actual degree symbol is being represented using a UTF-8 encoding, which, in hex, is `C2 B0`.
So, the two characters `°` and `C` actually take _three_ bytes to encode.

### Cheetah generator

The Cheetah generator is responsible for taking tags such as `$current.outTemp` and turning them
into a byte sequence that is inserted into a template. What encoding that byte sequence will use is
determined by the option `encoding` in the `[CheetahGenerator]` section of `skin.conf`. So, 
a directive such as

```
[CheetahGenerator]
  encoding = html_entities
```

tells the WeeWX to use HTML entities to encode any non-ASCII characters. As an example, if the
current temperature is 20C, then the tag `$current.outTemp` will result in either

    20 &deg;C

or, possibly,

    20 &#176;C

By contrast, if you were to specify UTF-8:

```
[CheetahGenerator]
  encoding = utf_8
```

then the emitted byte sequence would use UTF-8 encoding:

    20 °C

which, in hex, is the byte sequence

    32 30 20 C2 B0 43
     2  0      °    C

Note how it takes two bytes, `C2 B0`, to represent the degree sign.

### Template encoding

The Cheetah generator uses _templates_, which are source files that include tags that are to be 
replaced by the engine. The Cheetah directive `#encoding` tells the generator what encoding
a template uses. This is why most WeeWX templates start with:

```
#errorCatcher Echo
#encoding UTF-8
```

Almost always you want UTF-8, although there may be instances where all the special characters in
the template have been encoded using HTML entities, so the template itself is actually in pure
ASCII. If that's the case, a UTF-8 encoding doesn't hurt, but you could alternatively specify
`#encoding ascii`.

Note that this encoding is different from the encoding that will be used by the tag after
substitution. It's entirely possible to have the template in ASCII, but the substitutions use
UTF-8, so the result is a mix. Which brings us to the final kind of encoding directive.

### HTML file

The job of the final directive, the `<meta>` tag, is to give the browser a clue of what encoding
the HTML file uses

```
<html lang="en">
<head>
    <title>weewx: Documentation</title>
    <meta charset="UTF-8">
</head>
```

Again, these days, this is almost always in UTF-8.

To find out what character encoding your browser is using, open up the
"Developer's Tools" window, navigate to the console, and type `document.characterSet`.

## Gotchas

A common problem is special characters in a _text_  (not HTML) file. These are files with the
suffix `.txt`, instead of `.html`. When a browser encounters a file ending in `.txt` it treats
the contents as raw text and does not try to interpret any HTML tags within it, including 
a `<meta>` tag. Because it ignores any possible `<meta>` tag, it does not know what encoding the
file uses, so it has to guess.

Most browsers guess `cp1252`, aka `Windows-1252`. This means that if the file
includes special characters that cannot be encoded in `cp1252`, then they will
end up looking "funny". This is a common problem with NOAA reports (which are
text files) with location names that include special characters that cannot be
encoded in `cp1252`.

There are three solutions:

1. Specify an encoding of `normalized_ascii`. The Cheetah generator will turn something like
`crêpe` into `crepe`. The results won't be correct, but they will be recognizable.

2. Specify an encoding of `cp1252`. This way, the output of the Cheetah generator will match what
the browser is expecting. Of course, this will only work if all the characters in the location name
can be represented in `cp1252`. If not, you will end up with a garbled location name.

3. Wrap the text file in an HTML file that specifies UTF-8 encoding. This is how the Seasons
skin does it, provided you always navigate to the NOAA files via the title bar drop
down option list.

What will _not_ work is specifying an encoding of UTF-8. Remember, the browser
thinks the file is in `cp1252` so, for example, the degree symbol, which takes
two bytes to encode using UTF-8, is instead interpreted by the browser as two
characters encoded in `cp1252`, in this case, the character 'Á' and the
character '⁰'.
