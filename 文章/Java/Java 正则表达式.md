# Java 相关类 
java.util.regex 包主要包括以下三个类：
* Pattern 类：
    
    pattern 对象是一个正则表达式的编译表示。也就是说在这个类中设置正则表达式。

* Matcher 类：

    Matcher 对象是对输入字符串进行解释和匹配操作的引擎。也就是说在这个类中设置要匹配的字符串，并获得匹配的结果。
    
* PatternSyntaxException：

    PatternSyntaxException 是一个非强制异常类，它表示一个正则表达式模式中的语法错误。
    
示例：

```
// 按指定模式在字符串查找
      String line = "This order was placed for QT3000! OK?";
      String pattern = "(\\D*)(\\d+)(.*)";
 
      // 创建 Pattern 对象
      Pattern r = Pattern.compile(pattern);
 
      // 现在创建 matcher 对象
      Matcher m = r.matcher(line);
      if (m.find( )) {
         System.out.println("Found value: " + m.group(0) );
         System.out.println("Found value: " + m.group(1) );
         System.out.println("Found value: " + m.group(2) );
         System.out.println("Found value: " + m.group(3) ); 
      } else {
         System.out.println("NO MATCH");
      }
```
## Pattern

Pattern, 是Java正则表达式API中的主要入口，无论何时,需要使用正则表达式,从Pattern 类开始。

### Pattern.compile()

Pattern 没有公共构造方法，使用此静态方法返回 Pattern 对象。flags 参数可以控制 Pattern 匹配模式的方式。

### Pattern.matches()

Pattern.matches(String regex, CharSequence input) 此静态方法可以直接匹配字符，返回是否匹配字符是否匹配此表达式

```
String text    =
        "This is the text to be searched " +
        "for occurrences of the pattern.";
String pattern = ".*is.*";
boolean matches = Pattern.matches(pattern, text);
System.out.println("matches = " + matches);
```
### Pattern.split()

Pattern 类的 split()方法，可以用正则表达式作为分隔符，把文本分割为String类型的数组。

```
String text = "A sep Text sep With sep Many sep Separators";
String patternString = "sep";
Pattern pattern = Pattern.compile(patternString);
String[] split = pattern.split(text);
System.out.println("split.length = " + split.length);
for(String element : split){
    System.out.println("element = " + element);
}
```
上例中把text 文本分割为一个包含5个字符串的数组。

### Pattern.matcher()

用于获得 Matcher 对象，在此时传入要匹配的文本
    
## Matcher 操作

Matcher 类用于匹配一段文本中多次出现一个正则表达式，Matcher 也适用于多文本中匹配同一个正则表达式。

### matches 和 lookingAt 方法

matches 和 lookingAt 方法都用来尝试匹配一个输入序列模式。它们的不同是 matches 要求整个序列都匹配，而lookingAt 不要求。
lookingAt 方法虽然不需要整句都匹配，但是需要从第一个字符开始匹配。

这两个方法经常在输入字符串的开始使用。


```
import java.util.regex.Matcher;
import java.util.regex.Pattern;
 
public class RegexMatches
{
    private static final String REGEX = "foo";
    private static final String INPUT = "fooooooooooooooooo";
    private static final String INPUT2 = "ooooofoooooooooooo";
    private static Pattern pattern;
    private static Matcher matcher;
    private static Matcher matcher2;
 
    public static void main( String args[] ){
       pattern = Pattern.compile(REGEX);
       matcher = pattern.matcher(INPUT);
       matcher2 = pattern.matcher(INPUT2);
 
       System.out.println("Current REGEX is: "+REGEX);
       System.out.println("Current INPUT is: "+INPUT);
       System.out.println("Current INPUT2 is: "+INPUT2);
 
 
       System.out.println("lookingAt(): "+matcher.lookingAt());
       System.out.println("matches(): "+matcher.matches());
       System.out.println("lookingAt(): "+matcher2.lookingAt());
   }
}
```
运行结果

```
Current REGEX is: foo
Current INPUT is: fooooooooooooooooo
Current INPUT2 is: ooooofoooooooooooo
lookingAt(): true
matches(): false
lookingAt(): false
```



### replaceFirst 和 replaceAll 方法

