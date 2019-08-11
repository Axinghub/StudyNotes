---
typora-copy-images-to: images
typora-root-url: ./
---

## SpringBoot简介

SpringBoot为 快速启动且最小化配置的Spring应用 而设计，SpringBoot具有一套固化的视图，该视图用于构建生产级别的应用，它使构建独立的Spring生产级别应用变得简单。

## SpringBoot的特性

1. 创建独立的Spring应用
2. 直接嵌入Tomcat、Jetty或Undertow等Web容器（不需要部署war文件）
3. 提供固化的“starter”依赖，简化构建配置
4. 当条件满足时自动地装配Spring或第三方类库
5. 提供运维特性，如指标信息、健康检查及外部化配置
6. 绝无代码生成，并且不需要XML配置

## 独立的Spring应用

在大多数SpringBoot应用场景中，程序直接或间接地使用SpringApplication API引导应用，其中又结合嵌入式Web容器，对外提供HTTP服务。从应用类型上可划分为：

**非Web应用：**

主要用于服务提供、调度任务、消息处理等场景。

**Web应用：**

SpringBoot 1.x 中有且仅有servlet容器实现，包括传统的Servlet和Spring MVC。

SpringBoot 2.x 新增了Reactive Web容器实现，Spring 5.0 WebFlux。

SpringBoot 2.x允许程序显示的设置Web应用的枚举类型（**NONE：非Web类型，SERVLET：Servlet Web，REACTIVE：Reavtive Web**）方法为：setWebApplicationType(WebApplicationType)

Servlet Web依赖：org.springframework.boot:spring-boot-stater-web

Reavtive Web依赖：org.springframework.boot:spring-boot-stater-webflux

**SpringBoot采用嵌入式容器**，独立于外部容器，对应用生命周期拥有完全自主的控制权。

从本质上来说，嵌入式容器属于Spring应用上下文中的组件Beans，这些组件和其他组件均由自动装配特性组装成Spring Bean 定义，随Spring应用上下文启动而注册并初始化

（传统Spring Web和Web MVC的做法是：前者利用ServletContext生命周期构建Web ROOT Spring应用上下文，后者结合Servlet生命周期创建DispatcherServlet的Spring应用上下文，均属于被动回调执行）

### SpringBoot模板应用目录结构

1. .gitignore

   它定义了最常见的文件或目录的版本控制忽略名单。

2. Maven Wrapper 文件

   一种简单的maven构建方式，其运行环境不需要提前安装Maven二进制文件。（文件包括.mvn目录、执行脚本mvnw和mvnw.cmd）

3. SpringBoot应用属性配置文件——application.properties

   默认的应用外部配置文件

4. JUnit测试文件*.Tests.java

   它提供了SpringBoot集成测试的基本模式

### 创建可执行jar

```
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
       </plugin>
    </plugins>
</build>

mvn clear
mvn package
//运行
java -jar *.jar

```

### 可执行jar的资源结构

解压后：

BOOT-INF/classes目录存放应用编译后的class文件

BOOT-INF/lib目录存放应用依赖的jar包

MATE-INF目录存放应用相关的元信息，如MANIFEST.MF

org/目录存放SpringBoot相关的class文件  

### FAT JAR和WAR执行模块

MANIFEST.MF文件中

```
Manifest-Version: 1.0
Implementation-Title: spring-boot-mybatis-annotation-druid
Implementation-Version: 0.0.1-SNAPSHOT
Built-By: Axing
Implementation-Vendor-Id: com.exercise
Spring-Boot-Version: 2.1.0.RELEASE
#可执行jar文件启动器
Main-Class: org.springframework.boot.loader.JarLauncher
#项目引导类
Start-Class: com.exercise.springbootmybatisannotationdruid.SpringBootM
 ybatisAnnotationDruidApplication
Spring-Boot-Classes: BOOT-INF/classes/
Spring-Boot-Lib: BOOT-INF/lib/
Created-By: Apache Maven 3.5.4
Build-Jdk: 1.8.0_131
Implementation-URL: https://projects.spring.io/spring-boot/#/spring-bo
 ot-starter-parent/spring-boot-mybatis-annotation-druid
```



可执行jar文件启动器：JarLauncher

（项目引导类\*Application被JarLauncher装载并执行，JarLauncher会将这些jar文件作为\*Application的类库依赖）

可执行war文件启动器：WarLauncher

### JarLauncher的实现原理

```java
public class JarLauncher extends ExecutableArchiveLauncher {

	static final String BOOT_INF_CLASSES = "BOOT-INF/classes/";

	static final String BOOT_INF_LIB = "BOOT-INF/lib/";

	public JarLauncher() {
	}

	protected JarLauncher(Archive archive) {
		super(archive);
	}

	@Override
    //方法参数Archive.Entry对象看似为JAR文件中的资源
    //entry.getName()方法为获取jar资源的相对路径
    //当application·properties资源位于FAT JAR时，返回true
    //返回false时，说明FAT JAR被解压至文件目录
	protected boolean isNestedArchive(Archive.Entry entry) {
		if (entry.isDirectory()) {
			return entry.getName().equals(BOOT_INF_CLASSES);
		}
		return entry.getName().startsWith(BOOT_INF_LIB);
	}
	//当执行java -jar命令时，、META-INF、资源的Main-Class属性将调用其main(String[] args)方法，实际上调用的是JarLauncher().launch(args)，该方法继承于Launcher。
	public static void main(String[] args) throws Exception {
		new JarLauncher().launch(args);
	}

}
```

