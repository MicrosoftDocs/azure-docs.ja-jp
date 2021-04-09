---
title: Azure Kubernetes Service (AKS) 上のアプリケーションを Application Insights で監視する - Azure Monitor | Microsoft Docs
description: Azure Monitor は、Kubernetes で実行されているアプリケーションとシームレスに統合され、アプリケーションの問題を即座に見つけることができます。
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 52bd6d2a98e5126ff2463de1ef99da03ca555567
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87075303"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Kubernetes に対するゼロ インストルメンテーション アプリケーション監視 - Azure Monitor Application Insights

> [!IMPORTANT]
>  現時点では、コードをインストルメント化しなくても、Kubernetes で実行されている Java アプリの監視を有効にすることができます。[Java スタンドアロン エージェント](./java-in-process-agent.md)を使用します。 アプリケーションの監視をシームレスに有効にするソリューションは他の言語でも機能しますが、AKS で実行されているアプリを監視するには以下の SDK を使用します。[ASP.NET Core](./asp-net-core.md)、[ASP.NET](./asp-net.md)、[Node.js](./nodejs.md)、[JavaScript](./javascript.md)、および [Python](./opencensus-python.md)。

## <a name="application-monitoring-without-instrumenting-the-code"></a>コードのインストルメント化を伴わないアプリケーションの監視
現時点では、Java のみで、コードをインストルメント化しなくてもアプリケーションの監視を有効にすることができます。 他の言語でアプリケーションを監視するには、SDK を使用します。 

## <a name="java"></a>Java
Java エージェントは、有効になると、最も広く使用されているライブラリとフレームワークから多数の要求、依存関係、ログ、およびメトリックを自動的に収集します。

Kubernetes アプリやその他の環境で実行されている Java アプリを監視するには、[詳細な手順](./java-in-process-agent.md)に従います。 

## <a name="other-languages"></a>その他の言語

他の言語のアプリケーションでは、現在、以下の SDK を使用することをお勧めしています。
* [ASP.NET Core](./asp-net-core.md)
* [ASP.NET](./asp-net.md)
* [Node.js](./nodejs.md) 
* [JavaScript](./javascript.md)
* [Python](./opencensus-python.md)

## <a name="next-steps"></a>次のステップ

* [Azure Monitor](../overview.md) と [Application Insights](./app-insights-overview.md) の詳細を学習する
* [分散トレース](./distributed-tracing.md)の概要を把握し、[アプリケーション マップ](./app-map.md?tabs=net)を業務で活用する方法を確認する