replaceFirst 和 replaceAll 方法用来替换匹配正则表达式的文本。不同的是，replaceFirst 替换首次匹配，replaceAll 替换所有匹配。


```
import java.util.regex.Matcher;
import java.util.regex.Pattern;
 
public class RegexMatches
{
    private static String REGEX = "dog";
    private static String INPUT = "The dog says meow. " +
                                    "All dogs say meow.";
    private static String REPLACE = "cat";
 
    public static void main(String[] args) {
       Pattern p = Pattern.compile(REGEX);
       // get a matcher object
       Matcher m = p.matcher(INPUT); 
       INPUT = m.replaceAll(REPLACE);
       System.out.println(INPUT);
   }
}
```

### find 、start() 和 end()
find() 方法用于在文本中查找出现的正则表达式。如果在文本中多次匹配，find() 方法返回第一个，之后每次调用 find() 都会返回下一个。

start() 和 end() 返回每次匹配的字串在整个文本中的开始和结束位置。实际上, end() 返回的是字符串末尾的后一位，这样，可以在把 start() 和 end() 的返回值直接用在String.substring() 里。

看代码

```
    private static final String REGEX = "\\bcat\\b";
    private static final String INPUT =
                                    "cat cat cat cattie cat";
 
    public static void main( String args[] ){
       Pattern p = Pattern.compile(REGEX);
       Matcher m = p.matcher(INPUT); // 获取 matcher 对象
       int count = 0;
 
       while(m.find()) {
         count++;
         System.out.println("Match number "+count);
         System.out.println("start(): "+m.start());
         System.out.println("end(): "+m.end());
      }
   }
```
运行结果

```
Match number 1
start(): 0
end(): 3
Match number 2
start(): 4
end(): 7
Match number 3
start(): 8
end(): 11
Match number 4
start(): 19
end(): 22
```

### group 

Match 中的 group 方法是用来查找匹配到的字符串的。

**捕获组**是把多个字符当一个单独单元进行处理的方法，它通过对括号内的字符分组来创建。

例如，正则表达式(dog) 创建了单一分组，组里包含”d”，”o”，和”g”。

捕获组是通过从左至右计算其开括号来编号。例如，在表达式（（A）（B（C））），有四个这样的组：


```
((A)(B(C)))
(A)
(B(C))
(C)
```

可以通过调用matcher对象的groupCount方法来查看表达式有多少个分组。groupCount方法返回一个int值，表示matcher对象当前有多个捕获组。

还有一个特殊的组（组0），它总是代表整个表达式。该组不包括在groupCount的返回值中。

下面代码中 (John) (.+?)
这个表达式匹配文本 ”John”       后跟一个空格,然后跟1个或多个字符，最后跟一个空格。你可能看不到最后的空格。

```
String text    =
                "John writes about this, and John Doe writes about that," +
                        " and John Wayne writes about everything."
                ;
        String patternString1 = "(John) (.+?) ";
        Pattern pattern = Pattern.compile(patternString1);
        Matcher matcher = pattern.matcher(text);
        while(matcher.find()) {
            System.out.println("found: group  " + matcher.group());
            System.out.println("found: group0 " + matcher.group(0));
            System.out.println("found: group1 " + matcher.group(1));
            System.out.println("found: group2 " + matcher.group(2));
        }
```
运行结果：

```
found: group  John writes 
found: group0 John writes 
found: group1 John
found: group2 writes
found: group  John Doe 
found: group0 John Doe 
found: group1 John
found: group2 Doe
found: group  John Wayne 
found: group0 John Wayne 
found: group1 John
found: group2 Wayne
```



### appendReplacement 和 appendTail 方法
appendReplacement(StringBuffer sb, String replacement) 将当前匹配子串替换为指定字符串，并且将替换后的子串以及其之前到上次匹配子串之后的字符串段添加到一个 StringBuffer 对象里，而 appendTail(StringBuffer sb) 方法则将最后一次匹配工作后剩余的字符串添加到一个 StringBuffer 对象里。

看代码就容易理解了：

```
private static String REGEX = "a*b";
    private static String INPUT = "aabfooaabfooabfoobhh";
    private static String REPLACE = "-";
    public static void main(String[] args) {
        Pattern p = Pattern.compile(REGEX);
        // 获取 matcher 对象
        Matcher m = p.matcher(INPUT);
        StringBuffer sb = new StringBuffer();
        while(m.find()){
            m.appendReplacement(sb,REPLACE);
            System.out.println("appendReolacement "+sb);
        }
        m.appendTail(sb);
        System.out.println("appendTail"+sb.toString());
    }
```

