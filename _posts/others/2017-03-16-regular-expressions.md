---
layout: post
title:  "55分钟学会正则表达式"
categories: 其他
tags:  正则表达式
author: Geng
---

* content
{:toc}


转载自[Learn regular expressions in about 55 minutes](https://qntm.org/files/re/re.html) 




<html xmlns="http://www.w3.org/1999/xhtml"> 
<head> 
<meta http-equiv="Content-Type" content="application/xhtml+xml; charset=utf-8" /> 
<link rel="shortcut icon" href="../../page/favicon.ico"/> 
<title>Regular Expressions</title> 
<script type="text/javascript">
// For flipping between displayed things
var toggle = function (id) {
  var e = document.getElementById(id);
  e.style.display = e.style.display === "none" ? "" : "none";
};
</script>
<style type="text/css">
@font-face {
  font-family: 'Ubuntu Mono';
  font-style: normal;
  font-weight: 700;
src: local('Ubuntu Mono Bold'), local('UbuntuMono-Bold'), url('https://themes.googleusercontent.com/static/fonts/ubuntumono/v3/ceqTZGKHipo8pJj4molytjqR_3kx9_hJXbbyU8S6IN0.woff') format('woff');
}
<!-- body { -->
<!-- margin: 2em; -->
<!-- padding: 0; -->
<!--          font-family: Tahoma; -->
<!-- } -->
h1 {
  font-size: 2em;
}
h2 {
  margin-top: 1.8em;
  font-size: 1.8em;
}
h3 {
  margin-top: 1.6em;
  font-size: 1.6em;
color: #777777;
}
h2, h3 {
  border-bottom: 1px dotted gray;
}
h4 {
  margin-top: 1em;
  font-size: 1em;
  font-weight: bold;
}
pre {
  margin-left: 2em;
}
pre, code {
color: blue;
       font-family: "Ubuntu Mono", monospace;
}
.re {
color: green;
}
code em, pre em {
  font-style: normal;
  text-decoration: underline;
}
div.exercise {
  background-color: #d0ffd0;
margin: 1em 0;
padding: 1em;
}
div.warning {
  background-color: #ffD0D0;
margin: 1em 0;
padding: 1em;
}
div.exercise > :first-child,
  div.warning > :first-child {
    margin-top: 0;
  }
div.exercise > :last-child,
  div.warning > :last-child {
    margin-bottom: 0;
  }
</style>
</head>

<!-- https://fonts.googleapis.com/css?family=Ubuntu+Mono:400,700|Roboto+Slab:400,700 -->
<body>
<h1>Learn regular expressions in about 55 minutes</h1>
<h4><a href="https://qntm.org/re">By Sam Hughes</a></h4>

<p>Regular expressions ("regexes") are <strong>supercharged Find/Replace string operations</strong>. Regular expressions are used when editing text in a text editor, to:</p>
<ul>
<li>check whether the text contains a certain <i>pattern</i></li>
<li><i>find</i> those pattern matches, if there are any</li>
<li>pull <i>information</i> (i.e. substrings) out of the text</li>
<li>make <i>modifications</i> to the text.</li>
</ul>
<p>As well as text editors, almost every high-level programming language includes support for regular expressions. In this context "the text" is just a string variable, but the operations available are the same. Some programming languages (Perl, JavaScript) even provide dedicated syntax for regular expression operations.</p>

<h4>But what are they?</h4>
<p>A regular expression is just a <i>string</i>. There's no length limit, but typically the string is quite short. Some examples are:</p>
<ul>
<li><code class="re">I had a \S+ day today</code></li>
<li><code class="re">[A-Za-z0-9\-_]{3,16}</code></li>
<li><code class="re">\d\d\d\d-\d\d-\d\d</code></li>
<li><code class="re">v(\d+)(\.\d+)*</code></li>
<li><code class="re">TotalMessages="(.*?)"</code></li>
<li><code class="re">&lt;[^&lt;&gt;]&gt;</code></li>
</ul>
<p>The string is actually an extremely tiny <i>computer program</i>, and regular expression syntax is a small, terse, <i>domain-specific programming language</i>. Bearing this in mind, it shouldn't surprise you to learn that:</p>
<ul>
<li>Every regular expression can be broken down into a sequence of <i>instructions</i>. "Find this, then find that, then find one of these..."</li>
<li>A regular expression has <i>input</i> (the text) and <i>output</i> (pattern matches, and sometimes the modified text).</li>
<li>There are syntax errors - not every string is a legal regular expression!</li>
<li>The syntax has some quirks and, arguably, horrors.</li>
<li>A regular expression can sometimes be <i>compiled</i> to run faster.</li>
</ul>

<p>There are also significant variations in implementation. For this document I'm going to stay focused on the core syntax which is shared by almost every regular expression implementation.</p>

<div class="exercise">
<h4>Exercise</h4>
<p>Get a text editor which supports regular expressions. I prefer <a href="http://notepad-plus-plus.org/">Notepad++</a>.</p>
<p>Download a long prose story such as <a href="http://www.gutenberg.org/cache/epub/35/pg35.txt">Project Gutenberg's version of H. G. Wells' <i>The Time Machine</i></a> and open it.</p>
<p>Download a dictionary such as <a href="words.zip">this</a>, unzip it and open it.</p>
<p>That's all for right now. There will be more exercises shortly.</p>
</div>

<div class="warning">
<p><strong>Note:</strong> Regular expressions are completely incompatible with <a href="http://en.wikipedia.org/wiki/Glob_%28programming%29">file globbing</a> syntax, such as <code>*.xml</code>.</p>
</div>

<h2>Basic regular expression syntax</h2>

<h3>Literals</h3>

<p>Regular expressions contain a mixture of <i>literal</i> characters, which just represent themselves, and special characters called <i>metacharacters</i>, which do special things.</p>
<p>Here are those examples again. I'll underline the metacharacters.</p>
<ul>
<li><code class="re">I had a <em>\S+</em> day today</code></li>
<li><code class="re"><em>[A-Za-z0-9\-_]{3,16}</em></code></li>
<li><code class="re"><em>\d\d\d\d</em>-<em>\d\d</em>-<em>\d\d</em></code></li>
<li><code class="re">v<em>(\d+)(\.\d+)*</em></code></li>
<li><code class="re">TotalMessages="<em>(.*?)</em>"</code></li>
<li><code class="re">&lt;<em>[^&lt;&gt;]*</em>&gt;</code></li>
</ul>
<p>Most characters, including all alphanumeric characters, occur as literals. This means that they find themselves. For example, the regular expression</p>
<pre class="re">cat</pre>
<p>means "find a <code>c</code>, followed by an <code>a</code>, followed by a <code>t</code>".</p>
<p>So far so good. This is exactly like</p>
<ul>
<li>a conventional Find dialogue</li>
<li>Java's <code>String.indexOf()</code> function</li>
<li>PHP's <code>strpos()</code> function</li>
<li>etc.</li>
</ul>
<p><strong>Note:</strong> Unless otherwise specified, regular expressions are case-sensitive. However, almost all implementations provide a flag to enable case-insensitivity.</p>
<p><strong>Another note:</strong> It is important to know whether "the text" is a sequence of <i>bytes</i> or a sequence of <i>Unicode characters</i>. In plain ASCII, the two are the same, and you can get a long way without needing to know the difference...</p>

<h3>The dot</h3>
<p>Our first metacharacter is the full stop, <code class="re">.</code>. A <code class="re">.</code> finds any single character. The regular expression</p>
<pre class="re">c.t</pre>
<p>means "find a <code>c</code>, followed by any character, followed by a <code>t</code>".</p>
<p>In a piece of text, this will find <code>cat</code>, <code>cot</code>, <code>czt</code> and even the literal string <code>c.t</code> (<code>c</code>, full stop, <code>t</code>), but not <code>ct</code>, or <code>coot</code>.</p>
<p>Whitespace is significant in regular expressions. The regular expression</p>
<pre class="re">c t</pre>
<p>means "find a <code>c</code>, followed by a space, followed by a <code>t</code>".</p>
<p>Any metacharacter can be escaped using a backslash, <code>\</code>. This turns it back into a literal. So the regular expression</p>
<pre class="re">c\.t</pre>
<p>means "find a <code>c</code>, followed by a full stop, followed by a <code>t</code>".</p>
<p>The backslash is a metacharacter, which means that it too can be escaped using a backslash. So the regular expression</p>
<pre class="re">c\\t</pre>
<p>means "find a <code>c</code>, followed by a backslash, followed by a <code>t</code>".</p>

<div class="warning">
<p><strong>Caution!</strong> In some implementations, <code class="re">.</code> finds any character <em>except a line break</em>. The meaning of "line break" can vary from implementation to implementation, too. Check your documentation. For this document, however, I'll assume that <code class="re">.</code> finds any character.</p>
<p>In either case, there is usually a flag to adjust this behaviour, which is usually called <code>DOTALL</code> or similar.</p>
</div>

<div class="exercise">
<h4>Exercise</h4>
<p>In the dictionary, using regular expressions and only what you know so far, find the word(s) with two <code>z</code>s that are as far apart as possible.</p>
<h4><button onclick="toggle('a1');">Answer</button></h4>
<p id="a1" style="display: none;">The regular expression you should end up with is <code class="re">z.......z</code> and you should find four words: <code>razzamatazz</code>, <code>razzamatazzes</code>, <code>zwischenzug</code> and <code>zwischenzugs</code>.</p>
</div>

<div class="exercise">
<h4>Exercise</h4>
<p>In <i>The Time Machine</i>, use regular expressions to find a sentence ending in a preposition.</p>
<h4><button onclick="toggle('a2');">Answer</button></h4>
<p id="a2" style="display: none;">You should have a regular expression like <code class="re">up\.</code>.</p>
</div>

<h3>Character classes</h3>
<p>A <i>character class</i> is a collection of characters in square brackets. This means, "find any one of these characters".</p>
<ul>
<li>The regular expression <code class="re">c[aeiou]t</code> means, "find a <code>c</code> followed by a vowel followed by a <code>t</code>". In a piece of text, this will find <code>cat</code>, <code>cet</code>, <code>cit</code>, <code>cot</code> and <code>cut</code>.</li>
<li>The regular expression <code class="re">[0123456789]</code> means "find a digit".</li>
<li>The regular expression <code class="re">[a]</code> means the same as <code class="re">a</code>: "find an <code>a</code>".</li>
<li>The regular expression <code class="re">[ ]</code> means "find a space".</li>
</ul>

<p>Some examples of escaping:</p>
<ul>
<li><code class="re">\[a\]</code> means "find a left square bracket followed by an <code>a</code> followed by a right square bracket".</li>
<li><code class="re">[\[\]ab]</code> means "find a left square bracket or a right square bracket or an <code>a</code> or a <code>b</code>".</li>
<li><code class="re">[\\\[\]]</code> means "find a backslash or a left square bracket or a right square bracket". (Urgh!)</li>
</ul>

<p>Order and duplication of characters are not important in character classes. <code class="re">[dabaaabcc]</code> is the same as <code class="re">[abcd]</code>.</p>

<div class="warning">
<h4>An important note</h4>
<p>The "rules" inside a character class are different from the rules outside of a character class. Some characters act as metacharacters inside a character class, but as literals outside of a character class. Some characters do the opposite. Some characters act as metacharacters in <em>both</em> situations, but do different things in each situation!</p>
<p>In particular, <code class="re">.</code> means "find any character", but <code class="re">[.]</code> means "find a full stop". Not the same thing!</p>
</div>

<div class="exercise">
<h4>Exercise</h4>
<p>In the dictionary, using only what you have learned so far, use regular expressions to find the word(s) with the most consecutive vowels and the word(s) with the most consecutive consonants.</p>
<h4><button onclick="toggle('a3');">Answers</button></h4>
<p id="a3" style="display: none;"><code class="re">[aeiou][aeiou][aeiou][aeiou][aeiou][aeiou]</code> finds the six-vowel words <code>euouae</code> and <code>euouaes</code>, whereas the horrific <code class="re">[bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz]</code> finds the magnificent 10-consonant <code>sulphhydryls</code>. We'll see how to simplify these horrors very shortly.</p>
</div>

<h3>Character class ranges</h3>
<p>Within a character class, you can express a <i>range</i> of letters or digits, using a hyphen:</p>
<ul>
<li><code class="re">[b-f]</code> is the same as <code class="re">[bcdef]</code> and means "find a <code>b</code> or a <code>c</code> or a <code>d</code> or an <code>e</code> or an <code>f</code>".</li>
<li><code class="re">[A-Z]</code> is the same as <code class="re">[ABCDEFGHIJKLMNOPQRSTUVWXYZ]</code> and means "find an upper-case letter".</li>
<li><code class="re">[1-9]</code> is the same as <code class="re">[123456789]</code> and means "find a non-zero digit".</li>
</ul>

<p>Outside of a character class, the hyphen has no special meaning. The regular expression <code class="re">a-z</code> means "find an <code>a</code> followed by a hyphen followed by a <code>z</code>".</p>
<p>Ranges and isolated characters may coexist in the same character class:</p>
                                                                            <ul>
                                                                            <li><code class="re">[0-9.,]</code> means "find a digit or a full stop or a comma".</li>
                                                                            <li><code class="re">[0-9a-fA-F]</code> means "find a hexadecimal digit".</li>
                                                                            <li><code class="re">[a-zA-Z0-9\-]</code> means "find an alphanumeric character or a hyphen".</li>
                                                                            </ul>

                                                                            <p>Although you can <em>try</em> using non-alphanumeric characters as endpoints in a range (e.g. <code class="re">abc[!-/]def</code>), this is not legal syntax in every implementation. Even where it is legal, it is not obvious to a reader exactly which characters are included in such a range. Use caution (by which I mean, don't do this).</p>
                                                                            <p>Equally, range endpoints should be chosen from the same range. Even if a regular expression like <code class="re">[A-z]</code> is legal in your implementation of choice, it may not do what you think. (Hint: there are characters between <code>Z</code> and <code>a</code>...)</p>

                                                                            <div class="warning">
                                                                            <p><strong>Caution.</strong> Ranges are ranges of characters, not ranges of numbers. The regular expression <code class="re">[1-31]</code> means "find a <code>1</code> or a <code>2</code> or a <code>3</code>", <em>not</em> "find an integer from <code>1</code> to <code>31</code>".</p>
                                                                            </div>

                                                                            <div class="exercise">
                                                                            <h4>Exercise</h4>
                                                                            <p>Using what you have learned so far, write a regular expression to find a date in YYYY-MM-DD format.</p>
                                                                            <h4><button onclick="toggle('a4');">Answer</button></h4>
                                                                            <p id="a4" style="display: none;">The best we can do right now is <code class="re">[0-9][0-9][0-9][0-9]-[0-9][0-9]-[0-9][0-9]</code>. Again, we'll be able to simplify this a lot very shortly.</p>
                                                                            </div>

                                                                            <h3>Character class negation</h3>
                                                                            <p>You may <i>negate</i> a character class by putting a caret at the very beginning.</p>
                                                                            <ul>
                                                                            <li><code class="re">[^a]</code> means "find any character other than an <code>a</code>".</li>
                                                                            <li><code class="re">[^a-zA-Z0-9]</code> means "find a non-alphanumeric character".</li>
                                                                            <li><code class="re">[\^abc]</code> means "find a caret or an <code>a</code> or a <code>b</code> or a <code>c</code>".</li>
                                                                            <li><code class="re">[^\^]</code> means "find any character other than a caret". (Ugh!)</li>
                                                                            </ul>

                                                                            <div class="exercise">
                                                                            <h4>Exercise</h4>
                                                                            <p>In the dictionary, use regular expressions to find counterexamples to the rule "<code>i</code> before <code>e</code> except after <code>c</code>".</p>
                                                                            <h4><button onclick="toggle('a5');">Answer</button></h4>
                                                                            <p id="a5" style="display: none;"><code class="re">cie</code> and <code class="re">[^c]ei</code> will both find numerous counterexamples e.g. <code>ancient</code>, <code>science</code>, <code>veil</code>, <code>weigh</code>.</p>
                                                                            </div>

                                                                            <h3>Freebie character classes</h3>
                                                                            <p>The regular expression <code class="re">\d</code> means the same as <code class="re">[0-9]</code>: "find a <b>d</b>igit". (To find a backslash followed by a <code>d</code>, use the regular expression <code class="re">\\d</code>.)</p>
                                                                            <p><code class="re">\w</code> means the same as <code class="re">[0-9A-Za-z_]</code>: "find a <b>w</b>ord character".</p>
                                                                            <p><code class="re">\s</code> means "find a <b>s</b>pace character (space, tab, carriage return or line feed)".</p>
                                                                            <p>Furthermore,</p>
                                                                            <ul>
                                                                            <li><code class="re">\D</code> means <code class="re">[^0-9]</code>: "find a non-digit".</li>
                                                                            <li><code class="re">\W</code> means <code class="re">[^0-9A-Za-z_]</code>: "find a non-word character".</li>
                                                                            <li><code class="re">\S</code> means "find a non-space character".</li>
                                                                            </ul>
                                                                            <p>These are extremely common and you must learn them.</p>
                                                                            <p>As you may have noticed, the full stop, <code class="re">.</code>, is essentially <strong>a character class containing every possible character</strong>.</p>
                                                                            <p>Many implementations provide numerous additional character classes, or flags which extend these existing classes to cover characters beyond plain ASCII. Hint: Unicode contains more "digit characters" than just <code>0</code> to <code>9</code>, and the same is true of "word" and "space" characters. Check your documentation.</p>

                                                                            <div class="exercise">
                                                                            <h4>Exercise</h4>
                                                                            <p>Simplify the regular expression <code class="re">[0-9][0-9][0-9][0-9]-[0-9][0-9]-[0-9][0-9]</code>.</p>
                                                                            <h4><button onclick="toggle('a6');">Answer</button></h4>
                                                                            <p id="a6" style="display: none;"><code class="re">\d\d\d\d-\d\d-\d\d</code>.</p>
                                                                            </div>

                                                                            <h3>Multipliers</h3>
                                                                            <p>You can use braces to put a <i>multiplier</i> after a literal or a character class.</p>
                                                                            <ul>
                                                                            <li>The regular expression <code class="re">a{1}</code> is the same as <code class="re">a</code> and means "find an <code>a</code>".</li>
                                                                            <li><code class="re">a{3}</code> means "find an <code>a</code> followed by an <code>a</code> followed by an <code>a</code>".</li>
                                                                            <li><code class="re">a{0}</code> means "find the empty string". By itself, this appears to be useless. If you use this regular expression on any piece of text, you will immediately get a match, right at the point where you started searching. This remains true even if your text is the empty string!</li>
                                                                            <li><code class="re">a\{2\}</code> means "find an <code>a</code> followed by a left brace followed by a <code>2</code> followed by a right brace".</li>
                                                                            <li>Braces have no special meaning inside character classes. <code class="re">[{}]</code> means "find a left brace or a right brace".</li>
                                                                            </ul>

                                                                            <div class="warning">
                                                                            <p><strong>Caution.</strong> Multipliers have no memory. The regular expression <code class="re">[abc]{2}</code> means "find <code>a</code> or <code>b</code> or <code>c</code>, followed by <code>a</code> or <code>b</code> or <code>c</code>". This is the same as "find <code>aa</code> or <code>ab</code> or <code>ac</code> or <code>ba</code> or <code>bb</code> or <code>bc</code> or <code>ca</code> or <code>cb</code> or <code>cc</code>". It does <em>not</em> mean "find <code>aa</code> or <code>bb</code> or <code>cc</code>"!</p>
                                                                            </div>

                                                                            <div class="exercise">
                                                                            <h4>Exercises</h4>
                                                                            <p>Simplify these regular expressions:</p>
                                                                            <ul>
                                                                            <li><code class="re">z.......z</code></li>
                                                                            <li><code class="re">\d\d\d\d-\d\d-\d\d</code></li>
                                                                            <li><code class="re">[aeiou][aeiou][aeiou][aeiou][aeiou][aeiou]</code></li>
                                                                            <li><code class="re">[bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz][bcdfghjklmnpqrstvwxyz]</code></li>
                                                                            </ul>
                                                                            <h4><button onclick="toggle('a6A');">Answers</button></h4>
                                                                            <ul id="a6A" style="display: none;">
                                                                            <li><code class="re">z.{7}z</code></li>
                                                                            <li><code class="re">\d{4}-\d{2}-\d{2}</code></li>
                                                                            <li><code class="re">[aeiou]{6}</code></li>
                                                                            <li><code class="re">[bcdfghjklmnpqrstvwxyz]{10}</code></li>
                                                                            </ul>
                                                                            </div>

                                                                            <h3>Multiplier ranges</h3>
                                                                            <p>Multipliers may have <i>ranges</i>:</p>
                                                                            <ul>
                                                                            <li><code class="re">x{4,4}</code> is the same as <code class="re">x{4}</code>.</li>
                                                                            <li><code class="re">colou{0,1}r</code> means "find <code>colour</code> or <code>color</code>".</li>
                                                                            <li><code class="re">a{3,5}</code> means "find <code>aaaaa</code> or <code>aaaa</code> or <code>aaa</code>".</li>
                                                                            </ul>
                                                                            <p>Note that the longer possibility is most preferred, because multipliers are <i>greedy</i>. If your input text is <code>I had an aaaaawful day</code> then this regular expression will find the <code>aaaaa</code> in <code>aaaaawful</code>. It won't just stop after three <code>a</code>s.</p>
                                                                            <p>The multiplier is greedy, but it won't disregard a perfectly good match. If your input text is <code>I had an aaawful daaaaay</code>, then this regular expression will find the <code>aaa</code> in <code>aaawful</code> on its first match. Only if you then say "find me another match" will it continue searching and find the <code>aaaaa</code> in <code>daaaaay</code>.</p>
                                                                            <p>Multiplier ranges may be <i>open-ended</i>:</p>
                                                                            <ul>
                                                                            <li><code class="re">a{1,}</code> means "find one or more <code>a</code>s in a row". Your multiplier will still be greedy, though. After finding the first <code>a</code>, it will try to find as many more <code>a</code>s as possible.</li>
                                                                            <li><code class="re">.{0,}</code> means "find anything". No matter what your input text is - even the empty string - this regular expression will successfully match the entire text and return it to you.</li>
                                                                            </ul>

                                                                            <div class="exercise">
                                                                            <h4>Exercises</h4>
                                                                            <p>Write a regular expression to find a double-quoted string. The string may have any number of characters.</p>
                                                                            <p>Modify your regular expression so that the double-quoted string that you find has no extra double-quotes between it. Again, use only what you've already learned.</p>
                                                                            <h4><button onclick="toggle('a7');">Answers</button></h4>
                                                                            <p id="a7" style="display: none;"><code class="re">".{0,}"</code>, then <code class="re">"[^"]{0,}"</code>.</p>
                                                                            </div>

                                                                            <h3>Freebie multipliers</h3>
                                                                            <p><code class="re">?</code> means the same as <code class="re">{0,1}</code>. For example, <code class="re">colou?r</code> means "find <code>colour</code> or <code>color</code>".</p>
                                                                            <p><code class="re">*</code> means the same as <code class="re">{0,}</code>. For example, <code class="re">.*</code> means "find anything", exactly as above.</p>
                                                                            <p><code class="re">+</code> means the same as <code class="re">{1,}</code>. For example, <code class="re">\w+</code> means "find a word". Here a "word" is a sequence of 1 or more "word characters", such as <code>_var</code> or <code>AccountName1</code>.</p>
                                                                            <p>These are extremely common and you must learn them. Also:</p>

                                                                            <ul>
                                                                            <li><code class="re">\?\*\+</code> means "find a question mark followed by an asterisk followed by a plus sign".</li>
                                                                            <li><code class="re">[?*+]</code> means "find a question mark or an asterisk or a plus sign".</li>
                                                                            </ul>

                                                                            <div class="exercise">
                                                                            <h4>Exercises</h4>
                                                                            <p>Simplify these regular expressions:</p>
                                                                            <ul>
                                                                            <li><code class="re">".{0,}"</code> and <code class="re">"[^"]{0,}"</code></li>
                                                                            <li><code class="re">x?x?x?</code></li>
                                                                            <li><code class="re">y*y*</code></li>
                                                                            <li><code class="re">z+z+z+z+</code></li>
                                                                            </ul>
                                                                            <h4><button onclick="toggle('a8');">Answers</button></h4>
                                                                            <ul id="a8" style="display: none;">
                                                                            <li><code class="re">".*"</code> and <code class="re">"[^"]*"</code></li>
                                                                            <li><code class="re">x{0,3}</code></li>
                                                                            <li><code class="re">y*</code></li>
                                                                            <li><code class="re">z{4,}</code></li>
                                                                            </ul>
                                                                            </div>
                                                                            <div class="exercise">
                                                                            <h4>Exercise</h4>
                                                                            <p>Write an expression to find two words separated by non-word characters. What about three words? What about six?</p>
                                                                            <h4><button onclick="toggle('a9');">Answers</button></h4>
                                                                            <p id="a9" style="display: none;"><code class="re">\w+\W+\w+</code>, <code class="re">\w+\W+\w+\W+\w+</code>, <code class="re">\w+\W+\w+\W+\w+\W+\w+\W+\w+\W+\w+</code>. Again, we'll see how to simplify the latter two very soon.</p>
                                                                            </div>

                                                                            <h3>Non-greed</h3>
                                                                            <p>The regular expression <code class="re">".*"</code> means "find a double quote, followed by as many characters as possible, followed by a double quote". Notice how the inner characters, caught by <code class="re">.*</code>, could easily be more double quotes. This is not usually very useful.</p>
                                                                            <p>Multipliers can be made <i>non-greedy</i> by appending a question mark. This reverses the order of preference:</p>
                                                                            <ul>
                                                                            <li><code class="re">\d{4,5}?</code> means "find <code class="re">\d\d\d\d</code> or <code class="re">\d\d\d\d\d</code>". This has exactly the same behaviour as <code class="re">\d{4}</code>.</li>
                                                                            <li><code class="re">colou??r</code> is <code class="re">colou{0,1}?r</code> which means "find <code>color</code> or <code>colour</code>". This has the same behaviour as <code class="re">colou?r</code>.</li>
                                                                            <li><code class="re">".*?"</code> means "find a double quote, followed by as <em>few</em> characters as possible, followed by a double quote". This, unlike the two examples above, is actually useful.</li>
                                                                            </ul>

                                                                            <h3>Alternation</h3>
                                                                            <p>You can match one of several <i>choices</i> using pipes:</p>
                                                                            <ul>
                                                                            <li><code class="re">cat|dog</code> means "find <code>cat</code> or <code>dog</code>".</li>
                                                                            <li><code class="re">red|blue|</code> and <code class="re">red||blue</code> and <code class="re">|red|blue</code> all mean "find <code>red</code> or <code>blue</code> or the empty string".</li>
                                                                            <li><code class="re">a|b|c</code> is the same as <code class="re">[abc]</code>.</li>
                                                                            <li><code class="re">cat|dog|\|</code> means "find <code>cat</code> or <code>dog</code> or a pipe".</li>
                                                                            <li><code class="re">[cat|dog]</code> means "find <code>a</code> or <code>c</code> or <code>d</code> or <code>g</code> or <code>o</code> or <code>t</code> or a pipe".</li>
                                                                            </ul>

                                                                            <div class="exercise">
                                                                            <h4>Exercises</h4>
                                                                            <p>Simplify these regular expressions as much as you can:</p>
                                                                            <ul>
                                                                            <li><code class="re">s|t|u|v|w</code></li>
                                                                            <li><code class="re">aa|ab|ba|bb</code></li>
                                                                            <li><code class="re">[abc]|[^abc]</code></li>
                                                                            <li><code class="re">[^ab]|[^bc]</code></li>
                                                                            <li><code class="re">[ab][ab][ab]?[ab]?</code></li>
                                                                            </ul>
                                                                            <h4><button onclick="toggle('a10');">Answers</button></h4>
                                                                            <ul id="a10" style="display: none;">
                                                                            <li><code class="re">[s-w]</code></li>
                                                                            <li><code class="re">[ab]{2}</code></li>
                                                                            <li><code class="re">.</code></li>
                                                                            <li><code class="re">[^b]</code></li>
                                                                            <li><code class="re">[ab]{2,4}</code></li>
                                                                            </ul>
                                                                            </div>
                                                                            <div class="exercise">
                                                                            <h4>Exercise</h4>
                                                                            <p>Write a regular expression to match an integer between 1 and 31 inclusive. Remember, <code class="re">[1-31]</code> is not the right answer.</p>
                                                                            <h4><button onclick="toggle('a11');">Answer</button></h4>
                                                                            <p id="a11" style="display: none;">There are several equivalent ways to do this. <code class="re">[1-9]|[12][0-9]|3[01]</code> is the most readable in my opinion.</p>
                                                                            </div>

                                                                            <h3>Grouping</h3>
                                                                            <p>You may <i>group</i> expressions using parentheses:</p>
                                                                            <ul>
                                                                            <li>To find a day of the week, use <code class="re">(Mon|Tues|Wednes|Thurs|Fri|Satur|Sun)day</code>.</li>
                                                                            <li><code class="re">(\w*)ility</code> is the same as <code class="re">\w*ility</code>. Both mean "find a word ending in <code>ility</code>". For why the first form might be useful, see later...</li>
                                                                            <li><code class="re">\(\)</code> means "find a left parenthesis followed by a right parenthesis".</li>
                                                                            <li><code class="re">[()]</code> means "find a left parenthesis or a right parenthesis".</li>
                                                                            </ul>

                                                                            <div class="exercise">
                                                                            <h4>Exercise</h4>
                                                                            <p>In <i>The Time Machine</i>, use regular expressions to find an expression wrapped in parentheses. Then, modify your answer so that the match has no parentheses within it.</p>
                                                                            <h4><button onclick="toggle('a12');">Answer</button></h4>
                                                                            <p id="a12" style="display: none;"><code class="re">\(.*\)</code>. Then, <code class="re">\([^()]*\)</code>.</p>
                                                                            </div>

                                                                            <p>Groups may contain the empty string:</p>
                                                                            <ul>
                                                                            <li><code class="re">(red|blue|)</code> means "find <code>red</code> or <code>blue</code> or the empty string".</li>
                                                                            <li><code class="re">abc()def</code> means the same as <code class="re">abcdef</code>.</li>
                                                                            </ul>
                                                                            <p>You may use multipliers on groups:</p>
                                                                            <ul>
                                                                            <li><code class="re">(red|blue)?</code> means the same as <code class="re">(red|blue|)</code>.</li>
                                                                            <li><code class="re">\w+(\s+\w+)*</code> means "find one or more words separated by whitespace".</li>
                                                                            </ul>

                                                                            <div class="exercise">
                                                                            <h4>Exercise</h4>
                                                                            <p>Simplify <code class="re">\w+\W+\w+\W+\w+</code> and <code class="re">\w+\W+\w+\W+\w+\W+\w+\W+\w+\W+\w+</code>.</p>
                                                                            <h4><button onclick="toggle('a13');">Answers</button></h4>
                                                                            <p id="a13" style="display: none;"><code class="re">\w+(\W+\w+){2}</code>, <code class="re">\w+(\W+\w+){5}</code>.</p>
                                                                            </div>

                                                                            <h3>Word boundaries</h3>
                                                                            <p>A <i>word boundary</i> is the place between a word character and a non-word character. Remember, a word character is <code class="re">\w</code> which is <code class="re">[0-9A-Za-z_]</code>, and a non-word character is <code class="re">\W</code> which is <code class="re">[^0-9A-Za-z_]</code>.</p>
                                                                            <p>The beginning and end of the text always count as word boundaries too.</p>
                                                                            <p>In the input text <code>it's a cat</code>, there are eight word boundaries. If we added a trailing space after <code>cat</code>, there would be nine word boundaries.</p>
                                                                            <ul>
                                                                            <li>The regular expression <code class="re">\b</code> means "find a word boundary".</li>
                                                                            <li><code class="re">\b\w\w\w\b</code> means "find a three-letter word".</li>
                                                                            <li><code class="re">a\ba</code> means "find <code>a</code>, followed by a word boundary, followed by <code>a</code>". This regular expression will never successfully find a match, no matter what the input text.</li>
                                                                            </ul>
                                                                            <p>Word boundaries are not characters. They have zero width. The following regular expressions are identical in behaviour:</p>
                                                                            <ul>
                                                                            <li><code class="re">(\bcat)\b</code></li>
                                                                            <li><code class="re">(\bcat\b)</code></li>
                                                                            <li><code class="re">\b(cat)\b</code></li>
                                                                            <li><code class="re">\b(cat\b)</code></li>
                                                                            </ul>

                                                                            <div class="exercise">
                                                                            <h4>Exercise</h4>
                                                                            <p>Find the longest word(s) in the dictionary.</p>
                                                                            <h4><button onclick="toggle('a14');">Answer</button></h4>
                                                                            <p id="a14" style="display: none;">After some trial and error, the regular expression <code class="re">\b.{45,}\b</code> finds only one match in the whole dictionary: <code>pneumonoultramicroscopicsilicovolcanoconiosis</code>.</p>
                                                                            </div>

                                                                            <h3>Line boundaries</h3>
                                                                            <p>Every piece of text breaks down into one or more <i>lines</i>, separated by <i>line breaks</i>, like this:</p>
                                                                            <ul>
                                                                            <li>Line</li>
                                                                            <li>Line break</li>
                                                                            <li>Line</li>
                                                                            <li>Line break</li>
                                                                            <li>...</li>
                                                                            <li>Line break</li>
                                                                            <li>Line</li>
                                                                            </ul>
                                                                            <p>Note how the text always ends with a line, never with a line break. However, any of the lines may contain zero characters, including the final line.</p>
                                                                            <p>A <i>start-of-line</i> is the place between a line break and the first character of the next line. As with word boundaries, the beginning of the text also counts as a start-of-line.</p>
                                                                            <p>An <i>end-of-line</i> is the place between the last character of a line and the line break. As with word boundaries, the end of the text also counts as an end-of-line.</p>
                                                                            <p>So our new breakdown is:</p>
                                                                            <ul>
                                                                            <li>Start-of-line, line, end-of-line</li>
                                                                            <li>Line break</li>
                                                                            <li>Start-of-line, line, end-of-line</li>
                                                                            <li>Line break</li>
                                                                            <li>...</li>
                                                                            <li>Line break</li>
                                                                            <li>Start-of-line, line, end-of-line</li>
                                                                            </ul>
                                                                            <p>Based on this:</p>
                                                                            <ul>
                                                                            <li>The regular expression <code class="re">^</code> means "find a start-of-line".</li>
                                                                            <li>The regular expression <code class="re">$</code> means "find an end-of-line".</li>
                                                                            <li><code class="re">^$</code> means "find an empty line".</li>
                                                                            <li><code class="re">^.*$</code> will find your entire text, because a line break is a character and <code class="re">.</code> will find it. To find a single line, use a non-greedy multiplier, <code class="re">^.*?$</code>.</li>
                                                                            <li><code class="re">\^\$</code> means "find a caret followed by a dollar sign".</li>
                                                                            <li><code class="re">[$]</code> means "find a dollar sign". <strong>However, <code class="re">[^]</code> is not a valid regular expression.</strong> Remember that the caret has a <em>different</em> special meaning inside square brackets! To put a caret in a character class, use <code class="re">[\^]</code>.</li>
                                                                            </ul>

                                                                            <p>Like word boundaries, line boundaries are not characters. They have zero width. The following regular expressions are identical in behaviour:</p>
                                                                            <ul>
                                                                            <li><code class="re">(^cat)$</code></li>
                                                                            <li><code class="re">(^cat$)</code></li>
                                                                            <li><code class="re">^(cat)$</code></li>
                                                                            <li><code class="re">^(cat$)</code></li>
                                                                            </ul>

                                                                            <div class="exercise">
                                                                            <h4>Exercise</h4>
                                                                            <p>Use regular expressions to find the longest line in <i>The Time Machine</i>.</p>
                                                                            <h4><button onclick="toggle('a15');">Answer</button></h4>
                                                                            <p id="a15" style="display: none;">Project Gutenberg's version of this book appears to have been wrapped at a maximum line length of 73 characters, as found using <code class="re">^.{73,}$</code>. Many lines are of this length.</p>
                                                                            </div>

                                                                            <h3>Text boundaries</h3>

                                                                            <p>Many implementations provide a flag which changes the meaning of <code class="re">^</code> and <code class="re">$</code> from "start-of-line" and "end-of-line" respectively to "start-of-text" and "end-of-text" respectively.</p>
                                                                            <p>Other implementations provide the separate metacharacters <code class="re">\A</code> and <code class="re">\z</code> for this purpose.</p>

                                                                            <h2>Capturing and replacing</h2>

                                                                            <p>This is where regular expressions start to get extremely powerful.</p>

                                                                            <h3>Capture groups</h3>

                                                                            <p>You already know that parentheses are used to denote groups. They are also used to capture substrings. If a regular expression is a very small computer program, the <i>capture groups</i> are (part of) its output.</p>
                                                                            <p>The regular expression <code class="re">(\w*)ility</code> means "find a word ending in <code>ility</code>". Capture group 1 is the part matched by <code class="re">\w*</code>. For example, if our text contains the word <code>accessibility</code>, capture group 1 is <code>accessib</code>. If our text just contains <code>ility</code> all by itself, capture group 1 is the empty string.</p>
                                                                            <p>You can have multiple capture groups, and they can even nest. Capture groups are numbered from left to right. Just count the left-parentheses.</p>
                                                                            <p>Suppose our regular expression is <code class="re">(\w+) had a ((\w+) \w+)</code>. If our input text is <code>I had a nice day</code>, then</p>
                                                                            <ul>
                                                                            <li>Capture group 1 is <code>I</code>.</li>
                                                                            <li>Capture group 2 is <code>nice day</code>.</li>
                                                                            <li>Capture group 3 is <code>nice</code>.</li>
                                                                            </ul>
                                                                            <p>In some implementations, you will also have access to capture group 0, which is the entire match: <code>I had a nice day</code>.</p>
                                                                            <p>In some implementations, if there are no capture groups, capture group 1 is automatically populated with the value of capture group 0.</p>

                                                                            <p>Yes, all of this does mean that parentheses are somewhat overloaded. Some implementations provide a separate syntax to declare a "non-capturing group", but the syntax isn't standardised and so won't be covered here.</p>

                                                                            <div class="warning">
                                                                            <p>The number of capture groups returned from a successful match is <em>always</em> equal to the number of capture groups in the original regular expression. Remember this, as it can help you with some confusing cases.</p>
                                                                            <p>The regular expression <code class="re">((cat)|dog)</code> means "find <code>cat</code> or <code>dog</code>". There are <strong>always two capture groups</strong>. If our input text is <code>dog</code>, then capture group 1 is <code>dog</code>, and capture group 2 is the empty string, because that choice was not used.</p>
                                                                            <p>The regular expression <code class="re">a(\w)*</code> means "find a word beginning with <code>a</code>". There is <strong>always one capture group</strong>:</p>
                                                                            <ul>
                                                                            <li>If the input text is <code>a</code>, capture group 1 is the empty string.</li>
                                                                            <li>If the input text is <code>ad</code>, capture group 1 is <code>d</code>.</li>
                                                                            <li>If the input text is <code>apricot</code>, capture group 1 is <code>t</code>. However, capture group 0 would be the entire word, <code>apricot</code>.</li>
                                                                            </ul>
                                                                            </div>

                                                                            <h3>Replacement</h3>

                                                                            <p>Once you've used a regular expression to find a string, you can specify another string to replace it with. The second string is the <i>replacement expression</i>. At first, this is exactly like</p>
                                                                            <ul>
                                                                            <li>a conventional Replace dialogue</li>
                                                                            <li>Java's <code>String.replace()</code> function</li>
                                                                            <li>PHP's <code>str_replace()</code> function</li>
                                                                            <li>etc.</li>
                                                                            </ul>

                                                                            <div class="exercise">
                                                                            <h4>Exercise</h4>
                                                                            <p>Replace all the vowels in <i>The Time Machine</i> with the letter <code>r</code>. Be sure to use the correct case!</p>
                                                                            <h4><button onclick="toggle('a16');">Answer</button></h4>
                                                                            <p id="a16" style="display: none;">Use regular expressions <code class="re">[aeiou]</code> and <code class="re">[AEIOU]</code>, with replacement expressions <code class="re">r</code> and <code class="re">R</code> respectively.</p>
                                                                            </div>

                                                                            <p>However, you can refer to capture groups in your replacement expression. This is the only special thing you can do in replacement expressions, and it's incredibly powerful because it means you don't have to completely destroy the thing you just found.</p>
                                                                            <p>Let's say you're trying to replace American-style dates (MM/DD/YY) with ISO 8601 dates (YYYY-MM-DD).</p>
                                                                            <ul>
                                                                            <li><p>Start with the regular expression <code class="re">(\d\d)/(\d\d)/(\d\d)</code>. Note that this has three capture groups: the month, the day and the two-digit year.</p></li>
                                                                            <li><p>Capture groups are referred to using a backslash and then the capture group number. So, your replacement expression is <code class="re">20\3-\1-\2</code>.</p></li>
                                                                            <li>
                                                                            <p>If our input text contains <code>03/04/05</code> (representing March 4, 2005), then</p>
                                                                            <ul>
                                                                            <li>Capture group 1 is <code>03</code>.</li>
                                                                            <li>Capture group 2 is <code>04</code>.</li>
                                                                            <li>Capture group 3 is <code>05</code>.</li>
                                                                            <li>The replacement string is <code>2005-03-04</code>.</li>
                                                                            </ul>
                                                                            </li>
                                                                            </ul>

                                                                            <p>You can refer to capture groups more than once in the replacement expression.</p>
                                                                            <ul>
                                                                            <li>To double up vowels, use the regular expression <code class="re">([aeiou])</code> and the replacement expression <code class="re">\1\1</code>.</li>
                                                                            </ul>

                                                                            <p>Backslashes must be escaped in the replacement expression. For example, let's say you have some text which you want to use in a string literal in a computer program. That means you need to put a backslash in front of every double quote or backslash in the original text.</p>
                                                                            <ul>
                                                                            <li><p>Your regular expression would be <code class="re">([\\"])</code>. Capture group 1 is the double quote or backslash.</p></li>
                                                                            <li><p>Your replacement expression would be <code class="re">\\\1</code>; a literal backslash followed by the captured double quote or backslash.</p></li>
                                                                            </ul>

                                                                            <div class="warning">
                                                                            <p>Instead of a backslash, some implementations use the dollar sign <code class="re">$</code> to indicate capture groups.</p>
                                                                            </div>

                                                                            <div class="exercise">
                                                                            <h4>Exercise</h4>
                                                                            <p>Write a regular expression and replacement expression which could take timestamps such as <code>23h59</code> and turn them into <code>23:59</code>.</p>
                                                                            <h4><button onclick="toggle('a16A');">Answer</button></h4>
                                                                            <p id="a16A" style="display: none;">The regular expression <code class="re">(\d\d)h(\d\d)</code> finds the timestamps, <code class="re">\1:\2</code> will replace them.</p>
                                                                            </div>

                                                                            <h3>Back-references</h3>
                                                                            <p>You can also refer to a captured group later in the same regular expression. This is called a <i>back-reference</i>.</p>
                                                                            <p>For example, recall that the regular expression <code class="re">[abc]{2}</code> means "find <code>aa</code> or <code>ab</code> or <code>ac</code> or <code>ba</code> or <code>bb</code> or <code>bc</code> or <code>ca</code> or <code>cb</code> or <code>cc</code>". But the regular expression <code class="re">([abc])\1</code> means "find <code>aa</code> or <code>bb</code> or <code>cc</code>".</p>

                                                                            <div class="exercise">
                                                                            <h4>Exercise</h4>
                                                                            <p>In the dictionary, find the longest word which consists of the same string repeated twice (e.g. <code>papa</code>, <code>coco</code>).</p>
                                                                            <h4><button onclick="toggle('a17');">Answer</button></h4>
                                                                            <p id="a17" style="display: none;"><code class="re">\b(.{6,})\1\b</code> found <code>chiquichiqui</code>. If we don't care about full words, we can lop off the word boundary assertions, yielding <code class="re">(.{7,})\1</code> which finds <code>countercountermeasure</code> and <code>countercountermeasures</code>.</p>
                                                                            </div>

                                                                            <h2>Programming with regular expressions</h2>

                                                                            <p>Some notes specific to this task:</p>

                                                                            <h3>Excessive backslash syndrome</h3>

                                                                            <p>In some programming languages, such as Java, there is no special support for strings containing regular expressions. Strings have their own escaping rules, which are added on top of the escaping rules for regular expressions, commonly resulting in backslash overload. For example (still Java):</p>
                                                                                                                                                                                                                                                                                                                                                                                      <ul>
                                                                                                                                                                                                                                                                                                                                                                                      <li>To find a digit, the regular expression <code class="re">\d</code> becomes <code>String re = "\\d";</code> in source code.</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>To find a double-quoted string, <code class="re">"[^"]*"</code> becomes <code>String re = "\"[^\"]*\"";</code>.</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>To find a backslash or a left square bracket or a right square bracket, the regular expression <code class="re">[\\\[\]]</code> becomes <code>String re = "[\\\\\\[\\]]";</code>.</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li><code>String re = "\\s";</code> and <code>String re = "[ \t\r\n]";</code> are equivalent. Note the different "levels" of escaping.</li>
                                                                                                                                                                                                                                                                                                                                                                                      </ul>
                                                                                                                                                                                                                                                                                                                                                                                      <p>In other programming languages, regular expressions are marked out by a special delimiter, typically the forward slash <code>/</code>. Here's some JavaScript:</p>
                                                                                                                                                                                                                                                                                                                                                                                      <ul>
                                                                                                                                                                                                                                                                                                                                                                                      <li>To find a digit, <code class="re">\d</code> simply becomes <code>var regExp = /\d/;</code>.</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>To find a backslash or a left square bracket or a right square bracket, <code>var regExp = /[\\\[\]]/;</code>.</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li><code>var regExp = /\s/;</code> and <code>var regExp = /[ \t\r\n]/;</code> are equivalent.</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>Of course, this means forward slashes must be escaped instead of double quotes. To find the first part of a URL: <code>var regExp = /https?:\/\//;</code>.</li>
                                                                                                                                                                                                                                                                                                                                                                                      </ul>
                                                                                                                                                                                                                                                                                                                                                                                      <p>I hope you see why I've been trying to inoculate you against backslashes up to this point.</p>

                                                                                                                                                                                                                                                                                                                                                                                      <h3>Dynamic regular expressions</h3>
                                                                                                                                                                                                                                                                                                                                                                                      <p>Be cautious when constructing a regular expression string dynamically. If the string that you use is not fixed, then it could contain unexpected metacharacters. This could make for a syntax error. Worse, it could result in a syntactically correct regular expression, but one with unexpected behaviour.</p>
                                                                                                                                                                                                                                                                                                                                                                                      <p>Buggy Java code:</p>
                                                                                                                                                                                                                                                                                                                                                                                      <pre>
                                                                                                                                                                                                                                                                                                                                                                                      String sep = System.getProperty("file.separator");
                                                                                                                                                                                                                                                                                                                                                                                      String[] directories = filePath.split(sep);
                                                                                                                                                                                                                                                                                                                                                                                      </pre>
                                                                                                                                                                                                                                                                                                                                                                                      <p>The bug: <code>String.split()</code> expects <code>sep</code> to be a regular expression. But on Windows, <code>sep</code> is a string consisting of a single backslash, <code>"\\"</code>. This is not a syntactically correct regular expression! The result: a <code>PatternSyntaxException</code>.</p>
                                                                                                                                                                                                                                                                                                                                                                                      <p>Any good programming language provides a mechanism to escape all the metacharacters in a string. In Java, you would do this:</p>
                                                                                                                                                                                                                                                                                                                                                                                      <pre>
                                                                                                                                                                                                                                                                                                                                                                                      String sep = System.getProperty("file.separator");
                                                                                                                                                                                                                                                                                                                                                                                      String[] directories = filePath.split(<em>Pattern.quote(</em>sep<em>)</em>);
                                                                                                                                                                                                                                                                                                                                                                                      </pre>

                                                                                                                                                                                                                                                                                                                                                                                      <h3>Regular expressions in loops</h3>
                                                                                                                                                                                                                                                                                                                                                                                      <p>Compiling a regular expression string into a working "program" is a relatively expensive operation. You may find performance improves if you can avoid doing this inside a loop.</p>

                                                                                                                                                                                                                                                                                                                                                                                      <h2>Miscellaneous advice</h2>

                                                                                                                                                                                                                                                                                                                                                                                      <h3>Input validation</h3>
                                                                                                                                                                                                                                                                                                                                                                                      <p>Regular expressions can be used to validate user input. But excessively strict validation can make users' lives very difficult. Examples follow:</p>

                                                                                                                                                                                                                                                                                                                                                                                      <h4>Payment card numbers</h4>
                                                                                                                                                                                                                                                                                                                                                                                      <p>On a website, I entered my card number as <code>1234 5678 8765 4321</code>. The site rejected it. It was validating the field using <code class="re">\d{16}</code>.</p>
                                                                                                                                                                                                                                                                                                                                                                                      <p>The regular expression should allow for spaces. And hyphens.</p>
                                                                                                                                                                                                                                                                                                                                                                                      <p>In fact, why not just strip out all non-digit characters first and <em>then</em> perform the validation? To do this, use the regular expression <code class="re">\D</code>, and an empty string for the replacement expression.</p>

                                                                                                                                                                                                                                                                                                                                                                                      <div class="exercise">
                                                                                                                                                                                                                                                                                                                                                                                      <h4>Exercise</h4>
                                                                                                                                                                                                                                                                                                                                                                                      <p>Write a regular expression which could validate my card number without stripping out non-digit characters first.</p>
                                                                                                                                                                                                                                                                                                                                                                                      <h4><button onclick="toggle('a18');">Answer</button></h4>
                                                                                                                                                                                                                                                                                                                                                                                      <p id="a18" style="display: none;"><code class="re">\A\D*(\d\D*){16}\z</code> is one of several variations which would accomplish this.</p>
                                                                                                                                                                                                                                                                                                                                                                                      </div>

                                                                                                                                                                                                                                                                                                                                                                                      <h4>Names</h4>
                                                                                                                                                                                                                                                                                                                                                                                      <p>Do not use regular expressions to validate people's names. In fact, do not validate names at all if you can possibly help it.</p>
                                                                                                                                                                                                                                                                                                                                                                                      <p><a href="http://www.kalzumeus.com/2010/06/17/falsehoods-programmers-believe-about-names/">Falsehoods programmers believe about names</a>:</p>
                                                                                                                                                                                                                                                                                                                                                                                      <ul>
                                                                                                                                                                                                                                                                                                                                                                                      <li>Names do not contain spaces.</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>Names do not contain punctuation.</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>Names use only ASCII characters.</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>Names are restricted to <em>any</em> particular character set.</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>Names are always at least <var>M</var> characters long.</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>Names are never more than <var>N</var> characters long.</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>People always have exactly one forename.</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>People always have exactly one middle name.</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>People always have exactly one surname.</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>...</li>
                                                                                                                                                                                                                                                                                                                                                                                      </ul>

                                                                                                                                                                                                                                                                                                                                                                                      <h4>Email addresses</h4>
                                                                                                                                                                                                                                                                                                                                                                                      <p>Do not use regular expressions to validate email addresses.</p>
                                                                                                                                                                                                                                                                                                                                                                                      <p>Firstly, this is extremely difficult to do correctly. Email addresses do indeed conform to a regular expression, but the expression is <a href="http://www.ex-parrot.com/pdw/Mail-RFC822-Address.html">apocalyptically long and complicated</a>. Anything shorter is likely to yield false negatives. (Did you know? Email addresses can contain comments!)</p>
                                                                                                                                                                                                                                                                                                                                                                                      <p>Secondly, even if the supplied email address conforms to the regular expression, this doesn't prove it exists. <strong>The only way to validate an email address is to send an email to it.</strong></p>

                                                                                                                                                                                                                                                                                                                                                                                      <h3>Markup</h3>
                                                                                                                                                                                                                                                                                                                                                                                      <p>Do not use regular expressions to parse HTML or XML for serious applications. Parsing HTML/XML is</p>
                                                                                                                                                                                                                                                                                                                                                                                      <ol>
                                                                                                                                                                                                                                                                                                                                                                                      <li>Impossible using simple regular expressions</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>Incredibly difficult even in general</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>A solved problem.</li>
                                                                                                                                                                                                                                                                                                                                                                                      </ol>
                                                                                                                                                                                                                                                                                                                                                                                      <p>Find an existing parsing library which can do this for you.</p>

                                                                                                                                                                                                                                                                                                                                                                                      <h2>And that's 55 minutes</h2>

                                                                                                                                                                                                                                                                                                                                                                                      <p>In summary:</p>
                                                                                                                                                                                                                                                                                                                                                                                      <ul>
                                                                                                                                                                                                                                                                                                                                                                                      <li><p>Literals: <code class="re">a</code> <code class="re">b</code> <code class="re">c</code> <code class="re">d</code> <code class="re">1</code> <code class="re">2</code> <code class="re">3</code> <code class="re">4</code> etc.</p></li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>
                                                                                                                                                                                                                                                                                                                                                                                      <p>Character classes: <code class="re">.</code> <code class="re">[abc]</code> <code class="re">[a-z]</code> <code class="re">\d</code> <code class="re">\w</code> <code class="re">\s</code></p>
                                                                                                                                                                                                                                                                                                                                                                                      <ul>
                                                                                                                                                                                                                                                                                                                                                                                      <li><code class="re">.</code> means "any character"</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li><code class="re">\d</code> means "a digit"</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li><code class="re">\w</code> means "a word character", <code class="re">[0-9A-Za-z_]</code></li>
                                                                                                                                                                                                                                                                                                                                                                                      <li><code class="re">\s</code> means "a space, tab, carriage return or line feed character"</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>Negated character classes: <code class="re">[^abc]</code> <code class="re">\D</code> <code class="re">\W</code> <code class="re">\S</code></li>
                                                                                                                                                                                                                                                                                                                                                                                      </ul>
                                                                                                                                                                                                                                                                                                                                                                                      </li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>
                                                                                                                                                                                                                                                                                                                                                                                      <p>Multipliers: <code class="re">{4}</code> <code class="re">{3,16}</code> <code class="re">{1,}</code> <code class="re">?</code> <code class="re">*</code> <code class="re">+</code></p>
                                                                                                                                                                                                                                                                                                                                                                                      <ul>
                                                                                                                                                                                                                                                                                                                                                                                      <li><code class="re">?</code> means "zero or one"</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li><code class="re">*</code> means "zero or more"</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li><code class="re">+</code> means "one or more"</li>
                                                                                                                                                                                                                                                                                                                                                                                      <li>Multipliers are greedy unless you put a <code class="re">?</code> afterwards</li>
                                                                                                                                                                                                                                                                                                                                                                                      </ul>
                                                                                                                                                                                                                                                                                                                                                                                      </li>
                                                                                                                                                                                                                                                                                                                                                                                      <li><p>Alternation and grouping: <code class="re">(Septem|Octo|Novem|Decem)ber</code></p></li>
                                                                                                                                                                                                                                                                                                                                                                                      <li><p>Word, line and text boundaries: <code class="re">\b</code> <code class="re">^</code> <code class="re">$</code> <code class="re">\A</code> <code class="re">\z</code></p></li>
                                                                                                                                                                                                                                                                                                                                                                                      <li><p>To refer back to a capture groups: <code class="re">\1</code> <code class="re">\2</code> <code class="re">\3</code> etc. (works in both replacement expressions and matching expressions)</p></li>
                                                                                                                                                                                                                                                                                                                                                                                      </ul>
                                                                                                                                                                                                                                                                                                                                                                                      <ul>
                                                                                                                                                                                                                                                                                                                                                                                      <li><p>List of metacharacters: <code class="re">.</code> <code class="re">\</code> <code class="re">[</code> <code class="re">]</code> <code class="re">{</code> <code class="re">}</code> <code class="re">?</code> <code class="re">*</code> <code class="re">+</code> <code class="re">|</code> <code class="re">(</code> <code class="re">)</code> <code class="re">^</code> <code class="re">$</code></p></li>
                                                                                                                                                                                                                                                                                                                                                                                      <li><p>List of metacharacters when inside a character class: <code class="re">[</code> <code class="re">]</code> <code class="re">\</code> <code class="re">-</code> <code class="re">^</code></p></li>
                                                                                                                                                                                                                                                                                                                                                                                                                                                    <li><p>You can always escape a metacharacter using a backslash: <code class="re">\</code></p></li>
                                                                                                                                                                                                                                                                                                                                                                                                                                                    </ul>

                                                                                                                                                                                                                                                                                                                                                                                                                                                    <h3>Thanks for reading</h3>
                                                                                                                                                                                                                                                                                                                                                                                                                                                    <p>Regular expressions are ubiquitous and incredibly useful. Everybody who spends any amount of time editing text or writing computer programs should know how to use them.</p>
                                                                                                                                                                                                                                                                                                                                                                                                                                                    <p>So far today we have only scratched the surface...</p>

                                                                                                                                                                                                                                                                                                                                                                                                                                                    <div class="exercise">
                                                                                                                                                                                                                                                                                                                                                                                                                                                    <h4>Exercise</h4>
                                                                                                                                                                                                                                                                                                                                                                                                                                                    <p>Go and read the documentation for your regular expression implementation of choice. I guarantee that there are more features there than we've covered here.</p>
                                                                                                                                                                                                                                                                                                                                                                                                                                                    </div>

                                                                                                                                                                                                                                                                                                                                                                                                                                                    <h4><a href="https://qntm.org/re">Back to Things Of Interest</a></h4>

                                                                                                                                                                                                                                                                                                                                                                                                                                                    <script type="text/javascript"><!--
                                                                                                                                                                                                                                                                                                                                                                                                                                                    var sc_project=667681; 
                                                                                                                                                                                                                                                                                                                                                                                                                                                    var sc_invisible=1; 
                                                                                                                                                                                                                                                                                                                                                                                                                                                    var sc_partition=5; 
                                                                                                                                                                                                                                                                                                                                                                                                                                                    var sc_security="f56850e2"; 
                                                                                                                                                                                                                                                                                                                                                                                                                                                    var sc_remove_link=1; 
                                                                                                                                                                                                                                                                                                                                                                                                                                                    // --></script>
                                                                                                                                                                                                                                                                                                                                                                                                                                                    <script type="text/javascript" src="https://statcounter.com/counter/counter_xhtml.js"></script>

                                                                                                                                                                                                                                                                                                                                                                                                                                                    <noscript>
                                                                                                                                                                                                                                                                                                                                                                                                                                                    <p><img
                                                                                                                                                                                                                                                                                                                                                                                                                                                    class="statcounter"
                                                                                                                                                                                                                                                                                                                                                                                                                                                    src="https://c6.statcounter.com/counter.php?sc_project=667681&amp;java=0&amp;security=f56850e2&amp;invisible=1"
                                                                                                                                                                                                                                                                                                                                                                                                                                                    alt="website statistics"
                                                                                                                                                                                                                                                                                                                                                                                                                                                    /></p>
                                                                                                                                                                                                                                                                                                                                                                                                                                                    </noscript>
                                                                                                                                                                                                                                                                                                                                                                                                                                                    </body>
                                                                                                                                                                                                                                                                                                                                                                                                                                                    </html>
