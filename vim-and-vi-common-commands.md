# Linux 学习记录--vim 与 vi 常用命令

# vim 与 vi 常用命令  

###语系编码转换：iconv  
vi 是个文本编辑器，所有 UNIX Like 系统都会内置这个编辑器   
vim 是 vi 的强加版，其具有程序编辑的能力，可以主动以字体颜色辨识语法的正确性。    
###常用命令  

## 移动光标的方法   

<table cellpadding="0" cellspacing="0" border="1" width="95%"><tbody><tr><td><p><span style="font-family:calibri;font-size:14px;">h </span>或向左箭头键<span style="font-family:calibri;">(←)</span></p></td><td><p><span style="font-size:14px;">光标向左移动一个字符</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">j </span>或向下箭头键<span style="font-family:calibri;">(↓)</span></p></td><td><p><span style="font-size:14px;">光标向下移动一个字符</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">k </span>或向上箭头键<span style="font-family:calibri;">(↑)</span></p></td><td><p><span style="font-size:14px;">光标向上移动一个字符</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">l </span>或向右箭头键<span style="font-family:calibri;">(→)</span></p></td><td><p><span style="font-size:14px;">光标向右移动一个字符</span></p></td></tr><tr><td colspan="2"><p><span style="font-size:14px;">如果你将右手放在键盘上的话，你会发现<span style="font-family:calibri;">   hjkl</span>是排列在一起的，因此可以使用这四个按钮来移动光标。如果想要进行多次移动的话，例如向下移动<span style="font-family:calibri;"> 30</span>行，可以使用<span style="font-family:calibri;"> "30j" &nbsp;</span>或<span style="font-family:calibri;"> "30↓"</span>的组合按键，亦即加上想要进行的次数<span style="font-family:calibri;">(</span>数字<span style="font-family:calibri;">)</span>后，按下动作即可<span style="font-family:calibri;">.</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">[Ctrl] + [f]</span></p></td><td><p><span style="font-size:14px;">屏幕<span style="font-family:calibri;">[</span>向下<span style="font-family:calibri;">]</span>移动一页，相当于<span style="font-family:calibri;"> [Page Down]</span>按键<span style="font-family:calibri;"> (</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">[Ctrl] + [b]</span></p></td><td><p><span style="font-size:14px;">屏幕<span style="font-family:calibri;">[</span>向上<span style="font-family:calibri;">]</span>移动一页，相当于<span style="font-family:calibri;"> [Page Up]</span>按键<span style="font-family:calibri;"> (</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">[Ctrl] + [d]</span></p></td><td><p><span style="font-size:14px;">屏幕<span style="font-family:calibri;">[</span>向下<span style="font-family:calibri;">]</span>移动半页</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">[Ctrl] + [u]</span></p></td><td><p><span style="font-size:14px;">屏幕<span style="font-family:calibri;">[</span>向上<span style="font-family:calibri;">]</span>移动半页</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">+</span></p></td><td><p><span style="font-size:14px;">光标移动到非空格符的下一列</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">-</span></p></td><td><p><span style="font-size:14px;">光标移动到非空格符的上一列</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">n&lt;space&gt;</span></p></td><td><p><span style="font-size:14px;">那个<span style="font-family:calibri;"> n </span>表示<span style="font-family:calibri;">[</span>数字<span style="font-family:calibri;">]</span>，例如<span style="font-family:calibri;"> 20</span>。按下数字后再按空格键，光标会向右移动这一行的<span style="font-family:calibri;"> &nbsp; n </span>个字符。例如<span style="font-family:calibri;"> 20&lt;space&gt;</span>则光标会向后面移动<span style="font-family:calibri;"> 20 &nbsp;</span>个字符距离。</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">0 </span>或功能键<span style="font-family:calibri;">[Home]</span></p></td><td><p><span style="font-size:14px;">这是数字<span style="font-family:calibri;">[ 0 ]</span>：移动到这一行的最前面字符处<span style="font-family:calibri;"> (</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">$ </span>或功能键<span style="font-family:calibri;">[End]</span></p></td><td><p><span style="font-size:14px;">移动到这一行的最后面字符处<span style="font-family:calibri;">(</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">H</span></p></td><td><p><span style="font-size:14px;">光标移动到这个屏幕的最上方那一行的第一个字符</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">M</span></p></td><td><p><span style="font-size:14px;">光标移动到这个屏幕的中央那一行的第一个字符</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">L</span></p></td><td><p><span style="font-size:14px;">光标移动到这个屏幕的最下方那一行的第一个字符</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">G</span></p></td><td><p><span style="font-size:14px;">移动到这个档案的最后一行<span style="font-family:calibri;">(</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">nG</span></p></td><td><p><span style="font-family:calibri;font-size:14px;">n </span>为数字。移动到这个档案的第<span style="font-family:calibri;"> n</span>行。例如<span style="font-family:calibri;"> 20G &nbsp;</span>则会移动到这个档案的第<span style="font-family:calibri;"> 20</span>行<span style="font-family:calibri;">(</span>可配合<span style="font-family:calibri;"> :set nu)</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">gg</span></p></td><td><p><span style="font-size:14px;">移动到这个档案的第一行，相当于<span style="font-family:calibri;"> 1G</span>啊<span style="font-family:calibri;">. (</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">n&lt;Enter&gt;</span></p></td><td><p><span style="font-family:calibri;font-size:14px;">n </span>为数字。光标向下移动<span style="font-family:calibri;"> n</span>行<span style="font-family:calibri;">(</span>常用<span style="font-family:calibri;">)</span></p></td></tr></tbody></table>

## 搜寻与取代   

<table cellpadding="0" cellspacing="0" border="1" width="95%"><tbody><tr><td><p><span style="font-family:calibri;font-size:14px;">/word</span></p></td><td><p><span style="font-size:14px;">向光标之下寻找一个名称为<span style="font-family:calibri;"> word</span>的字符串。例如要在档案内搜寻<span style="font-family:calibri;"> asde &nbsp;</span>这个字符串，就输入<span style="font-family:calibri;"> /asde</span>即可<span style="font-family:calibri;">. (</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">?word</span></p></td><td><p><span style="font-size:14px;">向光标之上寻找一个字符串名称为<span style="font-family:calibri;"> word</span>的字符串。</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">n</span></p></td><td><p><span style="font-size:14px;">这个<span style="font-family:calibri;"> n </span>是英文按键。代表<span style="font-family:calibri;">[</span>重复前一个搜寻的动作<span style="font-family:calibri;">]</span>。举例来说，如果刚刚我们执行<span style="font-family:calibri;"> /asde</span>去向下搜寻<span style="font-family:calibri;"> &nbsp; asde </span>这个字符串，则按下<span style="font-family:calibri;"> n</span>后，会向下继续搜寻下一个名称为<span style="font-family:calibri;"> asde &nbsp;</span>的字符串。如果是执行<span style="font-family:calibri;"> ?asde</span>的话，那么按下<span style="font-family:calibri;"> n &nbsp;</span>则会向上继续搜寻名称为<span style="font-family:calibri;"> asde</span>的字符串<span style="font-family:calibri;">.</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">N</span></p></td><td><p><span style="font-size:14px;">这个<span style="font-family:calibri;"> N </span>是英文按键。与<span style="font-family:calibri;"> n</span>刚好相反，为<span style="font-family:calibri;">[</span>反向<span style="font-family:calibri;">]</span>进行前一个搜寻动作。例如<span style="font-family:calibri;"> &nbsp; /asde</span>后，按下<span style="font-family:calibri;"> N </span>则表示<span style="font-family:calibri;">[</span>向上<span style="font-family:calibri;">]</span>搜寻<span style="font-family:calibri;"> asde</span>。</span></p></td></tr><tr><td colspan="2"><p><span style="font-size:14px;">使用<span style="font-family:calibri;"> /word </span> &nbsp;配合<span style="font-family:calibri;"> n </span>及<span style="font-family:calibri;"> N</span>是非常有帮助的<span style="font-family:calibri;">.</span>可以让你重复的找到一些你搜寻的关键词<span style="font-family:calibri;">.</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:n1,n2s/word1/word2/g</span></p></td><td><p><span style="font-family:calibri;font-size:14px;">n1 </span>与<span style="font-family:calibri;"> n2</span>为数字。在第<span style="font-family:calibri;"> n1 &nbsp;</span>与<span style="font-family:calibri;"> n2</span>行之间寻找<span style="font-family:calibri;"> word1 &nbsp;</span>这个字符串，并将该字符串取代为<span style="font-family:calibri;"> word2 .</span>举例来说，在<span style="font-family:calibri;"> 100</span>到<span style="font-family:calibri;"> 200 &nbsp;</span>行之间搜寻<span style="font-family:calibri;"> asde</span>并取代为<span style="font-family:calibri;"> ASDE &nbsp;</span>则：<br><span style="font-family:calibri;font-size:14px;">[:100,200s/asde/ASDE/g]</span><span style="font-size:14px;">。<span style="font-family:calibri;">(</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:1,$s/word1/word2/g</span></p></td><td><p><span style="font-size:14px;">从第一行到最后一行寻找<span style="font-family:calibri;"> word1</span>字符串，并将该字符串取代为<span style="font-family:calibri;"> word2 .(</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:1,$s/word1/word2/gc</span></p></td><td><p><span style="font-size:14px;">从第一行到最后一行寻找<span style="font-family:calibri;"> word1</span>字符串，并将该字符串取代为<span style="font-family:calibri;"> word2 .</span>且在取代前显示提示字符给用户确认<span style="font-family:calibri;"> (confirm)</span>是否需要取代<span style="font-family:calibri;">.(</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr></tbody></table>

## 删除复制与粘贴   

<table cellpadding="0" cellspacing="0" border="1" width="95%"><tbody><tr><td><p><span style="font-family:calibri;font-size:14px;">x, X</span></p></td><td><p><span style="font-size:14px;">在一行字当中，<span style="font-family:calibri;">x </span> &nbsp;为向后删除一个字符<span style="font-family:calibri;"> (</span>相当于<span style="font-family:calibri;"> [del]</span>按键<span style="font-family:calibri;">)</span>，<span style="font-family:calibri;"> X</span>为向前删除一个字符<span style="font-family:calibri;">(</span>相当于<span style="font-family:calibri;"> &nbsp; [backspace]</span>亦即是退格键<span style="font-family:calibri;">) (</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">nx</span></p></td><td><p><span style="font-family:calibri;font-size:14px;">n </span>为数字，连续向后删除<span style="font-family:calibri;"> n</span>个字符。举例来说，我要连续删除<span style="font-family:calibri;"> 10 &nbsp;</span>个字符，<span style="font-family:calibri;"> [10x]</span>。</p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">dd</span></p></td><td><p><span style="font-size:14px;">删除游标所在的那一整列<span style="font-family:calibri;">(</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">ndd</span></p></td><td><p><span style="font-family:calibri;font-size:14px;">n </span>为数字。删除光标所在的向下<span style="font-family:calibri;"> n</span>列，例如<span style="font-family:calibri;"> 20dd &nbsp;</span>则是删除<span style="font-family:calibri;"> 20</span>列<span style="font-family:calibri;"> (</span>常用<span style="font-family:calibri;">)</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">d1G</span></p></td><td><p><span style="font-size:14px;">删除光标所在到第一行的所有数据</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">dG</span></p></td><td><p><span style="font-size:14px;">删除光标所在到最后一行的所有数据</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">d$</span></p></td><td><p><span style="font-size:14px;">删除游标所在处，到该行的最后一个字符</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">d0</span></p></td><td><p><span style="font-size:14px;">那个是数字的<span style="font-family:calibri;"> 0 </span> &nbsp;，删除游标所在处，到该行的最前面一个字符</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">yy</span></p></td><td><p><span style="font-size:14px;">复制游标所在的那一行<span style="font-family:calibri;">(</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">nyy</span></p></td><td><p><span style="font-family:calibri;font-size:14px;">n </span>为数字。复制光标所在的向下<span style="font-family:calibri;"> n</span>列，例如<span style="font-family:calibri;"> 20yy &nbsp;</span>则是复制<span style="font-family:calibri;"> 20</span>列<span style="font-family:calibri;">(</span>常用<span style="font-family:calibri;">)</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">y1G</span></p></td><td><p><span style="font-size:14px;">复制游标所在列到第一列的所有数据</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">yG</span></p></td><td><p><span style="font-size:14px;">复制游标所在列到最后一列的所有数据</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">y0</span></p></td><td><p><span style="font-size:14px;">复制光标所在的那个字符到该行行首的所有数据</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">y$</span></p></td><td><p><span style="font-size:14px;">复制光标所在的那个字符到该行行尾的所有数据</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">p, P</span></p></td><td><p><span style="font-family:calibri;font-size:14px;">p </span>为将已复制的数据在光标下一行贴上，<span style="font-family:calibri;">P</span>则为贴在游标上一行<span style="font-family:calibri;">. &nbsp;</span>举例来说，目前光标在第<span style="font-family:calibri;"> 20</span>行，且已经复制了<span style="font-family:calibri;"> 10 &nbsp;</span>行数据。则按下<span style="font-family:calibri;"> p</span>后，那<span style="font-family:calibri;"> 10 &nbsp;</span>行数据会贴在原本的<span style="font-family:calibri;"> 20</span>行之后，亦即由<span style="font-family:calibri;"> 21 &nbsp;</span>行开始贴。但如果是按下<span style="font-family:calibri;"> P</span>呢？那么原本的第<span style="font-family:calibri;"> 20 &nbsp;</span>行会被推到变成<span style="font-family:calibri;"> 30</span>行。<span style="font-family:calibri;"> (</span>常用<span style="font-family:calibri;">)</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">J</span></p></td><td><p><span style="font-size:14px;">将光标所在列与下一列的数据结合成同一列</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">c</span></p></td><td><p><span style="font-size:14px;">重复删除多个数据，例如向下删除<span style="font-family:calibri;"> 10</span>行，<span style="font-family:calibri;">[ 10cj ]</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">u</span></p></td><td><p><span style="font-size:14px;">复原前一个动作。<span style="font-family:calibri;">(</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">[Ctrl]+r</span></p></td><td><p><span style="font-size:14px;">重做上一个动作。<span style="font-family:calibri;">(</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">.</span></p></td><td><p><span style="font-size:14px;">意思是重复前一个动作的意思。如果你想要重复删除、重复贴上等等动作，按下小数点<span style="font-family:calibri;">[.]</span>就好了<span style="font-family:calibri;">. (</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr></tbody></table>

## 进入插入和替换

<table cellpadding="0" cellspacing="0" border="1" width="95%"><tbody><tr><td><p><span style="font-family:calibri;font-size:14px;">i, I</span></p></td><td><p><span style="font-size:14px;">进入插入模式<span style="font-family:calibri;">(Insert mode)</span>：</span><br><span style="font-family:calibri;font-size:14px;">i </span><span style="font-size:14px;">为<span style="font-family:calibri;">[</span>从目前光标所在处插入<span style="font-family:calibri;">]</span>，<span style="font-family:calibri;"> I</span>为<span style="font-family:calibri;">[</span>在目前所在行的第一个非空格符处开始插入<span style="font-family:calibri;">]</span>。<span style="font-family:calibri;"> &nbsp; (</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">a, A</span></p></td><td><p><span style="font-size:14px;">进入插入模式<span style="font-family:calibri;">(Insert mode)</span>：</span><br><span style="font-family:calibri;font-size:14px;">a </span><span style="font-size:14px;">为<span style="font-family:calibri;">[</span>从目前光标所在的下一个字符处开始插入<span style="font-family:calibri;">]</span>，<span style="font-family:calibri;"> A</span>为<span style="font-family:calibri;">[</span>从光标所在行的最后一个字符处开始插入<span style="font-family:calibri;">]</span>。<span style="font-family:calibri;">(</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">o, O</span></p></td><td><p><span style="font-size:14px;">进入插入模式<span style="font-family:calibri;">(Insert mode)</span>：</span><br><span style="font-size:14px;">这是英文字母<span style="font-family:calibri;"> o </span>的大小写。<span style="font-family:calibri;">o</span>为<span style="font-family:calibri;">[</span>在目前光标所在的下一行处插入新的一行<span style="font-family:calibri;">]</span>；<span style="font-family:calibri;"> &nbsp; O</span>为在目前光标所在处的上一行插入新的一行<span style="font-family:calibri;">.(</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">r, R</span></p></td><td><p><span style="font-size:14px;">进入取代模式<span style="font-family:calibri;">(Replace mode)</span>：</span><br><span style="font-family:calibri;font-size:14px;">r </span><span style="font-size:14px;">只会取代光标所在的那一个字符一次；<span style="font-family:calibri;">R</span>会一直取代光标所在的文字，直到按下<span style="font-family:calibri;"> ESC</span>为止；<span style="font-family:calibri;">(</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td colspan="2"><p><span style="font-size:14px;">上面这些按键中，在<span style="font-family:calibri;"> vi </span> &nbsp;画面的左下角处会出现<span style="font-family:calibri;">[--INSERT--]</span>或<span style="font-family:calibri;">[--REPLACE--]</span>的字样。特别注意的是，我们上面也提过了，你想要在档案里面输入字符时，一定要在左下角处看到<span style="font-family:calibri;"> INSERT</span>或<span style="font-family:calibri;"> REPLACE &nbsp;</span>才能输入</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">[Esc]</span></p></td><td><p><span style="font-size:14px;">退出编辑模式，回到一般模式中<span style="font-family:calibri;">(</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr></tbody></table>

## 存储离开与文件保存

<table cellpadding="0" cellspacing="0" border="1" width="95%"><tbody><tr><td><p><span style="font-family:calibri;font-size:14px;">:w</span></p></td><td><p><span style="font-size:14px;">将编辑的数据写入硬盘档案中<span style="font-family:calibri;">(</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:w!</span></p></td><td><p><span style="font-size:14px;">若文件属性为<span style="font-family:calibri;">[</span>只读<span style="font-family:calibri;">]</span>时，强制写入该档案。不过，到底能不能写入，还是跟你对该档案的档案权限有关啊<span style="font-family:calibri;">.</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:q</span></p></td><td><p><span style="font-size:14px;">离开<span style="font-family:calibri;"> vi (</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:q!</span></p></td><td><p><span style="font-size:14px;">若曾修改过档案，又不想储存，使用<span style="font-family:calibri;"> !</span>为强制离开不储存档案。</span></p></td></tr><tr><td colspan="2"><p><span style="font-size:14px;">注意一下啊，那个惊叹号<span style="font-family:calibri;"> (!)</span>在<span style="font-family:calibri;"> vi &nbsp;</span>当中，常常具有<span style="font-family:calibri;">[</span>强制<span style="font-family:calibri;">]</span>的意思～</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:wq</span></p></td><td><p><span style="font-size:14px;">储存后离开，若为<span style="font-family:calibri;"> :wq! </span> &nbsp;则为强制储存后离开<span style="font-family:calibri;"> (</span>常用<span style="font-family:calibri;">)</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">ZZ</span></p></td><td><p><span style="font-size:14px;">若档案没有更动，则不储存离开，若档案已经被更动过，则储存后离开<span style="font-family:calibri;">.</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:w [filename]</span></p></td><td><p><span style="font-size:14px;">将编辑的数据储存成另一个档案（类似另存新档）</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:r [filename]</span></p></td><td><p><span style="font-size:14px;">在编辑的数据中，读入另一个档案的数据。亦即将<span style="font-family:calibri;"> [filename]</span>这个档案内容加到游标所在行后面</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:n1,n2 w [filename]</span></p></td><td><p><span style="font-size:14px;">将<span style="font-family:calibri;"> n1 </span>到<span style="font-family:calibri;"> n2</span>的内容储存成<span style="font-family:calibri;"> filename &nbsp;</span>这个档案。</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:! command</span></p></td><td><p><span style="font-size:14px;">暂时离开<span style="font-family:calibri;"> vi </span> &nbsp;到指令列模式下执行<span style="font-family:calibri;"> command </span>的显示结果<span style="font-family:calibri;">.</span>例如</span><br><span style="font-family:calibri;font-size:14px;">[:! ls /home]</span><span style="font-size:14px;">即可在<span style="font-family:calibri;"> vi</span>当中察看<span style="font-family:calibri;"> /home &nbsp;</span>底下以<span style="font-family:calibri;"> ls</span>输出的档案信息<span style="font-family:calibri;">.</span></span></p></td></tr></tbody></table>

**vim环境设定与参数**

<table cellpadding="0" cellspacing="0" border="1" width="95%"><tbody><tr><td><p><span style="font-family:calibri;font-size:14px;">:set nu<br> &nbsp;:set nonu</span></p></td><td><p><span style="font-size:14px;">就是设定与取消行号啊<span style="font-family:calibri;">.</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:set hlsearch<br> &nbsp;:set nohlsearch</span></p></td><td><p><span style="font-family:calibri;font-size:14px;">hlsearch </span>就是<span style="font-family:calibri;"> high light search(</span>高亮度搜寻<span style="font-family:calibri;">)</span>。这个就是设定是否将搜寻的字符串反白的设定值。默认值是<span style="font-family:calibri;"> hlsearch</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:set autoindent<br> &nbsp;:set noautoindent</span></p></td><td><p><span style="font-size:14px;">是否自动缩排？<span style="font-family:calibri;">autoindent</span>就是自动缩排。</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:set backup</span></p></td><td><p><span style="font-size:14px;">是否自动储存备份档？一般是<span style="font-family:calibri;"> nobackup</span>的，如果设定<span style="font-family:calibri;"> backup &nbsp;</span>的话，那么当你更动任何一个档案时，则源文件会被另存成一个档名为<span style="font-family:calibri;"> filename~</span>的档案。举例来说，我们编辑<span style="font-family:calibri;"> hosts &nbsp;</span>，设定<span style="font-family:calibri;"> :set backup</span>，那么当更动<span style="font-family:calibri;"> hosts &nbsp;</span>时，在同目录下，就会产生<span style="font-family:calibri;"> hosts~</span>文件名的档案，记录原始的<span style="font-family:calibri;"> hosts &nbsp;</span>档案内容</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:set ruler</span></p></td><td><p><span style="font-size:14px;">还记得我们提到的右下角的一些状态栏说明吗？这个<span style="font-family:calibri;"> ruler</span>就是在显示或不显示该设定值的<span style="font-family:calibri;"> .</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:set showmode</span></p></td><td><p><span style="font-size:14px;">这个则是，是否要显示<span style="font-family:calibri;"> --INSERT--</span>之类的字眼在左下角的状态栏。</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:set backspace=(012)</span></p></td><td><p><span style="font-size:14px;">一般来说，如果我们按下<span style="font-family:calibri;"> i </span> &nbsp;进入编辑模式后，可以利用退格键<span style="font-family:calibri;"> (backspace) </span>来删除任意字符的。但是，某些<span style="font-family:calibri;"> distribution</span>则不许如此。此时，我们就可以透过<span style="font-family:calibri;"> backspace &nbsp;</span>来设定当<span style="font-family:calibri;"> backspace</span>为<span style="font-family:calibri;"> 2 &nbsp;</span>时，就是可以删除任意值；<span style="font-family:calibri;">0</span>或<span style="font-family:calibri;"> 1 &nbsp;</span>时，仅可删除刚刚输入的字符，而无法删除原本就已经存在的文字了<span style="font-family:calibri;">.</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:set all</span></p></td><td><p><span style="font-size:14px;">显示目前所有的环境参数设定值。</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:set</span></p></td><td><p><span style="font-size:14px;">显示与系统默认值不同的设定参数，一般来说就是你有自行变动过的设定参数<span style="font-family:calibri;"> .</span></span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:syntax on<br> &nbsp;:syntax off</span></p></td><td><p><span style="font-size:14px;">是否依据程序相关语法显示不同颜色举例来说，在编辑一个纯文本档时，如果开头是以<span style="font-family:calibri;"> #</span>开始，那么该行就会变成蓝色。如果你懂得写程序，那么这个<span style="font-family:calibri;"> :syntax on &nbsp;</span>还会主动的帮你除错呢<span style="font-family:calibri;">.</span>但是，如果你仅是编写纯文本档案，要避免颜色对你的屏幕产生的干扰，则可以取消这个设定。</span></p></td></tr><tr><td><p><span style="font-family:calibri;font-size:14px;">:set bg=dark<br> &nbsp;:set bg=light</span></p></td><td><p><span style="font-size:14px;">可用以显示不同的颜色色调，预设是<span style="font-family:calibri;">[ light ]</span>。如果你常常发现批注的字体深蓝色实在很不容易看，那么这里可以设定为<span style="font-family:calibri;"> dark.</span>看看，会有不同的样式呢<span style="font-family:calibri;">.</span></span></p></td></tr></tbody></table>

**说明：如果不想每次都进行设置 VIM 环境，可以讲环境命令添加到~/.vimrc 中(此文件需自行创建)**
[root@localhost ~]# vim ~/.vimrc
 set hlsearch
 set backspace=2
 set autoindent
 set ruler
 set showmode
 set nu
 set bg=dark
 syntax on  
以下图列出常用命令

![](images/2.jpg)

## 语系编码转换(iconv)
经常通过文本编辑器查看文字时会出现乱码，出现乱码的主要原因是环境的语系编码与文件的编码不一致导致的，比如系统语系是繁体中文(big5),文件语系是简体中文(gb2312)。
可以通过2种方式解决问题
**1：设置系统语系编码**
[root@localhost ~]# LANG GB2312
**2：语系转为和系统一致**
**语法**：iconv  --list
iconv –f 原编码 –t 新编码 filename [-o newfile]
**选项与参数：**
--list:列出支持的语系
-f:原编码
-t:新编码
-o file:保留源文件 file 为新文件名

本文出自 “StarFlex” 博客，请务必保留此出处[http://tiankefeng.blog.51cto.com/8687281/1372503](http://tiankefeng.blog.51cto.com/8687281/1372503)