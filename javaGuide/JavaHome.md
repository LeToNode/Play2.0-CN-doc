# 使用Java开发 Play 2.0 应用

Java版本的Play 2.0 API在`play` 包下. 

> `play.api` 包 (如 `play.api.mvc`) 下的API是为 Scala 开发者预留的. 作为一个Java开发者, 可以关注 `play.mvc`.

## 目录

1. [[HTTP 开发 | JavaActions]]
    1. [[Action, Controller 和 Result | JavaActions]]
    1. [[HTTP 路由 | JavaRouting]]
    1. [[操作 HTTP 响应 | JavaResponse]]
    1. [[Session 和 Flash 作用域 | JavaSessionFlash]]
    1. [[内容处理 | JavaBodyParsers]]
    1. [[Action 构成 | JavaActionsComposition]]
1. [[异步 HTTP 开发 | JavaAsync]]
    1. [[处理异步结果 | JavaAsync]]
    1. [[HTTP响应流 | JavaStream]]
    1. [[Comet 套接字 | JavaComet]]
    1. [[WebSockets | JavaWebSockets]]
1. [[模版引擎 | JavaTemplates]]
    1. [[模版语法 | JavaTemplates]]
    1. [[常用用例 | JavaTemplateUseCases]]
1. [[HTTP表单提交和验证 | JavaForms]]
    1. [[Form 定义 | JavaForms]]
    1. [[使用Form Template Helper | JavaFormHelpers]]
1. [[使用 Json | JavaJsonRequests]]
    1. [[处理和响应 Json 请求 | JavaJsonRequests]]
1. [[使用 XML | JavaXmlRequests]]
    1. [[处理和响应 XML 请求 | JavaXmlRequests]]
1. [[文件上传 | JavaFileUpload]]
    1. [[直接上传和 multipart/form-data | JavaFileUpload]]
1. [[访问SQL数据库 | JavaDatabase]]
    1. [[配置和使用 JDBC | JavaDatabase]]
    1. [[使用Ebean ORM | JavaEbean]]
    1. [集成 JPA | JavaJPA]]
1. [[缓存 | JavaCache]]
    1. [[Play cache API | JavaCache]]
1. [[访问WebService | JavaWS]]
    1. [[Play WS API  | JavaWS]]
    1. [[连接到OpenID服务器 | JavaOpenID]]
    1. [[访问OAuth控制下的资源 | JavaOauth]]
1. [[集成Akka | JavaAkka]]
    1. [[Setting up Actors and scheduling asynchronous tasks | JavaAkka]]
1. [[国际化 | JavaI18N]]
    1. [[Messages externalization and i18n | JavaI18N]]
1. [[全局application对象 | JavaGlobal]]
    1. [[全局配置 | JavaGlobal]]
    1. [[请求拦截器 | JavaInterceptors]]
1. [[测试 | JavaTest]]
    1. [[编写测试用例 | JavaTest]]
    1. [[编写功能性测试 | JavaFunctionalTest]]

## 教程

1. [[第一个应用 | JavaTodoList]]