运行结果

```
appendReolacement -
appendReolacement -foo-
appendReolacement -foo-foo-
appendReolacement -foo-foo-foo-
appendTail-foo-foo-foo-hh
```

# 正则表达式语法

> 在其他语言中，\\\ 表示：我想要在正则表达式中插入一个普通的（字面上的）反斜杠，请不要给它任何特殊的意义。
> 在 Java 中，\\\ 表示：我要插入一个正则表达式的反斜线，所以其后的字符具有特殊的意义。
> 所以，在其他的语言中，一个反斜杠\就足以具有转义的作用，而在正则表达式中则需要有两个反斜杠才能被解析为其他语言中的转义作用。也可以简单的理解在正则表达式中，两个 \ 代表其他语言中的一个 \，这也就是为什么表示一位数字的正则表达式是 \\\d，而表示一个普通的反斜杠是 \\\\\\\。


<html>
<table class="reference"><tbody><tr><th><p>字符</p></th><th><p>说明</p></th></tr><tr><td><p>\</p></td><td><p>
将下一字符标记为特殊字符、文本、反向引用或八进制转义符。例如，"n"匹配字符"n"。"\n"匹配换行符。序列"\\\\"匹配"\"，"\\("匹配"("。</p></td></tr><tr><td><p>^</p></td><td><p>匹配输入字符串开始的位置。如果设置了 <b>RegExp</b> 对象的 <b>Multiline</b> 属性，^ 还会与"\n"或"\r"之后的位置匹配。</p></td></tr><tr><td><p>$</p></td><td><p>匹配输入字符串结尾的位置。如果设置了 <b>RegExp</b> 对象的 <b>Multiline</b> 属性，$ 还会与"\n"或"\r"之前的位置匹配。</p></td></tr><tr><td><p>*</p></td><td><p>零次或多次匹配前面的字符或子表达式。例如，zo* 匹配"z"和"zoo"。* 等效于 {0,}。</p></td></tr><tr><td><p>+</p></td><td><p>一次或多次匹配前面的字符或子表达式。例如，"zo+"与"zo"和"zoo"匹配，但与"z"不匹配。+ 等效于 {1,}。</p></td></tr><tr><td><p>?</p></td><td><p>零次或一次匹配前面的字符或子表达式。例如，"do(es)?"匹配"do"或"does"中的"do"。? 等效于 {0,1}。</p></td></tr><tr><td><p>{<i>n</i>}</p></td><td><p><i>n </i>是非负整数。正好匹配 <i>n</i> 次。例如，"o{2}"与"Bob"中的"o"不匹配，但与"food"中的两个"o"匹配。</p></td></tr><tr><td><p>{<i>n</i>,}</p></td><td><p><i>n </i>是非负整数。至少匹配 <i>n </i>次。例如，"o{2,}"不匹配"Bob"中的"o"，而匹配"foooood"中的所有 o。"o{1,}"等效于"o+"。"o{0,}"等效于"o*"。</p></td></tr><tr><td><p>{<i>n</i>,<i>m</i>}</p></td><td><p><i>M</i> 和 <i>n</i> 是非负整数，其中 <i>n</i> &lt;= <i>m</i>。匹配至少 <i>n</i> 次，至多 <i>m</i> 次。例如，"o{1,3}"匹配"fooooood"中的头三个 o。'o{0,1}' 等效于 'o?'。注意：您不能将空格插入逗号和数字之间。</p></td></tr><tr><td><p>?</p></td><td><p>当此字符紧随任何其他限定符（*、+、?、{<i>n</i>}、{<i>n</i>,}、{<i>n</i>,<i>m</i>}）之后时，匹配模式是"非贪心的"。"非贪心的"模式匹配搜索到的、尽可能短的字符串，而默认的"贪心的"模式匹配搜索到的、尽可能长的字符串。例如，在字符串"oooo"中，"o+?"只匹配单个"o"，而"o+"匹配所有"o"。</p></td></tr><tr><td><p>.</p></td><td><p>匹配除"\r\n"之外的任何单个字符。若要匹配包括"\r\n"在内的任意字符，请使用诸如"[\s\S]"之类的模式。</p></td></tr><tr><td><p>(<i>pattern</i>)</p></td><td><p>匹配 <i>pattern</i> 并捕获该匹配的子表达式。可以使用 <b>$0…$9</b> 属性从结果"匹配"集合中检索捕获的匹配。若要匹配括号字符 ( )，请使用"\("或者"\)"。</p></td></tr><tr><td><p>(?:<i>pattern</i>)</p></td><td><p>匹配 <i>pattern</i> 但不捕获该匹配的子表达式，即它是一个非捕获匹配，不存储供以后使用的匹配。这对于用"or"字符 (|) 组合模式部件的情况很有用。例如，'industr(?:y|ies) 是比 'industry|industries' 更经济的表达式。</p></td></tr><tr><td><p>(?=<i>pattern</i>)</p></td><td><p>执行正向预测先行搜索的子表达式，该表达式匹配处于匹配 <i>pattern</i> 的字符串的起始点的字符串。它是一个非捕获匹配，即不能捕获供以后使用的匹配。例如，'Windows (?=95|98|NT|2000)' 匹配"Windows 2000"中的"Windows"，但不匹配"Windows 3.1"中的"Windows"。预测先行不占用字符，即发生匹配后，下一匹配的搜索紧随上一匹配之后，而不是在组成预测先行的字符后。</p></td></tr><tr><td><p>(?!<i>pattern</i>)</p></td><td><p>执行反向预测先行搜索的子表达式，该表达式匹配不处于匹配 <i>pattern</i> 的字符串的起始点的搜索字符串。它是一个非捕获匹配，即不能捕获供以后使用的匹配。例如，'Windows (?!95|98|NT|2000)' 匹配"Windows 3.1"中的 "Windows"，但不匹配"Windows 2000"中的"Windows"。预测先行不占用字符，即发生匹配后，下一匹配的搜索紧随上一匹配之后，而不是在组成预测先行的字符后。</p></td></tr><tr><td><p><i>x</i>|<i>y</i></p></td><td><p>匹配 <i>x</i> 或 <i>y</i>。例如，'z|food' 匹配"z"或"food"。'(z|f)ood' 匹配"zood"或"food"。</p></td></tr><tr><td><p>[<i>xyz</i>]</p></td><td><p>字符集。匹配包含的任一字符。例如，"[abc]"匹配"plain"中的"a"。</p></td></tr><tr><td><p>[^<i>xyz</i>]</p></td><td><p>反向字符集。匹配未包含的任何字符。例如，"[^abc]"匹配"plain"中"p"，"l"，"i"，"n"。</p></td></tr><tr><td><p>[<i>a-z</i>]</p></td><td><p>字符范围。匹配指定范围内的任何字符。例如，"[a-z]"匹配"a"到"z"范围内的任何小写字母。</p></td></tr><tr><td><p>[^<i>a-z</i>]</p></td><td><p>反向范围字符。匹配不在指定的范围内的任何字符。例如，"[^a-z]"匹配任何不在"a"到"z"范围内的任何字符。</p></td></tr><tr><td><p>\b</p></td><td><p>匹配一个字边界，即字与空格间的位置。例如，"er\b"匹配"never"中的"er"，但不匹配"verb"中的"er"。</p></td></tr><tr><td><p>\B</p></td><td><p>非字边界匹配。"er\B"匹配"verb"中的"er"，但不匹配"never"中的"er"。</p></td></tr><tr><td><p>\c<i>x</i></p></td><td><p>匹配 <i>x</i> 指示的控制字符。例如，\cM 匹配 Control-M 或回车符。<i>x</i> 的值必须在 A-Z 或 a-z 之间。如果不是这样，则假定 c 就是"c"字符本身。</p></td></tr><tr><td><p>\d</p></td><td><p>数字字符匹配。等效于 [0-9]。</p></td></tr><tr><td><p>\D</p></td><td><p>非数字字符匹配。等效于 [^0-9]。</p></td></tr><tr><td><p>\f</p></td><td><p>换页符匹配。等效于 \x0c 和 \cL。</p></td></tr><tr><td><p>\n</p></td><td><p>换行符匹配。等效于 \x0a 和 \cJ。</p></td></tr><tr><td><p>\r</p></td><td><p>匹配一个回车符。等效于 \x0d 和 \cM。</p></td></tr><tr><td><p>\s</p></td><td><p>匹配任何空白字符，包括空格、制表符、换页符等。与 [&nbsp;\f\n\r\t\v] 等效。</p></td></tr><tr><td><p>\S</p></td><td><p>匹配任何非空白字符。与 [^&nbsp;\f\n\r\t\v] 等效。</p></td></tr><tr><td><p>\t</p></td><td><p>制表符匹配。与 \x09 和 \cI 等效。</p></td></tr><tr><td><p>\v</p></td><td><p>垂直制表符匹配。与 \x0b 和 \cK 等效。</p></td></tr><tr><td><p>\w</p></td><td><p>匹配任何字类字符，包括下划线。与"[A-Za-z0-9_]"等效。</p></td></tr><tr><td><p>\W</p></td><td><p>与任何非单词字符匹配。与"[^A-Za-z0-9_]"等效。</p></td></tr><tr><td><p>\x<i>n</i></p></td><td><p>匹配 <i>n</i>，此处的 <i>n</i> 是一个十六进制转义码。十六进制转义码必须正好是两位数长。例如，"\x41"匹配"A"。"\x041"与"\x04"&amp;"1"等效。允许在正则表达式中使用 ASCII 代码。</p></td></tr><tr><td><p>\<i>num</i></p></td><td><p>匹配 <i>num</i>，此处的 <i>num</i> 是一个正整数。到捕获匹配的反向引用。例如，"(.)\1"匹配两个连续的相同字符。</p></td></tr><tr><td><p>\<i>n</i></p></td><td><p>标识一个八进制转义码或反向引用。如果 \<i>n</i> 前面至少有 <i>n</i> 个捕获子表达式，那么 <i>n</i> 是反向引用。否则，如果 <i>n</i> 是八进制数 (0-7)，那么 <i>n</i> 是八进制转义码。</p></td></tr><tr><td><p>\<i>nm</i></p></td><td><p>标识一个八进制转义码或反向引用。如果 \<i>nm</i> 前面至少有 <i>nm</i> 个捕获子表达式，那么 <i>nm</i> 是反向引用。如果 \<i>nm</i> 前面至少有 <i>n</i> 个捕获，则 <i>n</i> 是反向引用，后面跟有字符 <i>m</i>。如果两种前面的情况都不存在，则 \<i>nm</i> 匹配八进制值 <i>nm</i>，其中 <i>n </i>和 <i>m</i> 是八进制数字 (0-7)。</p></td></tr><tr><td><p>\<span class="parameter" sdata="paramReference">nml</span></p></td><td><p>当 <i>n</i> 是八进制数 (0-3)，<i>m</i> 和 <i>l</i> 是八进制数 (0-7) 时，匹配八进制转义码 <i>nml</i>。</p></td></tr><tr><td><p>\u<i>n</i></p></td><td><p>匹配 <i>n</i>，其中 <i>n</i> 是以四位十六进制数表示的 Unicode 字符。例如，\u00A9 匹配版权符号 (©)。</p></td></tr></tbody></table>

</html>



# 常用正则表达式


<html>
<p><strong>一、校验数字的表达式</strong></p><p>1 数字：^[0-9]*$</p><p>2 n位的数字：^\d{n}$</p><p>3 至少n位的数字：^\d{n,}$</p><p>4 m-n位的数字：^\d{m,n}$</p><p>5 零和非零开头的数字：^(0|[1-9][0-9]*)$</p><p>6 非零开头的最多带两位小数的数字：^([1-9][0-9]*)+(.[0-9]{1,2})?$</p><p>7 带1-2位小数的正数或负数：^(\-)?\d+(\.\d{1,2})?$</p><p>8 正数、负数、和小数：^(\-|\+)?\d+(\.\d+)?$</p><p>9 有两位小数的正实数：^[0-9]+(.[0-9]{2})?$</p><p>10 有1~3位小数的正实数：^[0-9]+(.[0-9]{1,3})?$</p><p>11 非零的正整数：^[1-9]\d*$ 或 ^([1-9][0-9]*){1,3}$ 或 ^\+?[1-9][0-9]*$</p><p>12 非零的负整数：^\-[1-9][]0-9"*$ 或 ^-[1-9]\d*$</p><p>13 非负整数：^\d+$ 或 ^[1-9]\d*|0$</p><p>14 非正整数：^-[1-9]\d*|0$ 或 ^((-\d+)|(0+))$</p><p>15 非负浮点数：^\d+(\.\d+)?$ 或 ^[1-9]\d*\.\d*|0\.\d*[1-9]\d*|0?\.0+|0$</p><p>16 非正浮点数：^((-\d+(\.\d+)?)|(0+(\.0+)?))$ 或 ^(-([1-9]\d*\.\d*|0\.\d*[1-9]\d*))|0?\.0+|0$</p><p>17 正浮点数：^[1-9]\d*\.\d*|0\.\d*[1-9]\d*$ 或 ^(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[1-9][0-9]*))$</p><p>18 负浮点数：^-([1-9]\d*\.\d*|0\.\d*[1-9]\d*)$ 或 ^(-(([0-9]+\.[0-9]*[1-9][0-9]*)|([0-9]*[1-9][0-9]*\.[0-9]+)|([0-9]*[1-9][0-9]*)))$</p><p>19 浮点数：^(-?\d+)(\.\d+)?$ 或 ^-?([1-9]\d*\.\d*|0\.\d*[1-9]\d*|0?\.0+|0)$</p><p><strong>二、校验字符的表达式</strong></p><p>1 汉字：^[\u4e00-\u9fa5]{0,}$</p><p>2 英文和数字：^[A-Za-z0-9]+$ 或 ^[A-Za-z0-9]{4,40}$</p><p>3 长度为3-20的所有字符：^.{3,20}$</p><p>4 由26个英文字母组成的字符串：^[A-Za-z]+$</p><p>5 由26个大写英文字母组成的字符串：^[A-Z]+$</p><p>6 由26个小写英文字母组成的字符串：^[a-z]+$</p><p>7 由数字和26个英文字母组成的字符串：^[A-Za-z0-9]+$</p><p>8 由数字、26个英文字母或者下划线组成的字符串：^\w+$ 或 ^\w{3,20}$</p><p>9 中文、英文、数字包括下划线：^[\u4E00-\u9FA5A-Za-z0-9_]+$</p><p>10 中文、英文、数字但不包括下划线等符号：^[\u4E00-\u9FA5A-Za-z0-9]+$ 或 ^[\u4E00-\u9FA5A-Za-z0-9]{2,20}$</p><p>11 可以输入含有^%&',;=?$\"等字符：[^%&',;=?$\x22]+</p><p>12 禁止输入含有~的字符：[^~\x22]+</p><p><strong>三、特殊需求表达式</strong></p><p>1 Email地址：^\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*$</p><p>2 域名：[a-zA-Z0-9][-a-zA-Z0-9]{0,62}(/.[a-zA-Z0-9][-a-zA-Z0-9]{0,62})+/.?</p><p>3 InternetURL：[a-zA-z]+://[^\s]* 或 ^http://([\w-]+\.)+[\w-]+(/[\w-./?%&=]*)?$</p><p>4 手机号码：^(13[0-9]|14[5|7]|15[0|1|2|3|5|6|7|8|9]|18[0|1|2|3|5|6|7|8|9])\d{8}$</p><p>5 电话号码("XXX-XXXXXXX"、"XXXX-XXXXXXXX"、"XXX-XXXXXXX"、"XXX-XXXXXXXX"、"XXXXXXX"和"XXXXXXXX)：^(\(\d{3,4}-)|\d{3.4}-)?\d{7,8}$</p><p>6 国内电话号码(0511-4405222、021-87888822)：\d{3}-\d{8}|\d{4}-\d{7}</p><p>7 身份证号(15位、18位数字)：^\d{15}|\d{18}$</p><p>8 短身份证号码(数字、字母x结尾)：^([0-9]){7,18}(x|X)?$ 或 ^\d{8,18}|[0-9x]{8,18}|[0-9X]{8,18}?$</p><p>9 帐号是否合法(字母开头，允许5-16字节，允许字母数字下划线)：^[a-zA-Z][a-zA-Z0-9_]{4,15}$</p><p>10 密码(以字母开头，长度在6~18之间，只能包含字母、数字和下划线)：^[a-zA-Z]\w{5,17}$</p><p>11 强密码(必须包含大小写字母和数字的组合，不能使用特殊字符，长度在8-10之间)：^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{8,10}$ </p><p>12 日期格式：^\d{4}-\d{1,2}-\d{1,2}</p><p>13 一年的12个月(01～09和1～12)：^(0?[1-9]|1[0-2])$</p><p>14 一个月的31天(01～09和1～31)：^((0?[1-9])|((1|2)[0-9])|30|31)$</p><p>15 钱的输入格式：</p><p>16 1.有四种钱的表示形式我们可以接受:"10000.00" 和 "10,000.00", 和没有 "分" 的 "10000" 和 "10,000"：^[1-9][0-9]*$</p><p>17 2.这表示任意一个不以0开头的数字,但是,这也意味着一个字符"0"不通过,所以我们采用下面的形式：^(0|[1-9][0-9]*)$</p><p>18 3.一个0或者一个不以0开头的数字.我们还可以允许开头有一个负号：^(0|-?[1-9][0-9]*)$</p><p>19 4.这表示一个0或者一个可能为负的开头不为0的数字.让用户以0开头好了.把负号的也去掉,因为钱总不能是负的吧.下面我们要加的是说明可能的小数部分：^[0-9]+(.[0-9]+)?$</p><p>20 5.必须说明的是,小数点后面至少应该有1位数,所以"10."是不通过的,但是 "10" 和 "10.2" 是通过的：^[0-9]+(.[0-9]{2})?$</p><p>21 6.这样我们规定小数点后面必须有两位,如果你认为太苛刻了,可以这样：^[0-9]+(.[0-9]{1,2})?$</p><p>22 7.这样就允许用户只写一位小数.下面我们该考虑数字中的逗号了,我们可以这样：^[0-9]{1,3}(,[0-9]{3})*(.[0-9]{1,2})?$</p><p>23 8.1到3个数字,后面跟着任意个 逗号+3个数字,逗号成为可选,而不是必须：^([0-9]+|[0-9]{1,3}(,[0-9]{3})*)(.[0-9]{1,2})?$</p><p>24 备注：这就是最终结果了,别忘了"+"可以用"*"替代如果你觉得空字符串也可以接受的话(奇怪,为什么?)最后,别忘了在用函数时去掉去掉那个反斜杠,一般的错误都在这里</p><p>25 xml文件：^([a-zA-Z]+-?)+[a-zA-Z0-9]+\\.[x|X][m|M][l|L]$</p><p>26 中文字符的正则表达式：[\u4e00-\u9fa5]</p><p>27 双字节字符：[^\x00-\xff] (包括汉字在内，可以用来计算字符串的长度(一个双字节字符长度计2，ASCII字符计1))</p><p>28 空白行的正则表达式：\n\s*\r (可以用来删除空白行)</p><p>29 HTML标记的正则表达式：<(\s*?)[^>]*>.*?|<.*?> (网上流传的版本太糟糕，上面这个也仅仅能部分，对于复杂的嵌套标记依旧无能为力)</.*?></(\s*?)[^></p><p>30 首尾空白字符的正则表达式：^\s*|\s*$或(^\s*)|(\s*$) (可以用来删除行首行尾的空白字符(包括空格、制表符、换页符等等)，非常有用的表达式)</p><p>31 腾讯QQ号：[1-9][0-9]{4,} (腾讯QQ号从10000开始)</p><p>32 中国邮政编码：[1-9]\d{5}(?!\d) (中国邮政编码为6位数字)</p><p>33 IP地址：\d+\.\d+\.\d+\.\d+ (提取IP地址时有用)</p><p>34 IP地址：((?:(?:25[0-5]|2[0-4]\\d|[01]?\\d?\\d)\\.){3}(?:25[0-5]|2[0-4]\\d|[01]?\\d?\\d))</p>
</html>

# 参考文章

[Java 正则表达式](http://www.importnew.com/19381.html)

[Java 正则表达式 菜鸟教程](http://www.runoob.com/java/java-regular-expressions.html)

[最全面的常用正则表达式大全](https://juejin.im/entry/5686056160b2e495ddd8b9a5)