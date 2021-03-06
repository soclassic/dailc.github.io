# 面试记自我介绍部分

## 主干

- 2015年7月毕业，到现在一共2-3年左右的开发经验

- 刚工作时是Android开发，工作一两个月左右就转为前端，到现在前端经验2年以上

- 目前在这个公司主要的职责是框架开发以及技术支撑，在这个公司两年多，第一年时基本是做项目，然后由于表现出色，逐渐脱颖而出，第二年后基本都是框架支撑性工作

- 在这个公司的两个主要成果：hybrid混合开发框架
（用于原生和h5结合的混合开发模式，基本上所有的移动项目都基于这套框架，减少了大量原生人员投入），
以及传统项目驱动型移动前端框架
（用于结合hybrid框架进行项目开发，主要是简单易用，给大量初级前端开发人员使用）

- hybrid混合开发框架是我们是从0开始自研，我（主要负责总体架构设计），结合两名原生人员（负责原生端）一起实现。
移动前端框架主要是我一人设计完成并实现。

- 公司整个移动前端团队的话大概有20人左右，分散在各个不同的小组，然后基本是我一个做技术支撑性工作以及其它人负责项目开发，
主要业务包括公司的混合开发应用，微信公众号，钉钉应用等

- 整个的发展历程大概是：最开始时团队只有几个人从0开始进行移动前端开发，基于第三方的混合开发方案（h5+）进行混合项目开发，
后来我们慢慢发现第三方方案的不足（不太适合这个公司的业务，譬如拓展不方便，发现问题后得等第三方修复，效率低），
于是转到自己的混合开发方案，后来基本上所有的项目都基于这个混合开发方案开发

- 平时业务的话会进行充分利用时间进行技术学习与开源，主要有两个开源项目，
一个是基于公司hybrid框架，抽取业务逻辑，并重新实现的混合开发框架（Android是自己抽取的，iOS和一个朋友一起完成），
另一个是比较受欢迎的H5下拉刷新，有不少人使用，现在有700+star。
通过开源项目学习了不少，譬如完善文档，处理issue以及不断进行版本迭代等，
也在里面尝试了一些项目中无法应用的技术，如gulp+rollup，单元测试等

    
## 总结自己遇到的一些问题

其实，接触前端的这两年中，遇到了不少问题，可能不一定记得全，但大概还是有一些印象的，不同时期关注的问题也会不一样

因为每一个阶段会有不同的总结

- 最早以前遇到的，float坍缩（通过触发bfc解决）；URLscheme交互问题（譬如#号是非法字符）

- 后来的输入框相关：

**如iOS下的输入框fixed的bug**

软件盘唤起后，页面的 fixed 元素将失效（即无法浮动，也可以理解为变成了 absolute 定位），
所以当页面超过一屏且滚动时，失效的 fixed 元素就会跟随滚动了。

解决方案：

1. 归根结底是fixed的父元素滚动带来的问题（只要确保父元素不滚动就可以了），因此可以将fixed单独抽取出来（譬如成为body的子元素），
然后滚动的元素放到其它元素下（如main，然后滚动这个main，这样就不会影响fixed了）

2. 触发软键盘，获取焦点时可以将position改为relative布局，然后失去焦点时重新变回去

3. iscroll等插件解决，不过一般不推荐（iscroll一般都是用transform动画实现的）

**android的软键盘遮挡输入框**

这应该是属于webview混合开发时的bug，

（使用scrollIntoView解决，可以让当前元素滚动到可视区域内）

双胞胎，非标准，但实现的不错（scrollIntoViewIfNeeded）

- 以及其它，click延迟与点透（iOS下的自动缩放机制，可以设置viewport即可解决），
css动画卡顿（采用硬件加速），settimeout与setinternal（进行动画计算会卡，可以采用requestAnimationFrame，下一帧渲染，以及不准时，涉及到JS事件机制）等问题

- 当然还有，date继承问题（归根结底内置对象不允许继承，以及es5与es6有区别），max callback stack以及转圈圈的区别（涉及到浏览器是否有尾递归优化）

- 当然，还有一些沟通问题，譬如前后端联调，一般是出规范，出方案，邮件追踪等，尽量按规范来提高效率

## hybrid混合开发框架的理解

包括优点，缺点，以及应用，与业务的结合

