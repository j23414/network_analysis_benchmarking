# TinkerPop3

** References**

* [Original Tutorial](https://tinkerpop.apache.org/docs/3.1.0-incubating/tutorials-getting-started.html)

## Installation

```
$ curl -L -O https://www.apache.org/dist/incubator/tinkerpop/3.1.0-incubating/apache-gremlin-console-3.1.0-incubating-bin.zip
$ unzip apache-gremlin-console-3.1.0-incubating-bin.zip
$ cd apache-gremlin-console-3.1.0-incubating
$ ./bin/gremlin.sh   
```

The last command starts gremlin. 

Worked fine on the linux computers...
```

         \,,,/
         (o o)
-----oOOo-(3)-oOOo-----
plugin activated: tickerpop.server
plugin activated: tickerpop.utilities
plugin activated: tickerpop.tickergraph
gremlin> 
```

Did not work on my Mac

```

         \,,,/
         (o o)
-----oOOo-(3)-oOOo-----
Exception in thread "main" java.lang.NoClassDefFoundError: java/util/concurrent/CompletableFuture
	at java.lang.Class.getDeclaredMethods0(Native Method)
	at java.lang.Class.privateGetDeclaredMethods(Class.java:2615)
	at java.lang.Class.privateGetPublicMethods(Class.java:2733)
	at java.lang.Class.getMethods(Class.java:1472)
	at java.beans.Introspector.getPublicDeclaredMethods(Introspector.java:1280)
	at java.beans.Introspector.getTargetMethodInfo(Introspector.java:1141)
	at java.beans.Introspector.getBeanInfo(Introspector.java:416)
	at java.beans.Introspector.getBeanInfo(Introspector.java:163)
	at groovy.lang.MetaClassImpl$15.run(MetaClassImpl.java:3289)
	at java.security.AccessController.doPrivileged(Native Method)
	at groovy.lang.MetaClassImpl.addProperties(MetaClassImpl.java:3287)
	at groovy.lang.MetaClassImpl.initialize(MetaClassImpl.java:3263)
	at org.codehaus.groovy.reflection.ClassInfo.getMetaClassUnderLock(ClassInfo.java:251)
	at org.codehaus.groovy.reflection.ClassInfo.getMetaClass(ClassInfo.java:282)
	at org.codehaus.groovy.runtime.metaclass.MetaClassRegistryImpl.getMetaClass(MetaClassRegistryImpl.java:255)
	at org.codehaus.groovy.vmplugin.v7.Selector$InitSelector.getMetaClass(Selector.java:365)
	at org.codehaus.groovy.vmplugin.v7.Selector$MethodSelector.setCallSiteTarget(Selector.java:947)
	at org.codehaus.groovy.vmplugin.v7.IndyInterface.selectMethod(IndyInterface.java:211)
	at org.apache.tinkerpop.gremlin.console.Console.<init>(Console.groovy:90)
	at org.codehaus.groovy.vmplugin.v7.IndyInterface.selectMethod(IndyInterface.java:215)
	at org.apache.tinkerpop.gremlin.console.Console.main(Console.groovy:303)
Caused by: java.lang.ClassNotFoundException: java.util.concurrent.CompletableFuture
	at java.net.URLClassLoader$1.run(URLClassLoader.java:366)
	at java.net.URLClassLoader$1.run(URLClassLoader.java:355)
	at java.security.AccessController.doPrivileged(Native Method)
	at java.net.URLClassLoader.findClass(URLClassLoader.java:354)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:425)
	at sun.misc.Launcher$AppClassLoader.loadClass(Launcher.java:308)
	at java.lang.ClassLoader.loadClass(ClassLoader.java:358)
	... 21 more
```

In comparison with Mango 

* On Mac, drag and drop to applications
* On Windows, run through the setup exe file
* On Linux, fetch and unzip (Maybe we should have a curl command...)


```
g.V()
```

```
1) nodes("g");
2) g.node."1"._text="Here";g.node."1"._radius=2;
2) foreach node in g where id==1 set _text="Here",_radius=2; /*take longer*/
3) print g.node."1".name;
4) select link from g where label=="knows" && (in.id==1||out.id==1);
```