**Archive.Entry存在两种实现**

```java
public class JarFileArchive implements Archive {
    ...
	/**
	 * {@link Archive.Entry} implementation backed by a {@link JarEntry}.
	 */
	private static class JarFileEntry implements Entry {

		private final JarEntry jarEntry;

		JarFileEntry(JarEntry jarEntry) {
			this.jarEntry = jarEntry;
		}

		public JarEntry getJarEntry() {
			return this.jarEntry;
		}

		@Override
		public boolean isDirectory() {
			return this.jarEntry.isDirectory();
		}

		@Override
		public String getName() {
			return this.jarEntry.getName();
		}

	}

}
```

```java
public class ExplodedArchive implements Archive {
	...
	/**
	 * {@link Entry} backed by a File.
	 */
	private static class FileEntry implements Entry {

		private final String name;

		private final File file;

		FileEntry(String name, File file) {
			this.name = name;
			this.file = file;
		}

		public File getFile() {
			return this.file;
		}

		@Override
		public boolean isDirectory() {
			return this.file.isDirectory();
		}

		@Override
		public String getName() {
			return this.name;
		}

	}

}
```

**Launcher类**

```java
public abstract class Launcher {
	...
	protected void launch(String[] args) throws Exception {
		JarFile.registerUrlProtocolHandler();
		ClassLoader classLoader = createClassLoader(getClassPathArchives());
		launch(args, getMainClass(), classLoader);
	}
    ...
}
```

```java
public class JarFile extends java.util.jar.JarFile {
	...
	public static void registerUrlProtocolHandler() {
        //注册协议，追加在其后面
		String handlers = System.getProperty(PROTOCOL_HANDLER, "");
		System.setProperty(PROTOCOL_HANDLER, ("".equals(handlers) ? HANDLERS_PACKAGE
				: handlers + "|" + HANDLERS_PACKAGE));
		resetCachedUrlHandlers();
	}

	private static void resetCachedUrlHandlers() {
		try {
			URL.setURLStreamHandlerFactory(null);
		}
		catch (Error ex) {
			// Ignore
		}
	}
    ...
}

```

JarFile.registerUrlProtocolHandler()方法利用了java.net.URLStreamHandler的扩展机制，其实现由URL#getURLStreamHandler(String）提供：

```java
public final class URL implements java.io.Serializable {
	...
    private static final String protocolPathProp = "java.protocol.handler.pkgs";
	...
    static URLStreamHandler getURLStreamHandler(String protocol) {

        URLStreamHandler handler = handlers.get(protocol);
        if (handler == null) {

            boolean checkedWithFactory = false;

            // Use the factory (if any)
            if (factory != null) {
                handler = factory.createURLStreamHandler(protocol);
                checkedWithFactory = true;
            }

            // Try java protocol handler
            if (handler == null) {
                String packagePrefixList = null;

                packagePrefixList
                    = java.security.AccessController.doPrivileged(
                    new sun.security.action.GetPropertyAction(
                        protocolPathProp,""));
                if (packagePrefixList != "") {
                    packagePrefixList += "|";
                }

                // REMIND: decide whether to allow the "null" class prefix
                // or not.
                packagePrefixList += "sun.net.www.protocol";

                StringTokenizer packagePrefixIter =
                    new StringTokenizer(packagePrefixList, "|");

                while (handler == null &&
                       packagePrefixIter.hasMoreTokens()) {

                    String packagePrefix =
                      packagePrefixIter.nextToken().trim();
                    try {
                        String clsName = packagePrefix + "." + protocol +
                          ".Handler";
                        Class<?> cls = null;
                        try {
                            cls = Class.forName(clsName);
                        } catch (ClassNotFoundException e) {
                            ClassLoader cl = ClassLoader.getSystemClassLoader();
                            if (cl != null) {
                                cls = cl.loadClass(clsName);
                            }
                        }
                        if (cls != null) {
                            handler  =
                              (URLStreamHandler)cls.newInstance();
                        }
                    } catch (Exception e) {
                        // any number of exceptions can get thrown here
                    }
                }
            }

            synchronized (streamHandlerLock) {

                URLStreamHandler handler2 = null;

                // Check again with hashtable just in case another
                // thread created a handler since we last checked
                handler2 = handlers.get(protocol);

                if (handler2 != null) {
                    return handler2;
                }

                // Check with factory if another thread set a
                // factory since our last check
                if (!checkedWithFactory && factory != null) {
                    handler2 = factory.createURLStreamHandler(protocol);
                }

                if (handler2 != null) {
                    // The handler from the factory must be given more
                    // importance. Discard the default handler that
                    // this thread created.
                    handler = handler2;
                }

                // Insert this handler into the hashtable
                if (handler != null) {
                    handlers.put(protocol, handler);
                }

            }
        }

        return handler;

    }
}
```















