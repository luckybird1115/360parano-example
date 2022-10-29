---
title: krpano学习笔记--干货
date: 2019-03-20 00:46:27
tags: krpano
categories: 全景
---
介绍krpano开发中的一些注意事项和重要属性等
<!--more-->
# 1.Krpano xml
1.	Onstart  
onstart事件将在xml加载和解析之后直接调用。
2.	Basedir  
basedir设置为所有下面的loadpano（）动作调用定义了基本目录。这可以是相对路径或绝对路径，也可以在这里使用 url占位符。默认值是％FIRSTXML％，这意味着所有下面的loadpano（）动作调用的所有相对xml路径都是相对于第一个加载的xml文件。
3.	关于vars和initvars区别  
vars在加载xml后加载图像前赋值，而initvars在加载xml前赋值，一般用于构建url。由于get:variable或calc:variable中的变量值需要在加载xml中定义，所以使用vars不能在开始加载时控制krpano的某些设置，但是可以在之后动态的修改其显示状态。
4.	Action: 参数/参数传递/解析
当调用一个动作时，可以将参数/参数传递给它。
为了访问参数/参数，有两种可能性：
+ 通过占位符替换：（ 旧版本的方法）
	可以使用从％0到％99形式的操作代码中的数字占位符来获取给定的参数。
	其中％0是操作本身的名称， ％1表示第一个参数， ％2表示第二个参数，依此类推。
	这些占位符可以在代码中的任何地方使用。
	在实际的操作代码将被解析并执行之前，所有的占位符将被其参数值替换。
	当给定的占位符不会有参数时，将使用值'null'。
	要在动作中使用％字符，需要使用%%
