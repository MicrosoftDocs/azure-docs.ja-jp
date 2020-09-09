---
title: コードを変更せずにアプリを監視する - Azure Monitor Application Insights の自動インストルメンテーション |Microsoft Docs
description: Azure Monitor Application Insights の自動インストルメンテーションの概要 - コード不要のアプリケーションのパフォーマンス管理
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 234f772b4a77b780f0b0c1faf307c93847e5caf2
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87310720"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Azure Monitor Application Insights の自動インストルメンテーションまたはコードなしアタッチとは

自動インストルメンテーション (コードなしアタッチ) を使用すると、コードを変更することなく Application Insights を使用したアプリケーションの監視を有効にすることができます。  

Application Insights は、さまざまなリソース プロバイダーと統合されており、さまざまな環境で動作します。 基本的には、エージェントを有効にし、場合によっては構成するだけで済みます、これにより、すぐに使用できるテレメトリが収集されます。 すぐに、Application Insights リソースにメトリック、データ、依存関係が表示されます。これにより、潜在的な問題が発生する前に原因を特定し、エンドツーエンドのトランザクション ビューを使用して根本原因を分析することができます。

## <a name="supported-environments-languages-and-resource-providers"></a>サポートされている環境、言語、リソース プロバイダー

統合が多く追加されるほど、自動インストルメンテーション機能のマトリックスは複雑になります。 次の表は、さまざまなリソース プロバイダー、言語、環境のサポートに関して、現在の状態を示しています。

|環境/リソース プロバイダー | .NET            | .NET Core       | Java            | Node.js         |
|------------------------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service on Windows  | GA、OnBD*       | GA、オプトイン      | プライベート プレビュー | プライベート プレビュー |
|Azure App Service on Linux    | 該当なし             | サポートされていません   | パブリック プレビュー  | パブリック プレビュー  |
|Azure App Service on AKS      | 該当なし             | 設計中       | 設計中       | 設計中       |
|Azure Functions - 基本       | GA、OnBD*       | GA、OnBD*       | GA、OnBD*       | GA、OnBD*       |
|Azure Functions - 依存関係| サポートされていません   | サポートされていません   | パブリック プレビュー  | サポートされていません   |
|Azure Kubernetes Service      | 該当なし             | 設計中       | エージェント経由   | 設計中       |
|Azure VM Windows             | パブリック プレビュー  | サポートされていません   | サポートされていません   | サポートされていません   |
|オンプレミスの VM Windows       | GA、オプトイン      | サポートされていません   | エージェント経由   | サポートされていません   |
|スタンドアロン エージェント - 任意の環境   | サポートされていません   | サポートされていません   | パブリック プレビュー  | サポートされていません   |

*OnBD は On by Default (既定でオン) の短縮形です。サポートされている環境でアプリをデプロイすると、Application Insights が自動的に有効になります。 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

[Azure App Service でのアプリケーションの監視](./azure-web-apps.md?tabs=net)は、.NET アプリケーションで使用でき、既定で有効になっています。.NET Core は 1 回のクリックで有効にできます。Java と Node.js はプライベート プレビュー段階にあります。

### <a name="linux"></a>Linux 

App Service での Java および Node.js アプリケーションの監視はパブリック プレビュー段階であり、Azure portal で有効にすることができ、すべてのリージョンで利用可能です。

## <a name="azure-functions"></a>Azure Functions

Azure Functions の基本的な監視は、ログ、パフォーマンス、エラー データ、HTTP 要求を収集するために、既定で有効になっています。 Java アプリケーションでは、分散トレースを使用した高度な監視を有効にし、エンドツーエンドのトランザクションの詳細を取得することができます。 Java 用のこの機能はパブリック プレビュー段階であり、[Azure portal で有効にする](./monitor-functions.md)ことができます。

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Azure Kubernetes Service のコード不要のインストルメンテーションは、現在、[スタンドアロン エージェント](./java-in-process-agent.md)を介して Java アプリケーションで利用できます。 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Azure Windows VM と仮想マシン スケール セット

[Azure VM と仮想マシン スケール セットの自動インストルメンテーション](./azure-vm-vmss-apps.md)は、.NET アプリケーション用に使用できます。 

## <a name="on-premises-servers"></a>オンプレミスのサーバー
[.NET アプリケーション](./status-monitor-v2-overview.md)用および [Java アプリ](./java-in-process-agent.md)用のオンプレミスの Windows サーバーの監視を、簡単に有効にすることができます。

## <a name="other-environments"></a>その他の環境
多用途の Java スタンドアロン エージェントはあらゆる環境で動作しますが、コードをインストルメント化する必要はありません。 [ガイド](./java-in-process-agent.md)に従って Application Insights を有効にし、Java エージェントのすばらしい機能について確認してください。 エージェントはパブリック プレビュー段階にあり、すべてのリージョンで利用できます。 

## <a name="next-steps"></a>次のステップ

* [Application Insights の概要](./app-insights-overview.md)
* [アプリケーション マップ](./app-map.md)
* [エンドツーエンドのパフォーマンスの監視](../learn/tutorial-performance.md)

