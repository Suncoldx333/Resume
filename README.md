# Resume
简历
面试

1. 在项目中遇到过什么技术难题？具体是如何解决的？

	技术难题：页面构建中的性能问题，具体表现为应用中社交模块类微博页面展示的流畅度

	解决方案：

		1.了解屏幕显示图像的原理：CPU与GPU的合作生成每一帧的内容，CPU负责视图的生命周期管理、布局计算、文本渲染、图片的解码与绘制；GPU负责纹理渲染、视图混合、图形生成。两者在主线程花费超过一帧的时间就会影响流畅度（掉帧）；

		2.优化角度：将除了生命周期管理之外的任务派发至后台并发列队，并在合适的时机提交至主线程进行展示。所涉及的技术点包含UIView与CALayer的绘制机制、线程安全、后台并发队列的管理、RunLoo与线程的响应关系；

		3.本着不重复造轮子的想法，寻找到满足优化角度的第三方框架Texture，学习源码进行应用，并进行技术分享，提升团队开发效率。

2. 做过最有压力和挑战的项目是什么？项目中你处于怎么样的角色，起到了什么方面的作用？

	项目：运动世界教师版
	
    角色：主程，
		
		负责整个项目从开发到上线的各个环节，
		维护基础架构并解决团队合作开发过程中的问题，
		构建基于JenKins的CI方案，
		为了更好的团队分工及测试安排，学习Git方面的知识，进行项目的组件化并最终应用

	压力与挑战来源：作为项目架构者所带来的更广泛的思维视野、思考问题的全面性、iOS以外知识的学习与实践

3. 接上题，如果再做这个项目，你会在哪些方面进行改善？

	更早的进行项目的组件化，降低业务模块之间耦合度，不拘泥与业务代码，从架构与合作的角度设计并维护整个项目

4. 多线程方法实际中用过了哪些? 发我下那几个方法的文件或代码片段

	延时：

		typealias Task = (_ cancel : Bool) -> Void

		func delay(_ time: TimeInterval, task: @escaping ()->()) ->  Task? {

    		func dispatch_later(block: @escaping ()->()) {
        		let t = DispatchTime.now() + time
        		DispatchQueue.main.asyncAfter(deadline: t, execute: block)
    		}
    		var closure: (()->Void)? = task
    		var result: Task?

    		let delayedClosure: Task = { cancel in
        	
      			if let internalClosure = closure {
            		if (cancel == false) {
                		DispatchQueue.main.async(execute: internalClosure)
            		}
        		}
        		closure = nil
        		result = nil
    		}
	
    		result = delayedClosure

    		dispatch_later {
        		if let delayedClosure = result {
            		delayedClosure(false)
        		}
    		}
  			return result
		}

		func cancel(_ task: Task?) {
    		task?(true)
		}

	线程安全(某个对象的属性的Set&Get)：

		_syncQueue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT,0);

		-(NSString *)someString {
			__block NSString *localSomeString;
			dispatch_sync(_syncQueue, ^{
				localSomeString = _someString;
			});
			return localSomeString;
		}

		-(void)setSomeString:(NSString *)someString {
			dispatch_barrier_async(_syncQueue, ^{
				_someString = someString;
			});
		}


5. 最近1年学习了什么技术或能力，并举下通过学习运用在工作的例子。

	最近1年学习了项目组件化、Redux架构的应用

    例子：

		组件化应用于运动世界校园，将跑步模块、登录模块、动态列表模块、个人主页模块、动态发布模块等业务模块从主项目中划分出来，通过私有pod源与中介模块连接，降低耦合度，同时可以针对性的进行开发与测试，单独的功能开发与测试不影响也不依赖于其他模块，很好的提升合作开发体验；

		Redux架构应用于大部分MVC设计的模块，将页面的变化统一处理，能够令开发者与测试成员更加清晰的了解代码的构建

6. 有自己的开源库和博客么？有做过技术分享么？

	我的Github地址：https://github.com/Suncoldx333

	我对于Swift、组件化、Redux的应用做过技术分享：
	
		组件化的keynote：https://pan.baidu.com/s/1CQkdp_2gI73ctV_LhRLeyQ
		Redux应用的keynote:https://pan.baidu.com/s/1hgFnRgYM-3pEKAjJOfXtfA

7. 平时和哪些人合作？有时候会有冲突的吧？（或者是否有说服对方被对方说服的经历）“有”那你说说上次冲突／经历的情况吧！

	在开发过程中，

		会与前后端同时进行联调，
		与UI同事进行UI确认与验收工作，
		与测试成员进行功能测试与线上Bug修复验收，
		与产品讨论需求；

	比较常见的与测试成员在修复线上Bug的时间上的冲突。
	
	最近的一次是线上版本部分学生偶尔无法记录跑步数据，而同时新版本的开发也正在进行，团队遵循时间优先原则，在与产品和运营确认了修复Bug时间与新版本的发布时间的优先级后，修复Bug较为紧迫，因此提出了如下的方案：

		1.安排我负责修复Bug,
		2.新版本的部分内容由原生开发转变为React-Native开发，安排安卓团队中掌握了RN开发的成员进行开发，
		3.修复Bug之后，我与React-Native开发进行联调


额外：

8. 面试部门的职责：
	
	之前对于K12部门的职责与产品存在误解，认为是一个提供在线教育直播的平台，面试时也由于自己疏忽未确认。在经过Leader的介绍后，是一款基于教师位置，对周边范围的学生进行教育的产品，而我在万航开发运动世界校园版的过程中，对于地图SDK、地图定位精度、定位算法有着丰富的经验积累与知识储备，因此在这一角度上，我认为我很符合部门的需求。

9. 职业规划：

	短期：通过逆向工程的研究，学习涉及C语言、操作系统、编译原理等底层的知识，扩大技术储备，从更深层次学习了解iOS开发；

	长远：跨知识点如前端、脚本语言等技术的学习与融合，提升思考问题的角度，更好的成为团队的领导者与融合剂；

	
