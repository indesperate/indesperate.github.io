# vim中的正则表达式

## 1.regexp

regexp即regular expression，常用来匹配某种pattern。  

正则有一系列规则，这里只记载一些转义字符的意义

```
\w means word
\W means the opposite
\a means ABCabc...
\l means abc...
\u means ABC...
\t means tab
\b means word's boundary in vim use \< \>
\s means space
\d means number
+  means 1 or more
.  means 0 or more
?  means 0 or 1
^  means start
$  means end

when use ungreed search use ?
.*?
.??  .? matched 0 or 1 , .?? will match 0 because of ungreed search
.{m,n}  this will only match m times
```

## 2.vim中的regexp

由于正则表达式用到了很多特殊字符，而在搜索这些特殊字符时：

比如：("Hello")  

这里的括号必须加上'\\'，为\\\(\\"Hello\\"\\\) 而这样的例子在代码中非常常见，  
所以vim使用了四种模式，并通过在匹配字符串之前加上'\\'+模式代号实现

1. magic (\m)
2. very magic (\v)
3. nomagic (\M)
4. very nomagic (\V)

```
:g/\v^$/d                  #删除所有空行  
```

ps:通过set magic等命令可以修改默认使用哪一种匹配方式，一般为magic  

magic其实可以理解为魔法字符，即正则字符，  
默认情况下magic使用一些常见字符不需要加'\\'，比如^$.*等  
比如匹配任意括号：

```
/(.*)                      #因为set magic所以\v不需要
```

而nomagic模式下，使用正则必须在正则字符前加'\\'

```
/\m(\.\*)
```

在very magic下，即完整的正则语法

```
/\v\(.*\)                  #此时不加'\'，会导致.*被认为是一个单元,无法匹配括号
```

在very no magic下，即直接输入你想要的字符即可，如果要使用正则，所有正则符号加'\\'

下面是一个将路径中的'\'换成'\\'的vim命令

```
:%s/\>\\\(\w\)/\\\\\1/g    #magic
:%s/\v>\\(\w)/\\\\\1/g     #very magic
```

此处%代表全文，s道标substitute，接着是替代字符串  
\v为very magic，>为单右边界，\\\\代表'\',括号为单元，\w为单词，  
之后为替代字符串,俩个'\'和前面括号里的元素，这里为'\1'
g为global

## 3.vim中的非贪婪匹配

前面可以看到，有些正则规则通过 ? 实现非贪婪匹配,但是在vim中并不可以

ps：贪婪匹配为尽可能多匹配，vim默认为贪婪匹配,比如 ".*"会匹配到"Hel"lo"这一整个字符串

你需要使用{-}来匹配，比如：

```
/\m\(L".\{-}"\)             #magic mode
/\v(L".{-})"                #very magic mode
```

用来匹配L"Hel"，而lo"则不会被匹配

## 4.vim中的g command

顺带正则用的非常多的g command

```
:g/pattern/z#.5          #display 5 lines around pattern
:g/pattern/t$            #paste lines to the end of the file
:g/pattern/m$            #move lines
:g/pattern/y [register]  #move to the register
:g/pattern/s/$/mytxt     #add txt to the end of lines
```