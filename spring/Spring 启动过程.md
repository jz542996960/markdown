### spring-boot 启动过程分析





#### SpringApplication.java 中

在main方法中调用SpringApplication的run方法

![image-20200731131502038](assets/image-20200731131502038.png)

初始化SpringApplication

![image-20200731131536044](assets/image-20200731131536044.png)

````java
@SuppressWarnings({ "unchecked", "rawtypes" })
	private void initialize(Object[] sources) {
		if (sources != null && sources.length > 0) {
			this.sources.addAll(Arrays.asList(sources));
		}
        // 判断是否是web环境
		this.webEnvironment = deduceWebEnvironment();
        //从fav文件里获取初始化的类
		setInitializers((Collection) getSpringFactoriesInstances(
				ApplicationContextInitializer.class));
        // 从fav文件里获取listen        
		setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
		this.mainApplicationClass = deduceMainApplicationClass();
	}
````

 调用SpringApplication 的run方法：



