---
title: Azure App Configuration の回復性とディザスター リカバリー | Microsoft Docs
description: Azure App Configuration を使用して回復性とディザスター リカバリーを実装する方法の概要。
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 05/29/2019
ms.author: yegu
ms.openlocfilehash: c05957cda16c96b841433483a90429aab2b4d22d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706503"
---
# <a name="resiliency-and-disaster-recovery"></a>回復性とディザスター リカバリー

現在、Azure App Configuration はリージョン サービスです。 各構成ストアは特定の Azure リージョンに作成されます。 リージョン全体が停止した場合、そのリージョン内のすべてのストアが影響を受けます。 App Configuration では、別のリージョンへの自動フェールオーバーは提供されていません。 この記事では、Azure リージョンの枠を超えて複数の構成ストアを使用し、アプリケーションの地理的な回復性を向上させる方法についての一般的なガイダンスを提供します。

## <a name="high-availability-architecture"></a>高可用性アーキテクチャ

複数のリージョンにわたる冗長性を実現するには、複数のリージョンで複数のアプリ構成ストアを作成する必要があります。 このセットアップにより、ご利用のアプリケーションには、プライマリ ストアにアクセスできなくなった場合のフォールバック先として少なくとも 1 つの構成ストアが追加で用意されます。 次の図は、ご利用のアプリケーションとそのプライマリおよびセカンダリ構成ストアの間のトポロジを示したものです。

![geo 冗長ストア](./media/geo-redundant-app-configuration-stores.png)

ご利用のアプリケーションでは、その構成がプライマリとセカンダリの両方のストアから読み込まれます。 これを実施すると、構成データを正常に取得できる可能性が高くなります。 両方のストア内のデータの同期状態は、ご自身で維持する必要があります。以降のセクションでは、アプリケーションに地理的な回復性を組み込む方法について説明します。

## <a name="failover-between-configuration-stores"></a>構成ストア間のフェールオーバー

技術的には、ご利用のアプリケーションでフェールオーバーは実行されていません。 2 つの App Configuration ストアから、同じ構成データのセットを同時に取得することが試みられています。 最初にセカンダリ ストアから、次にプライマリ ストアから読み込みを行うようにご自分のコードを調整します。 このアプローチにより、プライマリ ストア内の構成データが使用可能なときは常にそちらが確実に優先されます。 次のコード スニペットは、.NET Core CLI でのこの配置の実装方法を示しています。

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
    }
```

`optional` パラメーターが `AddAzureAppConfiguration` 関数に渡されていることに注目してください。 このパラメーターを `true` に設定すると、関数が構成データを読み込めなかった場合にアプリケーションが続行できなくなるのを防げます。

## <a name="synchronization-between-configuration-stores"></a>構成ストア間の同期

すべての geo 冗長構成ストアに同じデータ セットが存在していることが重要です。 App Configuration の**エクスポート**機能を使用することで、プライマリ ストアからセカンダリにオンデマンドでデータをコピーできます。 この機能は Azure portal と CLI の両方で使用できます。

Azure portal からは、次の手順に従って、別の構成ストアに変更をプッシュできます。

1. **[Import/Export]** タブに移動し、 **[エクスポート]**  >  **[App Configuration]**  >  **[ターゲット]**  >  **[リソースの選択]** の順に選択します。

2. 開いている新しいブレードで、ご利用のセカンダリ ストアのサブスクリプション、リソース グループ、リソース名を指定して、 **[適用]** を選択します。

3. UI が更新され、そのセカンダリ ストアにエクスポートする構成データを選択できるようになります。 時刻値は既定のままにし、 **[元のラベル]** と **[新しいラベル]** の両方を同じ値に設定できます。 **[適用]** を選択します。

4. すべての構成の変更について、前述の手順を繰り返します。

このエクスポート プロセスを自動化するには、Azure CLI を使用します。 次のコマンドは、1 つの構成変更をプライマリ ストアからセカンダリにエクスポートする方法を示しています。

    az appconfig kv export --destination appconfig --name {PrimaryStore} --label {Label} --dest-name {SecondaryStore} --dest-label {Label}

## <a name="next-steps"></a>次の手順

この記事では、アプリケーションを拡張して App Configuration の実行時に地理的な回復性を実現する方法について説明しました。 ビルド時またはデプロイ時に、App Configuration から構成データを埋め込むこともできます。 詳細については、[「CI/CD パイプラインとの統合」](./integrate-ci-cd-pipeline.md)を参照してください。