+ 通过参数到变量映射:( 推荐，新的）
	对于具有本地作用域的操作，可以将args属性添加到<action\>元素。例如：  
	`<action ... args =“var1，var2，var3”>`
	在那里可以定义给定动作参数/参数的args属性变量名称（用逗号分隔）。
	每个动作参数将被映射到本地作用域中的一个新变量，其名称是在args属性中设置的。
	这些变量可以像操作代码中的任何其他变量一样正常使用（例如，通过get / calc / copy读取）。
	当给定变量没有动作参数时，将使用值'null'。
	处理包含引号或逗号字符的值时，使用变量映射非常有用。在这种情况下，正常的占位符替换可能会产生问题
	注意 - 建议尽可能使用参数变量映射。没有％N占位符的动作可以在内部进行缓存，这使得进一步调用的执行速度更快。

# 值得注意的属性
1.	Name属性：每个名字都需要以字母字符开头！这意味着不允许使用数字作为名字！当名称仍然以数字字符开头时，名称将被解释为数组索引。这对于动作内部基于动态索引的访问没有问题，但不能用于定义元素！所有名称将自动转换为小写字母，以便直接区分大小写访问！
2.	URL属性：
当一个相对路径被用作url值时，路径将被自动调整为相对于定义了url属性的xml文件。这意味着xml中的相对路径总是相对于xml本身，但是请注意 - 当动态设置/更改url属性时，则不再有与xml的自动关系！要使路径相对于某些特定的预定义路径，可以 在url路径中使用这些占位符中的一些：
％FIRSTXML％ - 第一个加载的xml文件的路径。
％CURRENTXML％ - 当前加载的主要xml文件的路径（不是包含的）。
％SWFPATH％ - krpano查看器文件的路径。
％HTMLPATH％ - html文件的路径。
％BASEDIR％ - 使用basedir路径。
％$ VARIABLE％ - 使用给定'VARIABLE'的值 - 这可以是任何krpano变量，但必须在加载当前xml或场景之前定义它，例如，在嵌入过程中（通过initvars）或在loadpano（），loadscene（）调用之前。
3.	Style属性
<style\>元素是任何类型属性的集合/存储元素。
每个其他具有name属性的xml元素也可以有一个style属性。当xml元素首次被创建时，在<style\>元素处定义的所有属性将被复制到元素本身。这将在xml元素本身定义的属性被应用之前完成。
这意味着可以在<style\>元素中预先定义一些设置，然后使用直接在元素上定义的属性稍后覆盖它们。
4.	Get:|Calc方法
当xml属性的值以get：或calc：开头时：
在这种情况下，xml属性的值将从其他变量获取或使用表达式计算。
在get：或calc：中使用的变量需要在当前xml元素之前（=上方）定义。此外，这里有一个特殊情况 - 在<include\>元素上使用它时，那么在那里使用的变量需要在当前xml加载之前定义。这可以在嵌入期间通过使用initvars设置或在xml中进行loadpano（）调用之前在html文件中完成。


# Javascript krpano操作 （仅限HTML5）
```
<action name =“...” type =“ Javascript ” > <！[CDATA [     ... autorun =“”

    Javascript代码
    ... 
]]> </ action>
```
Action type krpano操作的类型：
type =“”（默认） - 正常krpano操作 - 适用于Flash和HTML5
type =“Javascript” - JavaScript krpano操作 - 仅限HTML5

Action scope
在操作中为新生成的变量定义范围：
全局或未设置（默认）
新变量将被添加到全局变量作用域中。
其他任何行为或代码也可以使用它们。
这在再使用变量名称时可能会有问题，特别是当变量类型在用法之间会有所不同时。
本地  
新变量将被添加到仅存在于当前操作调用中的局部变量作用域中。
当动作完成或调用其他动作时，局部作用域和其中定义的所有变量将不再可用。
动作越复杂，它使用的临时变量越多，使用本地作用域进行此操作越有意义，以避免干扰其他动作的问题。
设置LocalOnly
有些作为scope = local，但区别在于，默认情况下，所有访问仅引用本地作用域本身。
全局范围的访问只能由全局对象来实现。
亲
使用调用者的范围。
当调用当前动作的动作具有局部范围时，则可以在当前动作中使用/访问该动作。
私人：NAME
为该操作定义一个私有本地范围。
范围将由自定义的唯一名称进行参考。
该范围在多个操作调用之间保持活动状态，并可以在多个操作中共享。
有些作为范围= localonly所有访问只涉及本地范围本身。

# 笔记
全局范围访问 - 搜索变量时，首先搜索本地范围，然后搜索全局范围。为了能够将变量添加到全局范围，即使在使用本地范围或直接访问全局范围内的变量时，也存在全局对象。
本地范围访问 - 为了能够确保访问本地范围（例如，当可能已经存在具有相同名称的全局变量时定义本地变量），当前本地范围也可用作 操作代码中的本地对象。例如使用def（local.i，integer，0）; 定义一个名为'i'的局部整型变量。为了稍后访问动作代码，只需使用'i'来解决该变量就足够了。
延期代码 - 稍后调用其他代码的操作（例如delayedcall，tween，asyncloop等）将使用与该代码中当前操作相同的范围。
调用者范围 - 从插件/图层/热点事件调用的代码
对于非localscope动作：
当一个动作（或任何代码）将从plugin / layer / hotspot事件中被调用时，plugin / layer / hotspot对象本身也将作为搜索变量的范围。但仅限于访问现有的变量！新生成的变量将始终添加到全局范围中。
对于localscope动作：
在localscope动作中总是有预定义的局部变量调用者。当动作将从插件/图层/热点事件（或通过使用callwith）被调用时，那么该 调用者变量将引用该元素。否则，调用者变量将为空。

本地作用域操作中的 预定义变量：
actionname - 当前操作的名称。
args - 参数的值数组。 
访问者：args [index]。
通过以下方式获取数字或参数：args.length。
调用者 - 从那里调用插件/图层/热点对象，否则为null。
全球 - 参考全球范围。
本地 - 对当前本地范围本身的引用。
parentscope - 父行为的范围引用，当没有父范围时，这指的是全局范围。
