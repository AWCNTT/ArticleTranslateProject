---
layout: post
title: "Android Quick Tip: Formatting Text with Html.fromHtml()"
date: 2014-08-15 15:32
comments: true
categories: Html.fromHtml()
---

#Android Quick Tip: Formatting Text with Html.fromHtml()

Android offers you the possibility to easily format text with HTML markup. Thus it’s easy to create text like this:

![Sample screen showing some formattings](http://d3a0c3fa3t59bz.cloudfront.net/wordpress/wp-content/uploads/2012/08/sampleScreen.png)

You probably are going to use bold or italics the most, but there are many more supported.

Here is the list of all supported tags. You can find it in the source of [android.text.Html](https://android.googlesource.com/platform/frameworks/base/+/refs/heads/master/core/java/android/text/Html.java)‘s inner class HtmlToSpannedConverter:

#### Supported HTML-Tags
| Ta              | Format            | 
|:----------------|:------------------|
| b, strong       | Bold              |	
|i, em, cite, dfn |	Italics            |
|u                | Underline         |
|sub              |	Subtext            |
|sup              |	Supertext          |
|big              | Big              |
|small            |	Small              |
|tt               |	Monospace          |
|h1 … h6          |	Headlines          |
|img              |	Image              |
|font             |	Font face and color|
|blockquote       | For longer quotes  |
|a                |	Link               |
|div, p	          | Paragraph          |
|br	              | Linefeed           |

#### Formatting text from your strings.xml

If you want to support text formatting from within your strings.xml file, you have to escape the tags – or use a CDATA section (thanks to [Jose Miguel](http://www.conzebit.com/) for pointing this out). Otherwise Android simply ignores them when reading the resource file.

To escape the tags you just need to replace all "<" characters. Luckily you do not have to escape the ">" characters as well. That way the HTML structure is still at least kind of readable. But only kind of.

If you use many HTML tags a CDATA section is better. For the sample above it looks like this:

```
<string name="htmlFormattedText">
      <![CDATA[
      <p>Text with markup for <strong>bold</strong>
      and <em>italic</em> text.</p>
      <p>There is also support for a 
      <tt>teletype-style</tt> font. 
      But no use for the <code>code</code>
      tag!</p>
      ]]></string>
```

Even if you can add a lot of HTML tags, you are better off using only minor styling as mixing too much styles makes your text look uneasy instead of being more striking.

The following snippet shows how to use this string from within your Java code:

```
TextView view = (TextView)findViewById(R.id.sampleText);
String formattedText = getString(R.string.htmlFormattedText);
Spanned result = Html.fromHtml(formattedText);
view.setText(result);
```
Alternatives to consider

For longer texts that use HTML tags, I recommend to use raw files instead.

For more complicated formatting a [WebView](https://developer.android.com/reference/android/webkit/WebView.html) probably would be better.

---


原文地址：[http://www.grokkingandroid.com/android-quick-tip-formatting-text-with-html-fromhtml/](http://www.grokkingandroid.com/android-quick-tip-formatting-text-with-html-fromhtml/)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)