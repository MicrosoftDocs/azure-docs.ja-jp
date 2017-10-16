---
title: "複数のコンポーネント、マイクロサービス、コンテナーに対する Azure Application Insights のサポート | Microsoft Docs"
description: "複数のコンポーネントまたはロールから成るアプリのパフォーマンスと使用状況を監視。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/17/2017
ms.author: bwren
ms.openlocfilehash: ca1bb8ee886c4b4e69be9dd653d6a52b874e1f5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-multi-component-applications-with-application-insights-preview"></a>Application Insights (プレビュー) でマルチコンポーネント アプリケーションを監視する

複数のサーバー コンポーネント、ロール、またはサービスからなるアプリを、[Azure Application Insights](app-insights-overview.md) で監視できます。 コンポーネントの正常性とコンポーネント間の関係は、単一のアプリケーション マップに表示されます。 複数のコンポーネントによる個々の操作を、自動 HTTP 関連付けでトレースできます。 コンテナー診断は、アプリケーション テレメトリに統合し、関連付けることができます。 アプリケーションのすべてのコンポーネントに対して、単一の Application Insights リソースを使用します。 

![マルチコンポーネントのアプリケーション マップ](./media/app-insights-monitor-multi-role-apps/app-map.png)

ここでは、"コンポーネント" は大きなアプリケーションの各機能部分を意味しています。 たとえば、典型的なビジネス アプリケーションは、Web ブラウザーで動作するクライアント コードからなり、そのコードは 1 つ以上の Web アプリケーション サービスとやりとりし、さらにそのサービスはバックエンド サービスを使用します。 サーバー コンポーネントは、クラウドでホストされるオンプレミスであることもあれば、Azure Web と worker ロールであることもあり、Docker や Service Fabric などのコンテナーで実行されることもあります。 

### <a name="sharing-a-single-application-insights-resource"></a>単一の Application Insights リソースの共有 

ここでの主要なテクニックは、アプリケーションのすべてのコンポーネントから同じ Application Insights リソースにテレメトリを送信し、必要であれば `cloud_RoleName` プロパティを使用してロールを区別することです。 Application Insights SDK は、`cloud_RoleName` プロパティをテレメトリ コンポーネントの出力に追加します。 たとえば、Web サイト名またはサービス ロール名を `cloud_RoleName` プロパティに追加します。 この値は、telemetryinitializer でオーバーライドできます。 アプリケーション マップは、`cloud_RoleName` プロパティを使用してマップ上のコンポーネントを識別します。

`cloud_RoleName` プロパティをオーバーライドする方法については、「[プロパティの追加: ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer)」を参照してください。  

一部のケースでは、この方法が適切ではなく、コンポーネントのグループごとに個別のリソースを使用する方がよい場合があります。 たとえば、管理や課金のために、異なるリソースを使用しなければならないことがあります。 個別のリソースを使用する場合は、すべてのコンポーネントが単一のアプリケーション マップには表示されず、[Analytics](app-insights-analytics.md) で複数のコンポーネントにわたるクエリを実行することができません。 また、リソースごとにセットアップを行う必要があります。

そのことを考慮して、このドキュメントの残りの部分では、複数のコンポーネントから 1 つの Application Insights リソースにデータを送信することを想定しています。

## <a name="configure-multi-component-applications"></a>マルチコンポーネント アプリケーションを構成する

マルチコンポーネント アプリケーション マップを取得するには、以下の目標を達成する必要があります。

* アプリケーションの各コンポーネントに、Application Insights パッケージの**最新のプレリリースをインストール**します。 
* アプリケーションのすべてのコンポーネントで、**単一の Application Insights リソースを共有**します。
* [プレビュー] ブレードで、**マルチロール アプリケーション マップを有効**にします。

アプリケーションの各コンポーネントを、その種類に適した方法で構成します。 ([ASP.NET](app-insights-asp-net.md)、[Java](app-insights-java-get-started.md)、[Node.js](app-insights-nodejs.md)、[JavaScript](app-insights-javascript.md)。)

### <a name="1-install-the-latest-pre-release-package"></a>1.最新のプレリリース パッケージをインストールする

