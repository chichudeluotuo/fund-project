# fund-project

时间：2018年2月3日17:27:24

项目实现基金交易的中间环节。具体说：连接前端APP、微信端、WEB端，介绍前端信息，向基金公司发送申购、赎回、交易历史查询、持仓查询、收益查询等操作。
其中用户信息系统、卡服务系统、基金系统全部模拟实现



百信项目组
负责：额度中心升级改造可行性研讨及风险评估，生产数据库批量修改，数据库数据校验，风控系统外部服务异常处理，完善Jenkins代码构建功能、代码评审重构

具体负责：为了更好的对贷款产品进行分组，增加额度新二级类型额度表，刷新旧一级账户额度数据，数据库刷新完成后，使用awk脚本进行数据校验；当外部关键服务异常时，借助redis设置全局重试标示，将请求写入外部异常队列进行重试，补充并完善Jenkins编译使用sh脚本，优化代码构建流程，技术研究

背景：

百信银行现支持联贷、联营、自营等业务。联贷现阶段主要依靠与百度合作，借助百度流量接受用户授信请求。用户用信时，双方按一定比例出资，完成用户用信操作。联营与去哪儿合作，借助去哪儿用户流量，增加自己贷款客户量积累。自营是通过自己宣传推广，用户使用自己APP端进行授信用信

技术架构
百信风控及额度中心以及其他模块应用有很好的横向扩展能力。应用层面采用百度分布式架构，使用金融网关，进行各个服务的注册与发布，利于服务的接入；持久层面，采用XML配置分表策略方式，对数据库MySql数据库进行分表操作，预防后期的数据库成为瓶颈，并对常用变量进行Redis缓存进一步提高系统性能。

风控系统采用Activiti工作流框架，控制消费信贷核心传递到风控系统的授信请求流程，各个系统之间采用ActivityMQ消息队列进行请求消息传递，避免交易高峰对系统的压力。

在进行对第三方参数查询时，使用多线程同步查询多个外部变量提高系统反馈性能，并且同一用户查询时，首先检查redis缓存。

联贷：启明星、大额
联营：去哪儿
自营：天狼星

一级：质押、抵押、信用
信用下：消费分期、现金分期、信用支付

循环额度、非循环额度

数据库刷新规则：取产品最大额度作为总额度，三级额度相等
