---
title: Azure Application Insights - 依存関係の自動収集 | Microsoft Docs
description: Application Insights では、依存関係が自動的に収集されて視覚化されます。
services: application-insights
documentationcenter: .net
author: nikmd23
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 04/29/2019
ms.reviewer: mbullwin
ms.author: nimolnar
ms.openlocfilehash: 839ab291a99de646053b638520ce43f459d5c41f
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297016"
---
# <a name="dependency-auto-collection"></a>依存関係の自動収集

以下に示したのは、アプリケーションのコードに別途変更を加えなくても、現時点で依存関係として自動的に検出することができる依存関係呼び出しの一覧です。 これらの依存関係は、Application Insights の [[アプリケーション マップ]](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) ビューと [[トランザクション診断]](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) ビューで視覚化されます。 必要な依存関係が以下の一覧にない場合でも、[TrackDependency 呼び出し](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)を使えば手動で追跡することができます。

## <a name="net"></a>.NET

| アプリケーション フレームワーク| バージョン |
| ------------------------|----------|
| ASP.NET Webforms | 4.5 以降 |
| ASP.NET MVC | 4 以降 |
| ASP.NET WebAPI | 4.5 以降 |
| ASP.NET Core | 1.1 以降 |
| <b> 通信ライブラリ</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5 以降、.NET Core 1.1 以降 |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0 以降、NuGet 4.3.0 |
| [EventHubs Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus Client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>ストレージ クライアント</b>|  |
| ADO.NET | 4.5 以降 |

## <a name="java"></a>Java
| アプリケーション サーバー | バージョン |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7、8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6、7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>アプリケーション フレームワーク</b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9 以降<sup>*</sup> |
| Java サーブレット | 3.1 以降 |
| <b>通信ライブラリ</b> |  |
| [Apache Http クライアント](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3 以降<sup>†</sup> |
| <b>ストレージ クライアント</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1 以降<sup>†</sup> |
| [PostgreSQL (ベータ サポート)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1 以降<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1 以降<sup>†</sup> |
| <b>ログ ライブラリ</b> | |
| [Logback](https://logback.qos.ch/) | 1 以降 |
| [Log4j](https://logging.apache.org/log4j/) | 1.2 以降 |
| <b>メトリック ライブラリ</b> |  |
| JMX | 1.0 以降 |

> [!NOTE]
> *リアクティブ プログラミング サポートを除く。
> <br>†[JVM エージェント](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java)のインストールが必要。

## <a name="nodejs"></a>Node.js

| 通信ライブラリ | バージョン |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html)、[HTTPS](https://nodejs.org/api/https.html) | 0.10 以降 |
| <b>ストレージ クライアント</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb)、[MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2.x から 3.x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 から 2.16.x |
| [PostgreSql](https://www.npmjs.com/package/pg) | 6.x から 7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x から 2.x |
| <b>ログ ライブラリ</b> | |
| [console](https://nodejs.org/api/console.html) | 0.10 以降 |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x から 3.x |

## <a name="javascript"></a>JavaScript

| 通信ライブラリ | バージョン |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | All |

## <a name="next-steps"></a>次の手順

- [.NET](../../azure-monitor/app/asp-net-dependencies.md) のカスタム依存関係追跡を設定します。
- [Java](../../azure-monitor/app/java-agent.md) のカスタム依存関係追跡を設定します。
- [カスタム依存関係テレメトリを記述します](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)。
- Application Insights の型とデータ モデルについては、[データ モデル](../../azure-monitor/app/data-model.md)に関するページを参照してください。
- Application Insights でサポートされている[プラットフォーム](../../azure-monitor/app/platforms.md)を確認します。
