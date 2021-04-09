---
title: Azure App Configuration の回復性とディザスター リカバリー
description: Azure App Configuration を使用して回復性とディザスター リカバリーを実装する方法を説明します。
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 7910e2092259081aade799fc662052e5a1375e25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930485"
---
# <a name="resiliency-and-disaster-recovery"></a>回復性とディザスター リカバリー

現在、Azure App Configuration はリージョン サービスです。 各構成ストアは特定の Azure リージョンに作成されます。 リージョン全体が停止した場合、そのリージョン内のすべてのストアが影響を受けます。 App Configuration では、別のリージョンへの自動フェールオーバーは提供されていません。 この記事では、Azure リージョンの枠を超えて複数の構成ストアを使用し、アプリケーションの地理的な回復性を向上させる方法についての一般的なガイダンスを提供します。

## <a name="high-availability-architecture"></a>高可用性アーキテクチャ

複数のリージョンにわたる冗長性を実現するには、複数のリージョンで複数の App Configuration ストアを作成する必要があります。 このセットアップにより、ご利用のアプリケーションには、プライマリ ストアにアクセスできなくなった場合のフォールバック先として少なくとも 1 つの構成ストアが追加で用意されます。 次の図は、ご利用のアプリケーションとそのプライマリおよびセカンダリ構成ストアの間のトポロジを示したものです。

![geo 冗長ストア](./media/geo-redundant-app-configuration-stores.png)

ご利用のアプリケーションでは、その構成がプライマリとセカンダリの両方のストアから読み込まれます。 これを実施すると、構成データを正常に取得できる可能性が高くなります。 両方のストア内のデータの同期状態は、ご自身で維持する必要があります。以降のセクションでは、アプリケーションに地理的な回復性を組み込む方法について説明します。

## <a name="failover-between-configuration-stores"></a>構成ストア間のフェールオーバー

技術的には、ご利用のアプリケーションでフェールオーバーは実行されていません。 2 つの App Configuration ストアから、同じ構成データのセットを同時に取得することが試みられています。 最初にセカンダリ ストアから、次にプライマリ ストアから読み込みを行うようにご自分のコードを調整します。 このアプローチにより、プライマリ ストア内の構成データが使用可能なときは常にそちらが確実に優先されます。 次のコード スニペットは、.NET Core でのこの配置の実装方法を示しています。

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                  .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
        })
        .UseStartup<Startup>();
    
```

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionString_SecondaryStore"], optional: true)
                    .AddAzureAppConfiguration(settings["ConnectionString_PrimaryStore"], optional: true);
            })
            .UseStartup<Startup>());
```
---

`optional` パラメーターが `AddAzureAppConfiguration` 関数に渡されていることに注目してください。 このパラメーターを `true` に設定すると、関数が構成データを読み込めなかった場合にアプリケーションが続行できなくなるのを防げます。

## <a name="synchronization-between-configuration-stores"></a>構成ストア間の同期

すべての geo 冗長構成ストアに同じデータ セットが存在していることが重要です。 これを実現する方法は 2 つあります。

### <a name="backup-manually-using-the-export-function"></a>エクスポート機能を使用して手動でバックアップする

App Configuration の **エクスポート** 機能を使用することで、プライマリ ストアからセカンダリにオンデマンドでデータをコピーできます。 この機能は Azure portal と CLI の両方で使用できます。

Azure portal からは、次の手順に従って、別の構成ストアに変更をプッシュできます。

1. **[Import/Export]** タブに移動し、 **[エクスポート]**  >  **[App Configuration]**  >  **[ターゲット]**  >  **[リソースの選択]** の順に選択します。

1. 開いた新しいブレードで、ご利用のセカンダリ ストアのサブスクリプション、リソース グループ、リソース名を指定して、 **[適用]** を選択します。

1. UI が更新され、そのセカンダリ ストアにエクスポートする構成データを選択できるようになります。 時刻値は既定のままにし、 **[元のラベル]** と **[ラベル]** の両方を同じ値に設定できます。 **[適用]** を選択します。 プライマリ ストア内のすべてのラベルに対して、この手順を繰り返します。

1. 構成を変更するたびに、前述の手順を繰り返します。

エクスポート プロセスは Azure CLI を使用して実行することもできます。 次のコマンドは、すべての構成をプライマリ ストアからセカンダリにエクスポートする方法を示しています。

```azurecli
    az appconfig kv export --destination appconfig --name {PrimaryStore} --dest-name {SecondaryStore} --label * --preserve-labels -y
```

### <a name="backup-automatically-using-azure-functions"></a>Azure Functions を使用して自動的にバックアップする

Azure Functions を使用して、バックアップ プロセスを自動化できます。 App Configuration で Azure Event Grid との統合を利用します。 設定が完了すると、構成ストアでキーと値に加えられたすべての変更について、App Configuration から Event Grid にイベントが発行されます。 このようにして、Azure Functions アプリでこれらのイベントをリッスンし、それに応じてデータをバックアップできます。 詳細については、[App Configuration のストアを自動的にバックアップする方法](./howto-backup-config-store.md)に関するチュートリアルを参照してください。

## <a name="next-steps"></a>次のステップ

この記事では、アプリケーションを拡張して App Configuration の実行時に地理的な回復性を実現する方法について説明しました。 ビルド時またはデプロイ時に、App Configuration から構成データを埋め込むこともできます。 詳細については、[「CI/CD パイプラインとの統合」](./integrate-ci-cd-pipeline.md)を参照してください。
