---
title: ASP.NET Core SignalR Java 客户端
author: mikaelm12
description: 了解如何使用 ASP.NET Core SignalR Java 客户端。
monikerRange: '>= aspnetcore-2.2'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/java-client
ms.openlocfilehash: 0354821694d3f3422458c1986749fb9cc25d6276
ms.sourcegitcommit: 68df2a4d236f9f3299622ed38c75bb51cbdb4856
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2021
ms.locfileid: "107225548"
---
# <a name="aspnet-core-signalr-java-client"></a>ASP.NET Core SignalR Java 客户端

作者：[Mikael Mengistu](https://twitter.com/MikaelM_12)

Java 客户端允许 SignalR 从 java 代码（包括 Android 应用）连接到 ASP.NET Core 服务器。 与 [JavaScript 客户端](xref:signalr/javascript-client) 和 [.net 客户](xref:signalr/dotnet-client)端一样，Java 客户端允许您实时接收消息并向中心发送消息。 Java 客户端在 ASP.NET Core 2.2 及更高版本中可用。

本文中引用的示例 Java 控制台应用使用 SignalR java 客户端。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/main/aspnetcore/signalr/java-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="install-the-signalr-java-client-package"></a>安装 SignalR Java 客户端包

*Signalr-1.0.0* JAR 文件允许客户端连接到 SignalR 中心。 若要查找最新的 JAR 文件版本号，请参阅 [Maven 搜索结果](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr)。

如果使用 Gradle，请将以下行添加到 `dependencies` *Gradle* 文件的部分：

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

如果使用的是 Maven，请在pom.xml文件的元素中添加以下行 `<dependencies>` ： 

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a>连接到集线器

若要建立 `HubConnection` ， `HubConnectionBuilder` 应使用。 在建立连接时，可以配置中心 URL 和日志级别。 在之前调用任何方法，配置任何所需的选项 `HubConnectionBuilder` `build` 。 启动与的连接 `start` 。

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a>从客户端调用集线器方法

调用以调用 `send` 集线器方法。 将 hub 方法名称和在 hub 方法中定义的任何自变量传递给 `send` 。

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> 仅 SignalR 在 *默认* 模式下使用 Azure 服务时，才支持从客户端调用中心方法。 有关详细信息，请参阅 [Signalr GitHub 存储库)  (常见问题解答 ](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)。

## <a name="call-client-methods-from-hub"></a>从中心调用客户端方法

用于 `hubConnection.on` 在客户端上定义中心可调用的方法。 在生成之后但在开始连接之前定义方法。

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a>添加日志记录

SignalRJava 客户端使用[SLF4J](https://www.slf4j.org/)库进行日志记录。 这是一个高级日志记录 API，它允许库的用户通过引入特定的日志记录依赖项来选择自己的特定日志记录实现。 下面的代码段演示如何将用于 `java.util.logging` SignalR Java 客户端。

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

如果未在依赖项中配置日志记录，SLF4J 将加载默认的非操作记录器，并提供以下警告消息：

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

可以安全地忽略此情况。

## <a name="android-development-notes"></a>Android 开发说明

对于客户端功能 Android SDK 兼容性 SignalR ，在指定目标 Android SDK 版本时，请考虑以下各项：

* SignalRJava 客户端将在 ANDROID API Level 16 和更高版本上运行。
* 通过 Azure 服务连接 SignalR 将需要 ANDROID API 级别20和更高版本，因为 [Azure SignalR 服务](/azure/azure-signalr/signalr-overview) 需要 TLS 1.2，并且不支持基于 sha-1 的密码套件。 Android [增加了对 SHA-256 (及更) 高](https://developer.android.com/reference/javax/net/ssl/SSLSocket) 版本的支持。

## <a name="configure-bearer-token-authentication"></a>配置持有者令牌身份验证

在 SignalR Java 客户端中，可以通过向 [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr.httphubconnectionbuilder?view=aspnet-signalr-java)提供 "访问令牌工厂" 来配置用于身份验证的持有者令牌。 使用[withAccessTokenFactory](/java/api/com.microsoft.signalr.httphubconnectionbuilder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html)。 如果调用了 [单延迟](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，你可以编写逻辑来为客户端生成访问令牌。

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

::: moniker range=">= aspnetcore-5.0"

### <a name="passing-class-information-in-java"></a>在 Java 中传递类信息

`on` `invoke` `stream` 在 Java 客户端中调用的、或方法时 `HubConnection` ，用户应传递 `Type` 对象而不是 `Class<?>` 对象来描述 `Object` 传递给方法的任何泛型。 `Type`可以使用提供的类来获取 `TypeReference` 。 例如，使用名为的自定义泛型类 `Foo<T>` ，下面的代码获取 `Type` ：

```java
Type fooType = new TypeReference<Foo<String>>() { }).getType();
```

对于非泛型，如基元或其他非参数化类型（如 `String` ），只需使用内置 `.class` 。

使用一个或多个对象类型调用这些方法之一时，请在调用方法时使用泛型语法。 例如，当注册 `on` 一个名为的方法 `func` （该方法采用字符串和对象作为参数）时，请 `Foo<String>` 使用以下代码设置一个操作来打印参数：

```java
hubConnection.<String, Foo<String>>on("func", (param1, param2) ->{
    System.out.println(param1);
    System.out.println(param2);
}, String.class, fooType);
```

此约定是必需的，因为我们无法通过 `Object.getClass` Java 中的类型擦除来检索有关复杂类型的完整信息。 例如， `getClass` 对的调用 `ArrayList<String>` 将不会返回 `Class<ArrayList<String>>` ，而是不会 `Class<ArrayList>` 为反序列化程序提供足够的信息来正确地反序列化传入消息。 对于自定义对象也是如此。

::: moniker-end

## <a name="known-limitations"></a>已知的限制

::: moniker range=">= aspnetcore-5.0"

* 传输回退和服务器发送事件传输不受支持。

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

* 传输回退和服务器发送事件传输不受支持。
* 仅支持 JSON 协议。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* 仅支持 JSON 协议。
* 仅支持 Websocket 传输。
* 目前尚不支持流式处理。

::: moniker-end

## <a name="additional-resources"></a>其他资源

* [Java API 参考](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [Azure SignalR Service 无服务器文档](/azure/azure-signalr/signalr-concept-serverless-development-config)
