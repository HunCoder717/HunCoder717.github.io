一.Maven概念
		1.Maven作为依赖管理工具
			可以用来管理大规模的jar包，自动下载相关的jar包(一般自动下载，需要配置阿里镜像)
			jar包之间有较强的依赖性，一个jar包需要借助其他jar包，为了使得jar包更方便管理和规范，所以开发了maven工具(是用java开发的)
		2.Maven作为构建工具
			代替IDE环境执行构建操作
	二.发展历史
		是Apache软件基金会组织的一款专门为Java项目提供构建和依赖管理支持的工具。
	三.具体使用(简单的Demo)
		构建过程中的主要环节：
		--清理
			删除上一次构建的结果(删除target目录)，为下一次构建做好准备
		--编译(compile)
		--测试
		--报告
			针对测试的结果生成一个全面的信息
		--打包
			java工程--jar包
			web工程--war包
				相关的路径需要自己确认在src/main/webapp/WEB-INF/web.xml
			pom包--另外一个工程可以使用的工程包
		--安装(install)
			把一个maven工程打包好的包安装到本地Maven仓库中
		--部署
		
		使用之前在解压目录中，我们需要着重关注 Maven 的核心配置文件：conf/settings.xml
		1.仓库路径
		<localRepository>D:\repository</localRepository>
		2.镜像--防止访问境外网站传输慢
		
		3.按照默认配置运行，Java 工程使用的默认 JDK 版本是 1.5,所以需要进行配置
		<profile>
			<id>jdk-1.8</id>
			<activation>
				<activeByDefault>true</activeByDefault>
				<jdk>1.8</jdk>
			</activation>
			<properties>
				<maven.compiler.source>1.8</maven.compiler.source>
				<maven.compiler.target>1.8</maven.compiler.target>
				<maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
			</properties>
		</profile>

        核心名词：
		*坐标
		  ##三个向量唯一确定一个jar包(一个项目包含多个工程)
			groupId ：公司或组织域名的倒序，通常也会加上项目名称 com.yang.maven
			artifactId：模块的名称，将来作为Maven工程的工程名
			version：模块的版本号，根据自己的需要设定
				-SNAPSHOT(snapshot) 表示快照版本，正在迭代过程中，不稳定的版本
				-RELEASE(release) 表示正式版本
		  (重点)与存储路径的对应关系(将其与项目名称中对应的包联系起来理解)
		  例子：<groupId>javax.servlet</groupId>
				<artifactId>servlet-api</artifactId>
				<version>2.5</version>
			Maven本地仓库根目录\javax\servlet\servlet-api\2.5\servlet-api-2.5.jar
		*命令行
			1.mvn archetype:generate
			   含义：mvn 主程序
					 archetype 插件
					 generate 插件的目标
		*POM(project object model)项目对象模型
			类似的是DOM(document object model)文档对象模型
			-模型化思想
			-核心配置文件 pom.xml 可以说学习maven，就是学习这个配置文件怎么配
		*约定的目录结构
			-target目录：专门存放构建操作输出的结果
			-约定目录结构是为了使得构建过程尽可能自动化完成

            Web工程依赖Java工程
		-也就是Web工程中导入jar包
		-最终java工程会变成jar包，放在Web工程的WEB-INF/lib目录下
		-在web工程中的pom.xml文件中配置 dependence标签
		*依赖范围
			dependence标签里面的<scope>
			可选值：complie(默认)/test/provided(已提供)/runtime/system/import
				 main目录(空间)，test目录(空间)，开发过程(时间)，部署到服务器(时间)
				 main目录--开发的程序所在的地方
				 test目录--测试开发程序的地方
				 开发过程--写代码的过程
				 部署到服务器--是否会被打包到war包
			 已提供表示服务器上面已经有了，所以provided范围的不会部署到服务器。
		*依赖的传递性
			例子：A依赖B，B依赖C，那么A是否能间接依赖C呢？
			  换种说法：A的运行需要B的参与，B的运行需要C的参与，那么我是否可以直接让C参与到A的运行中去呢？
			    既然是传递性，A要到C就必须要通过B，所以要看B依赖C的范围，也就是看B中C对应的scope
			结论：如果是compile 可以
				        test/provided 不可以
				通过结论可以看出，这样规定传递性，可以极大的减少服务器的负载。
		*依赖的排除
			目的：阻断依赖的传递性，防止jar包冲突(一般是版本不同)
			配置方式：
				在dependece标签中配置
				<exclusions>
					<exclusion>
						<!-- 指定要排除的依赖的坐标(不需要写version)-->
					</exclusion>
				</exclusions>