**1.为何要自己研发hybrid框架？**

这个并不是我们凭空想的。而是基于实际的业务需求出发，最终确认确实需要自己造一个轮子

首先，场景是这样的，为了调研与评估混合开发在项目中的应用，我们这边两个Android人员转前端，开始在项目中尝试混合开发。

最初是基于一个第三方公司Dcloud的HTML5+混合开发方案，然后我们做了几个项目后一起评估确实发现混合开发方案的开发效率更高
（譬如仅仅是两个新手前端，也能比较快速的完成项目，而且主要痛点是可以解决一套代码两个版本），
于是后来就开始慢慢的推HTML5+这套混合开发方案。

但是，在项目逐渐多时，慢慢的我们发现了不少问题，譬如：

- 整个项目的流程是由前端人员独立完成的，包括写代码，调试，打包等，
但是经常会遇到一些要拓展原生功能的需求（譬如增加一个原生功能），
而当时的大部分前端人员仅仅是初级前端，也不懂原生开发，往往需要分配原生人员来协助增加，调试，打包上线等等，
这样一来，实际上的沟通成本并不低。

- sdk受第三方的限制，如果我们要自己拓展功能，不是很方便
（因为它以前基本都是配合ide自动打包的，如果我们改了底层的sdk，
很多功能就无法使用了，很局限）

- 每次遇到问题时，修复起来很麻烦
（我们得向第三方提issue，然后等待修复，
而且有时候往往他们都不能及时修复）

- 发现第三方方案的API大而全，比较多，而且往往互相之间还由关联，要用的好，学习成本并不低，
而且它们那套更适合于一整个APP全由js实现，但是不太符合我们公司的业务需求
（我们公司更多的是原生容器有一个基本框架，然后只需要H5实现不同的模块就可以了，
所以契合度并不是很高）

因此，在这种情况下我们决定研发自己的混合开发框架。

**2.解决的痛点**

这款hybrid框架主要是基于这个公司的业务出发，迭代，会更适合这个公司的实际业务场景

- 项目的流程分工明确：原生人员负责底层容器+h5人员负责业务开发，中间通过js调用API来实现原生功能
（这样，h5人员只需要关心h5的业务，而不需要去关心原生的代码流程，在自己熟悉的领域内，效率更高）

- 整个项目分为框架部分+项目自定义部分。框架部分是友几个核心人员维护，然后打包成SDK的形式让项目引入，
项目也可以自己拓展项目自定义的业务功能。而且由于都是同一个公司，遇到问题，只需要提出来，然后框架人员就能尽快修复。避免耽误。

- API设计上更多的是：功能专一，简单易用。
简单点说就是：一个API做一个事情，如果很多API，就继续按功能模块划分，然后配套文档，更近简单易学，可以达到即学即用的要求

**3.迭代过程**

框架并非可以一蹴而就，而是经过不断迭代才逐渐完善。特别是我们刚开始时基本都是新人，处于初级阶段，而且周围没有任何人可以给予技术上的帮助。
都只能靠自己摸索，不断踩坑，一步一步走。

总体来说，经过了三个大版本的迭代（然后小版本的API更新更是若干）：

**4.分工协作情况**

总体来说，整体框架由三名人员完成，我（负责总体架构，API设计，文档梳理，以及前端js库部分），
另外有两名原生人员分别负责Android和iOS端的原生实现

**5.优点与缺点**

这个框架产生的初衷就是用来适应公司的业务，优点是：

- 契合这个公司的业务，原生和h5端分离，让h5人员只关心自己的熟悉领域，提高效率

- 非常适用于这种简单的业务场景（这边更多的业务都是简单的下拉刷新列表+详情展示那种）

- API功能专一，简单易用，基本可以达到边看文档，边学，边写的效果

- 还有一点就是框架中对API进行了一个多平台支撑的兼容，部分API，如alert等在h5也可以直接使用，可以提高代码复用率

缺点：

- 由于业务需求对总能要求不是很高，目前在性能上优化的还不够，譬如预计的一些离线资源访问（数据埋点，权限校验）等优化方案都没用到

- 还有就是hybrid模式的固有缺陷，webview混合开发的效率较低，相比纯原生，或者weex那种js转原生，性能上损耗更大，某些情况下体验不够

**6.未来可完善的地方**

