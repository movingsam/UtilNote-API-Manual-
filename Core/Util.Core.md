Util核心类库

这个目录下的文件主要是应用的基础类

定义了应用的DTO:

-   Dto应当继承自RequestBase实现接口IDto

-   RequestBase实现了对参数的验证功能（虚方法可自行override）

-   IDto继承接口IKey/IRequest/IResponse

-   IKey定义了一个string的Id需要在Dto中实现

-   IRequest定义了一个访问抽象 继承了RequestBase无需自行实现

-   Oprations中有包含了所有Dto的数据库查询操作抽象接口

-   IQueryService继承了Oprations中的所有接口

定义了拦截器

-   InterceptorBase一个拦截器的基类

-   ParameterInterceptorBase参数拦截器基类

-   IgnoreAttribute忽略拦截特性

-   NotEmptyAttribute验证字符串不能为空特性继承自参数拦截器并重写相关验证逻辑

-   NotNullAttribute验证不能为null 继承自参数拦截器并重写相关验证逻辑

缓存

-   ICache缓存抽象 对应实现类CacheManager(基于IEasyCachingProvider)

上下文

-   ContextFactory静态上下文工厂类 Create方法会产生一个空上下文
    如果当前不是Web请求的话反之使用Web的上下文

-   IContext上下文抽象接口实现了ISingletonDependency预示着将在程序运行时以单例生命周期被注入到ioc容器中

-   NullContext为空上下文

-   WebContext Web请求的上下文

数据相关

-   DatabaseType数据库类型枚举

-   PersistentEntityBase 持久化对象基类

    -   实现接口IKey\<TKey\> TKey作为主键类型

    -   带泛型使用泛型作为主键反之使用Guid作为主键

    -   重写了Equals方法

    -   获取哈希重写

    -   操作符==重写

        -   主键若为默认值则直接返回false

    -   重写!=取上面重写的==号操作符的相反值

-   PersistentObjectBase 持久化对象基类继承自PersistentEntityBase并带上了Version
    乐观锁

-   TreePersistentObjectBase 树型持久化对象基类

    -   两个泛型一个TKey一个ParentId

    -   不写两个泛型会默认使用Guid作为Id,PartentId

    -   实现接口IParentId\<TParentId\> IPath IEnabled ISortId

    -   实现接口IPath Path为树形结构的当前的路径Util已经将Path的生成写了一个实现在TreeEntityBase中 InitPath()

    -   IEnabled 是否启用接口 属性Enabled为True时开启 False失败

    -   ISortId 是排序时用的字段

-   查询相关内容放在Queries文件夹中

    -   查询条件放在Criterias文件夹中

        -   AndCriteria 查询条件 "与（And）"

            -   实现了抽象类ICriteria查询抽象接口需要带上查询相关实体接口泛型

            -   构造函数需要传入两个Expression<Func<TEntity,bool>>型的参数 再利用Util封装的表达式树静态工具类Extensions.And来拼接两个Lambda

            -   同时ICriteria提供了一个GetPredicate()方法来获取条件 所以这里需要实现 那么需要一个中间变量来存放拼接的Lambda 这里使用Predicate属性来接收并暴露给GetPredicate()方法

    -   DateSegmentCriteria 日期范围过滤条件 - 不包含时间
