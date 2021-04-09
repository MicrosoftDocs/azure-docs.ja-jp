---
title: コードを変更せずにアプリを監視する - Azure Monitor Application Insights の自動インストルメンテーション |Microsoft Docs
description: Azure Monitor Application Insights の自動インストルメンテーションの概要 - コード不要のアプリケーションのパフォーマンス管理
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 9ead123338a410daf53569ff577dfc8c728a8ddf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708494"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Azure Monitor Application Insights の自動インストルメンテーションまたはコードなしアタッチとは

自動インストルメンテーション (コードなしアタッチ) を使用すると、コードを変更することなく Application Insights を使用したアプリケーションの監視を有効にすることができます。  

Application Insights は、さまざまなリソース プロバイダーと統合されており、さまざまな環境で動作します。 基本的には、エージェントを有効にし、場合によっては構成するだけで済みます。これによりテレメトリは自動的にボックスに収集されます。 すぐに、Application Insights リソースにメトリック、データ、依存関係が表示されます。これにより、潜在的な問題が発生する前に原因を特定し、エンドツーエンドのトランザクション ビューを使用して根本原因を分析することができます。

## <a name="supported-environments-languages-and-resource-providers"></a>サポートされている環境、言語、リソース プロバイダー

新たな統合を追加していくと、自動インストルメンテーション機能のマトリックスは複雑になります。 次の表は、さまざまなリソース プロバイダー、言語、環境のサポートに関して、現在の状態を示しています。

|環境/リソース プロバイダー          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service on Windows           | GA、OnBD*       | GA、オプトイン      | 進行中     | 進行中     | サポートされていません   |
|Azure App Service on Linux             | 該当なし             | サポートされていません   | 進行中     | パブリック プレビュー  | サポートされていません   |
|Azure App Service on AKS               | 該当なし             | 設計中       | 設計中       | 設計中       | サポートされていません   |
|Azure Functions - 基本                | GA、OnBD*       | GA、OnBD*       | GA、OnBD*       | GA、OnBD*       | GA、OnBD*       |
|Azure Functions Windows - 依存関係 | サポートされていません   | サポートされていません   | パブリック プレビュー  | サポートされていません   | サポートされていません   |
|Azure Kubernetes Service               | 該当なし             | 設計中       | エージェント経由   | 設計中       | サポートされていません   |
|Azure VM Windows                      | パブリック プレビュー  | サポートされていません   | サポートされていません   | サポートされていません   | サポートされていません   |
|オンプレミスの VM Windows                | GA、オプトイン      | サポートされていません   | エージェント経由   | サポートされていません   | サポートされていません   |
|スタンドアロン エージェント - 任意の環境            | サポートされていません   | サポートされていません   | GA              | サポートされていません   | サポートされていません   |

*OnBD は On by Default (既定でオン) の短縮形です。サポートされている環境でアプリをデプロイすると、Application Insights が自動的に有効になります。 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

#### <a name="net"></a>.NET
Azure App Service on Windows でのアプリケーション監視は [.NET アプリケーション](./azure-web-apps.md?tabs=net) .NET で使用可能であり、既定で有効です。

#### <a name="netcore"></a>.NETCore
[.NETCore アプリケーション](./azure-web-apps.md?tabs=netcore)の監視をワンクリックで有効にできます。

#### <a name="java"></a>Java
App Service on Windows での Java アプリケーション監視のためのポータル統合は現在利用できませんが、アプリを App Service にデプロイする前にコードに変更を加えることなく、Application Insights [Java 3.0 スタンドアロン エージェント](./java-in-process-agent.md)をアプリケーションに追加できます。 Application Insights Java 3.0 エージェントは一般提供されています。

#### <a name="nodejs"></a>Node.js
現時点では Windows 上の Node.js アプリケーションの監視をポータルから有効にすることはできません。 Node.js アプリケーションを監視するには、[SDK](./nodejs.md) を使用します。

### <a name="linux"></a>Linux

#### <a name="netcore"></a>.NETCore
Linux で実行中の .NETCore アプリケーションを監視するには、[SDK](./asp-net-core.md) を使用します。

#### <a name="java"></a>Java 
App Service on Linux での Java アプリケーション監視をポータルから有効にすることはできませんが、アプリを App Service にデプロイする前に、[Application Insights Java 3.0 エージェント](./java-in-process-agent.md)をアプリに追加できます。 Application Insights Java 3.0 エージェントは一般提供されています。

#### <a name="nodejs"></a>Node.js
[App Service on Linux での Node.js アプリケーションの監視](./azure-web-apps.md?tabs=nodejs)はパブリック プレビュー段階であり、Azure portal で有効にすることができ、すべてのリージョンで利用可能です。 

#### <a name="python"></a>Python
SDK を使用して [Python アプリを監視します](./opencensus-python.md) 

## <a name="azure-functions"></a>Azure Functions

Azure Functions の基本的な監視は、ログ、パフォーマンス、エラー データ、HTTP 要求を収集するために、既定で有効になっています。 Java アプリケーションでは、分散トレースを使用した高度な監視を有効にし、エンドツーエンドのトランザクションの詳細を取得することができます。 Java 用のこの機能はパブリック プレビュー段階であり、[Azure portal で有効にする](./monitor-functions.md)ことができます。

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes Service のコード不要のインストルメンテーションは、現在、[スタンドアロン エージェント](./java-in-process-agent.md)を介して Java アプリケーションで利用できます。 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure Windows VM と仮想マシン スケール セット

Azure VM と仮想マシン スケール セットの自動インストルメンテーションを [.NET](./azure-vm-vmss-apps.md) および [Java](./java-in-process-agent.md) で使用できます。  

## <a name="on-premises-servers"></a>オンプレミスのサーバー
[.NET アプリケーション](./status-monitor-v2-overview.md)用および [Java アプリ](./java-in-process-agent.md)用のオンプレミスの Windows サーバーの監視を、簡単に有効にすることができます。

## <a name="other-environments"></a>その他の環境
多用途の Java スタンドアロン エージェントはあらゆる環境で動作しますが、コードをインストルメント化する必要はありません。 [ガイド](./java-in-process-agent.md)に従って Application Insights を有効にし、Java エージェントのすばらしい機能について確認してください。 エージェントはパブリック プレビュー段階にあり、すべてのリージョンで利用できます。 

## <a name="next-steps"></a>次のステップ

* [Application Insights の概要](./app-insights-overview.md)
* [アプリケーション マップ](./app-map.md)
* [エンドツーエンドのパフォーマンスの監視](../app/tutorial-performance.md)