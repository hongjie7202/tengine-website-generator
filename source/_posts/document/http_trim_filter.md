---
title: "Ngx_http_trim_filter"
date: "2016-12-02 03:37:32"
---


The ngx_http_trim_filter module is a filter that modifies a response by removing unnecessary whitespaces
(spaces, tabs, newlines) and comments from HTML (including inline javascript and css). Trim module parses
HTML with a state machine.

```
location / {
trim on;
trim_js on;
trim_css on;
}
```

**trim** `on` | `off`

**Default:** `trim off`

**Context:** `http, server, location`

Enable or disable trim module for pure HTML.

This module will retain some contents unchanged, in case that they are enclosed by the tag `pre`,`textarea`,`script` and `style`,as well as IE/SSI/ESI comments.

Parameter value can contain variables.

Example:

```
set $flag "off";
if ($condition) {
set $flag "on";
}
trim $flag;
```

**trim_js** `on` | `off`

**Default:** `trim_js off`

**Context:** `http, server, location`

Enable or disable trim module for inline javascript.

Parameter value can contain variables too.

**trim_css** `on` | `off`

**Default:** `trim_css off`

**Context:** `http, server, location`

Enable or disable trim module for inline css.

Parameter value can contain variables too.

**trim_types** `MIME types`

**Default:** `trim_types: text/html`

**Context:** `http, server, location`

Enable trim module for the specified MIME types in addition to "text/html". Responses with the “text/html” type are always processed.

Trim module will be disabled if incoming request has `http_trim=off` parameter in url.

e.g.  `http://www.xxx.com/index.html?http_trim=off`

original:

```
&lt;!DOCTYPE html&gt;
&lt;textarea  &gt;
trim
module
&lt;/textarea  &gt;
&lt;!--remove all--&gt;
&lt;!--[if IE]&gt; trim module &lt;![endif]--&gt;
&lt;!--[if !IE ]&gt;--&gt; trim module  &lt;!--&lt;![endif]--&gt;
&lt;!--# ssi--&gt;
&lt;!--esi--&gt;
&lt;pre    style  =
"color:   blue"  &gt;Welcome    to    nginx!&lt;/pre  &gt;
&lt;script type="text/javascript"&gt;
/***  muitl comment
***/
//// single comment
str.replace(/     /,"hello");
&lt;/script&gt;
&lt;style   type="text/css"  &gt;
/*** css comment
! ***/
body
{
font-size:  20px ;
line-height: 150% ;
}
&lt;/style&gt;
```

result:

```
&lt;!DOCTYPE html&gt;
&lt;textarea&gt;
trim
module
&lt;/textarea&gt;
&lt;!--[if IE]&gt; trim module &lt;![endif]--&gt;
&lt;!--[if !IE ]&gt;--&gt; trim module  &lt;!--&lt;![endif]--&gt;
&lt;!--# ssi--&gt;
&lt;!--esi--&gt;
&lt;pre style="color:   blue"&gt;Welcome    to    nginx!&lt;/pre&gt;
&lt;script type="text/javascript"&gt;str.replace(/     /,"hello");&lt;/script&gt;
&lt;style type="text/css"&gt;body{font-size:20px;line-height:150%;}&lt;/style&gt;

### Html

##### Whitespace

*   Remove '\r'.
*   Replace '\t' with space.
*   Replace multiple spaces with a single space.
*   Replace multiple '\n' with a single '\n'.
*   Replace multiple '\n' and '\t' in tag with a single space.
*   Do not trim quoted strings in tag.
*   Do not trim the contents enclosed by the tag `pre`,`textarea`,`script` and `style`.

##### Comment

*   Remove html comment(`&lt;!-- --&gt;`).
*   Do not trim IE/SSI/ESI comments.
IE comment: `&lt;!--[if  &lt;![endif]--&gt;`
SSI comment: `&lt;!--#  --&gt;`
ESI comment: `&lt;!--esi  --&gt;`

### Javascript

Contents enclosed by `&lt;script type="text/javascript"&gt;` or `&lt;script&gt;` will be identified as javascript.

##### Whitespace

*   Remove '\r'.
*   Remove '\t','\n' and space that behind '(',',','=',':','[','!','&amp;','|','?',';','>','~','*','{'.
*   Replace multiple spaces with a single space.
*   Do not trim quoted strings and regular expression literals.

##### Comment

*   Remove single comment. `//`
*   Remove multi comment. `/*  */`

### Css

Contents enclosed by `&lt;style type="text/css"&gt;` or `&lt;style&gt;` will be identified as css.

##### Whiltespace

*   Remove '\r'.
*   Remove '\t','\n' and space that around ';','>','{','}',':',','.
*   Replace multiple '\n' and spaces with a single space.
*   Do not trim quoted strings.

##### Comment

*   Remove css comment(`/* */`).
*   Do not remove child seletor and IE5 /Mac hack comments.
Child seletor hack: `html&gt;/**/body p{color:blue}`
IE5 /Mac hack: `/*\*/.selector{color:khaki}/**/`
```