- 如离线资源优化（可能需要用到路径映射表，拦截网络地址请求，然后映射到本地，当然资源映射表可以从服务端下载，这样的话可以进行版本控制）

- 离线本地路径图片资源的使用（这一块应该涉及到原生读取路径，返回给浏览器文件流）

- 如数据埋点（便于统计分析），权限校验（譬如平台APP对外发布，第三方对接时通常需要用到这一块）等

## 移动前端框架的理解

优点，缺点，与vue等区别，为何适用于这个业务

**1.为何要开发一个传统型的移动前端框架**

主要是基于此公司的一个大环境：

- 整体的前端技术水准不行，很多都可能是以前写java业务转过来的jsp人员（而且是门槛比较低的那种）

- 整体业务比较简单，偏传统的列表想去界面，而且公司整体偏保守，对新技术不是很热衷

- 公司以前没有一款移动前端开发框架，导致开发效率低

因此，我结合此公司的业务情况，开发出了一款使用上尽量简单，尽量和传统的业务开发模式契合，变更不大的移动前端框架

用来方便他们进行项目开发，提高开发效率

**2.解决的痛点**

- 基于一个统一框架，写出来的代码更符合规格，包括一些组件，工具类可以提高考法效率，而且开发出来代码更加同质化，便于维护

- 没有使用vue，react等一些较现代的框架，而是采用了传统的开发模式，和以前的开发模式更加贴近，学习成本低

- 内部结合了hybrid框架前端库，更加无缝的结合公司的混合开发方案

**3.开发过程**

总体来说，主要由我一个人架构，设计，并开发完成

当然，后续加了一两个共同协作的人员，他们可以能会将某些组件提交进来，共同迭代

**4.为什么没有用vue，react？**

这个是有实际原因的，整体技术选型评估，我都有参与

首先，我自己选型的时候就排除了react，原因是：

react相对来说学习成本比较高（或者说对开发人员的技术素养要求更高，并不是谁都能马上上手），
而这个公司恰好的情况就是前端人员的技术素质普遍偏低（更多的都是那种一知半解的或者新手，这与入职门槛有关，
我也面试过几个人，知道这点薪资很难吸引真正有技术的人-这边竞争力不够）

然后当初预计划是选型vue的，因此还特地调研了下vue以及weex，可惜，最后评估时被拒了

当然我也分析了下被拒原因（重新再评估一次不一定被拒）

- 演示时，没有注意突出亮点，整体过程过于繁琐，譬如当初是基于webpack的脚手架，
然后结合输入npm命令，以及.vue单独的膜拜文件，路由机制等演示。
让领导觉得过于复杂，而且和以前的开发模式冲突较大，所以拒绝了

- 当然，当时的领导还有一层考虑是：公司的整体业务偏简单，都是列表详情这种居多，
另外前端人员的技术素质偏低（因为门槛低），所以觉得没有必要引入一个改变较大的开发模式

现在再分析下，其实可以这样：

- 尽量突出vue的强项，减少繁琐的流程

所以，会基于浏览器引入的版本，而不是自动构建那一套，这样更平滑，改动没那么大，更容易接受。
而且，会突出它的优势：

mvvm，双向绑定，减少dom操作，可以很大程度提升性能（经过这边的经验，平时业务中，对性能影响最大的就是频繁的DOM操作），
特比是对于一些新手，不给他们操作DOM的机会，这样就能避免这方面的损耗（据经验，新手恰好是最容易写出性能不好的代码）

另外，双向绑定，只需要操作数据，自动更新视图，代码的复杂性会降低。
譬如，传统的代码中，一个ajax后再更新视图都需要进行DOM操作，如果要优化，甚至还得把DOM操作提取，复用，整体复杂度不低。
而用mvvm的话，只需要操作数据就可以了，它会自动更新

所以总的来说，先将vue这套的优势体现，转型过来后，后续可以再慢慢的往对应的工程化上面优化，靠拢

**5.优点与缺点**

优点：

- 简单，易用，即学即用

- 适合于这个公司的简单业务情况，而且大部分人员都可以直接使用，提高开发效率（而且框架还有一点规范性的约束）

缺点：

- 相比于mvvm，仍然是传统的DOM操作，性能方面，代码复杂度上都不太好控制

