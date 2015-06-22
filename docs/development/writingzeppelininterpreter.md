---
layout: page
title: "Writing Zeppelin Interpreter"
description: ""
group: development
---
{% include JB/setup %}

### What is Zeppelin Interpreter

Zeppelin Interpreter is a language backend. For example to use scala code in Zeppelin, you need scala interpreter.
Every Interpreter belongs to an InterpreterGroup. InterpreterGroup is a unit of start/stop interpreter.
Interpreters in the same InterpreterGroup can reference each other. For example, SparkSqlInterpreter can reference SparkInterpreter to get SparkContext from it while they're in the same group. 

<img class="img-responsive" style="width:50%; border: 1px solid #ecf0f1;" height="auto" src="../../assets/themes/zeppelin/img/interpreter.png" />

Interpreter can be launched either using separate classloader or separate JVM process. Sometimes separate classloader causes problem especially when your interpreter uses reflections or trying to grab standard out/err. In this case, separate JVM process is the option you can select. (by checking 'fork' in Interpreter menu, which is default value) When Interpreter is running in separate JVM process, it's communicating with Zeppelin via thrift.

### Make your own Interpreter

Creating a new interpreter is quite simple. Just extend [org.apache.zeppelin.interpreter](https://github.com/apache/incubator-zeppelin/blob/master/zeppelin-interpreter/src/main/java/org/apache/zeppelin/interpreter/Interpreter.java) abstract class and implement some methods.

You can include org.apache.zeppelin:zeppelin-interpreter:[VERSION] artifact in your build system.

### Install your interpreter binary

Once you have build your interpreter, you can place your interpreter under directory with all the dependencies.

```
[ZEPPELIN_HOME]/interpreter/[INTERPRETER_NAME]/
```

### Configure your interpreter

You can configure zeppelin.interpreters property in conf/zeppelin-site.xml
Property value is comma separated [INTERPRETER_CLASS_NAME]

for example, 

```
<property>
  <name>zeppelin.interpreters</name>
  <value>org.apache.zeppelin.spark.SparkInterpreter,org.apache.zeppelin.spark.PySparkInterpreter,org.apache.zeppelin.spark.SparkSqlInterpreter,org.apache.zeppelin.spark.DepInterpreter,org.apache.zeppelin.markdown.Markdown,org.apache.zeppelin.shell.ShellInterpreter,org.apache.zeppelin.hive.HiveInterpreter,com.me.MyNewInterpreter</value>
</property>
```

### Use your interpreter

#### 0.5.0
Inside of a notebook, %[INTERPRETER_NAME] directive will call your interpreter.
Note that the first interpreter configuration in zeppelin.interpreters will be the default one.

for example

```
%myintp

val a = "My interpreter"
println(a)
```

<br />
#### 0.6.0 and later
Inside of a noteobok, %[INTERPRETER\_GROUP].[INTERPRETER\_NAME] directive will call your interpreter.
Note that the first interpreter configuration in zeppelin.interpreters will be the default one.

You can omit either [INTERPRETER\_GROUP] or [INTERPRETER\_NAME]. Omit [INTERPRETER\_NAME] selects first available interpreter in the [INTERPRETER\_GROUP].
Omit '[INTERPRETER\_GROUP]' will selects [INTERPRETER\_NAME] from defualt interpreter group.


For example, if you have two interpreter myintp1 and myintp2 in group mygrp,

you can call myintp1 like

```
%mygrp.myintp1

codes for myintp1
```

and you can call myintp2 like

```
%mygrp.myintp2

codes for myintp2
```

If you ommit your interpreter name, it'll selects first available interpreter in the group (myintp1)

```
%mygrp

codes for myintp1

```

You can only ommit your interpreter group when your interpreter group is selected as a default group.

```
%myintp2

codes for myintp2
```




### Examples

Check some interpreters shipped by default.

 - [spark](https://github.com/apache/incubator-zeppelin/tree/master/spark)
 - [markdown](https://github.com/apache/incubator-zeppelin/tree/master/markdown)
 - [shell](https://github.com/apache/incubator-zeppelin/tree/master/shell)
 - [hive](https://github.com/apache/incubator-zeppelin/tree/master/hive)