各サーバー コンポーネントのプロジェクトで、Appication Insights パッケージを更新するかインストールします。 Visual Studio を使用している場合:

1. プロジェクトを右クリックし、**[NuGet パッケージの管理]** を選択します。 
2. **[プレリリースを含める]** を選択します。
3. Application Insights パッケージが [更新] に表示されたら、それを選択します。 

    表示されない場合は、以下の適切なパッケージを参照してインストールします。
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric - ゲスト実行可能ファイルとして動作しているコンテナーや Service Fabric アプリケーションで実行されている Docker コンテナーの場合
    * Microsoft.ApplicationInsights.ServiceFabric.Native - ServiceFabric アプリケーションのリライアブル サービスの場合
    * Microsoft.ApplicationInsights.Kubernetes - Kubernetes 上の Docker で実行されているコンポーネントの場合

### <a name="2-share-a-single-application-insights-resource"></a>2.単一の Application Insights リソースを共有する

* Visual Studio でプロジェクトを右クリックして **[Application Insights の構成]** を選択するか、**[Application Insights] > [構成]** の順に選択します。 最初のプロジェクトでは、ウィザードを使用して Application Insights リソースを作成します。 その後のプロジェクトでは、同じリソースを選択します。
* [Application Insights] メニューがない場合は、次のようにして手動で構成します。

   1. [Azure Portal](https://portal,azure.com) で、他のコンポーネント用に既に作成した Application Insights リソースを開きます。
   2. [概要] ブレードで [要点] ドロップダウン タブを開き、**インストルメンテーション キー**をコピーします。
   3. プロジェクトで ApplicationInsights.config を開き、`<InstrumentationKey>your copied key</InstrumentationKey>` を挿入します。

![インストルメンテーション キーを .config ファイルにコピーする](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-multi-role-application-map"></a>3.マルチロール アプリケーション マップを有効にする

Azure Portal でアプリケーションのリソースを開きます。 [プレビュー] ブレードで "*マルチロール アプリケーション マップ*" を有効にします。

### <a name="4-enable-docker-metrics-optional"></a>4.Docker メトリックを有効にします (省略可能) 

Azure Windows VM でホストされている Docker でコンポーネントが実行されている場合は、コンテナーから追加のメトリックを収集できます。 [Azure 診断](../monitoring-and-diagnostics/azure-diagnostics.md)構成ファイルに次のコードを挿入します。

```
"DiagnosticMonitorConfiguration": {
        ...
        "sinks": "applicationInsights",
        "DockerSources": {
                "Stats": {
                    "enabled": true,
                    "sampleRate": "PT1M"
                }
            },
        ...
    }
    ...   
    "SinksConfig": {
        "Sink": [{
            "name": "applicationInsights",
            "ApplicationInsights": "<your instrumentation key here>"
        }]
    }
    ...
}

```

## <a name="use-cloudrolename-to-separate-components"></a>cloud_RoleName を使用してコンポーネントを区別する

`cloud_RoleName` プロパティは、すべてのテレメトリに関連付けられます。 このプロパティで、テレメトリを生成したコンポーネント (ロールまたはサービス) を識別できます。 (cloud_RoleInstance とは異なります。このプロパティは、複数のサーバー プロセスやマシンで並列して実行されている同じロールを区別するものです)。

ポータルでこのプロパティを使用して、テレメトリをフィルター処理したりセグメント化したりすることができます。 この例では [障害] ブレードがフィルター処理され、フロントエンド Web サービスからの情報だけが表示されます。CRM API バックエンドの障害は除外されます。

![クラウド ロール名によってセグメント化されたメトリック グラフ](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-components"></a>コンポーネント間の操作をトレースする

個々の操作の処理中に行われた、1 つのコンポーネントから別のコンポーネントへの呼び出しをトレースすることができます。


![操作のテレメトリを表示する](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

クリックして、フロントエンド Web サーバーとバックエンド API を対象に、この操作に関連するテレメトリの一覧を表示します。

![コンポーネント間の検索](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>次のステップ

* [開発、テスト、および運用のテレメトリを分離する](app-insights-separate-resources.md)