- 相比于webpack工程化那一套，无法对静态资源进行统一合并优化，如果遇到一些性能要求高的场景，基本无法做到
（譬如对首屏加载要求严格的基本都无法很好做到，只能单独用优化方案去自定义）

**6.未来可完善的地方**

总的来说，在这种传统开发上，结合与这个公司的业务，这套框架基本已经是接近最优了。

但是，如果后续的需求是越来越注重性能，体验。

那么基本上往工程化，vue那一套转是必须的。
（另外，其实对于技术较低的初级人员来说，可能接触工程化的代价比适用于vue那一套更难，
当然，其实如果真的狠下心去推，个人觉得是没什么问题的）

http://efe.baidu.com/blog/mobile-fixed-layout/

## 面向hr版本

- 93年11月出生，2015年7月毕业于江南大学，计算机相关专业，然后从毕业后就一直在现在这家公司，从事移动前端开发，有2-3年的开发经验

- 我在这家公司的主要工作是框架开发和技术支撑，在这家公司的几个主要成果也都是框架相关的。
一个是hybrid模式混合开发框架，主要用来解决原生人员资源不够的问题，有了这个框架后可以原生和h5分开，协作开发项目，
复用了大量的前端人员，减少了人力资源成本，同时提高了开发效率。

一个是移动前端框架，也是针对这个公司特定业务需求出发的，用来方便前端人员使用，提高开发效率

- 整个移动前端相关的团队大概有20人左右，由我来主要负责框架开发和技术支撑，其他人更多的是进行项目开发，
业务场景包括混合开发APP，微信公众号，钉钉应用号等

- 在工作之余的空闲时间和业务时间，我个人热爱开源和写博客进行总结，也会特地规划时间出来学习新的技术，确保自己不断在进步，
同时也会开一些技术相关书籍，巩固基础，看一些英语相关书籍，慢慢提升自己的阅读水平，以及还有一门业务爱好：吹口琴，
某次年会上还和同事组成小乐队演出过。
总之，我会避免自己闲下来，只有感觉在不断的进步，才能让自己心安。

- 我向往的平台与团队，是那种能给我带来高速成长的，我希望与最优秀的人共事，一起成长，现在处于事业初期，苦点累点不要紧，
重要的就是要能够有足够的发展空间，因为我渴望进步，期待着挑战自我，发掘自己的潜力。


如果问答：

- 自认为最大的优点就是：坚持
其他人认为我有的特地的：学习能力强，工作效率高，职业技能优秀，我个人认为都是由此衍生的

（2年以上）譬如我从工作后，就开始坚持吹口琴，一直到现在，基本都是每天风雨无阻。

（1年以上）我从去年开始规划人生后就开始坚持：
看英语书籍，每天坚持一点点，提升阅读水准。
坚持看技术书籍与写博客总结，在不断总结中，技术成长的更快。
还坚持每天50的俯卧撑与仰卧起坐，每一天花一点点时间，增加身体素质。

我也最欣赏坚持的人，因为我认为，坚持的人有一种专一的气质。

另外，我也发现，身边的人，真正能坚持的非常少，而且有一个特性就是：

优秀的人不一定坚持，但坚持的人往往都优秀。

缺点？
个人认为：在同一时间，我只擅长专注于做一件事，如果手里同时有几件事，我会变得低效
（这点其实很多身边的程序员都有，这点感觉可以通过锻炼来逐步完善）

- 身边的人说我是（从他人的视角出发，更有信服力，而且确实是事实）过于谨慎，很多时候都是三思而后行，有时候不够果敢。
（就拿换工作举例，很多人都是第一年后觉得不满足了，就直接跳槽，去找一个更好的了，
而我则考虑了很多因素，譬如当前水准不足，不够去顶尖公司，以及频繁跳槽对成长体验不好等等
于是，在这里又带了一年后才开始规划换工作。
不过有得必有失，如若是去年，我觉得是里阿里社招标准还是有一定距离的，
但现在，我已经觉得我可以胜任了，于是来尝试了）

这种情况，有时候我会强迫自己做一些决定，譬如参加阿里的社招就是一次挑战自我，遇到了内推信后就当机立断的投简历，
放在以前的话，不一定有这个决断，说不定会继续想着想提升技术一段时间后再试。

当然身边的人也说我是那种可以影响其他人的人。