---
layout: post
title: "Java REPL For Your Projects"
date: 2015-10-07T05:19:10+05:30
author: Swathi Venkatachala
comments: true
tags:
- java
- REPL
- projects
- script
- JShell
- tech
---

We have seen REPL (Read-Eval-Print Loop) in lots of languages like Scala, Python, Ruby, Haskell etc. but, orginates from LISP. 
This feature really helps in rapid testing and learning the language. Java needed one such and so, in Java 9, we can expect this.
Refer here : [The Java Shell (Read-Eval-Print Loop)](http://openjdk.java.net/jeps/222) as `Project Kulla` 

However, if you would like to try REPL for Java 6+, you can give this a try - [java-repl](https://github.com/albertlatacz/java-repl) by [Albert Latacz](https://github.com/albertlatacz)

### Getting Started
1. Download the [jar](http://albertlatacz.published.s3.amazonaws.com/javarepl/javarepl.jar)
2. Open the terminal 
{% highlight sh %}

$ java -jar javarepl.jar
Welcome to JavaREPL version 282 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_60)
Type expression to evaluate, :help for more options or press tab to auto-complete.
java>:help

{% endhighlight %}

### Advanced
In this section, we shall look into two things :

1. Importing Other Projects

2. REPL Support For Your Project

The above two are pretty much the same! While the first one is when you would like to import third party libraries and the latter is a feature support that you would like to give, on your project, to your developers - more like using APIs.

#### Importing Other Projects
You need to add `javarepl.jar` and the path to third party jars (mostly in `lib` directory) to your classpath and execute as below.
{% highlight sh %}

$ java -cp ${CLASSPATH} javarepl.Main
Welcome to JavaREPL version 282 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_60)
Type expression to evaluate, :help for more options or press tab to auto-complete.
java>

{% endhighlight %}
where ${CLASSPATH} can be javarepl.jar:path_to_jar_1:path_to_jar_2: . . . :path_to_jar_n


#### REPL Support For Your Project
If you need a managed way to invoke a REPL environment without the hassle of CLASSPATH, you can tweak this script ( mostly two parameters per your needs `YOUR_PROJECT_DIR` and `YOUR_PROJECT_BUILD_DIR`.


{% highlight sh %}
#!/bin/bash
# script name : your-project-shell
# script location : parent-project-dir > your-project-shell sub-project-dir1 sub-project-dir2 sub-project-dir3 ...
# run as : ./your-project-shell

CURR_DIR=`pwd`
REPL_JAR_NAME="javarepl.jar"
EXT_LIB_FOLDER_NAME="lib"
EXT_LIB_FOLDER_PATH="$CURR_DIR/$EXT_LIB_FOLDER_NAME"
PATH_TO_REPL_JAR="$CURR_DIR/$EXT_LIB_FOLDER_NAME/$REPL_JAR_NAME"

YOUR_PROJECT_DIR="some-pretty-api-module"
YOUR_PROJECT_BUILD_DIR="$CURR_DIR/$YOUR_PROJECT_DIR/build/libs"

# create a lib dir, for external jars
mkdir -p "${EXT_LIB_FOLDER_PATH}"

isJarExists() {
	echo "Checking if Java REPL exists ..."
	test -f "$1" && echo "OK."
}

installJar() {
	echo "Java REPL not found! Installing ..."
	echo
	shift
	pushd "${EXT_LIB_FOLDER_PATH}" > /dev/null
	"$@"
	popd > /dev/null
	if [ $? -eq 0 ]; then
		echo "OK."
	fi
}

# Determine if java repl jar exists or else install
if ! isJarExists "$PATH_TO_REPL_JAR" ; then
	installJar javarepl wget 'http://albertlatacz.published.s3.amazonaws.com/javarepl/javarepl.jar' -O "$PATH_TO_REPL_JAR"
fi

# Determine the Java command to use to start the JVM.
if [ -n "$JAVA_HOME" ] ; then
	if [ -x "$JAVA_HOME/jre/sh/java" ] ; then
		# IBM's JDK on AIX uses strange locations for the executables
		JAVACMD="$JAVA_HOME/jre/sh/java"
	else
		JAVACMD="$JAVA_HOME/bin/java"
	fi
	if [ ! -x "$JAVACMD" ] ; then
		die "ERROR: JAVA_HOME is set to an invalid directory: $JAVA_HOME

		Please set the JAVA_HOME variable in your environment to match the
		location of your Java installation."
	fi
else
	JAVACMD="java"
	which java >/dev/null 2>&1 || die "ERROR: JAVA_HOME is not set and no 'java' command could be found in your PATH.

	Please set the JAVA_HOME variable in your environment to match the
	location of your Java installation."
fi

# Add all the required jars to classpath
if [[ -d "${EXT_LIB_FOLDER_PATH}" ]]; then
	for jar in "${EXT_LIB_FOLDER_PATH}"/*.jar; do
		echo "[INFO] : Adding $jar to the CLASSPATH ..."
		if [ -z $CLASSPATH ]; then
			export CLASSPATH="$jar"
		else
			export CLASSPATH="$jar:$CLASSPATH"
		fi
	done
fi

if [[ -d "${YOUR_PROJECT_BUILD_DIR}" ]]; then
	for jar in "${YOUR_PROJECT_BUILD_DIR}"/*.jar; do
		echo "[INFO] : Adding $jar to the CLASSPATH ..."
		if [ -z $CLASSPATH ]; then
			export CLASSPATH="$jar"
		else
			export CLASSPATH="$jar:$CLASSPATH"
		fi
	done
fi

# List in the CLASSPATH
echo -e "[INFO] : CLASSPATH : ${CLASSPATH}"

exec ${JAVACMD} -cp ${CLASSPATH} javarepl.Main
{% endhighlight %}

Hope this helps!

Happy REPL-ing! :)

