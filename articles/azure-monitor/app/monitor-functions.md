---
title: Azure Functions で実行されているアプリケーションを Application Insights - Azure Monitor で監視する | Microsoft Docs
description: Azure Functions で実行されているアプリケーションとシームレスに統合された Azure Monitor を使用して、パフォーマンスを監視し、アプリケーションの問題を即座に見つけることができます。
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 6d790a6f02fdc07609e374639c6e452b9088262e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87024573"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Azure Monitor Application Insights で Azure Functions を監視する

[Azure Functions](../../azure-functions/functions-overview.md) には、関数を監視する Azure Application Insights との統合機能が組み込まれています。 

Application Insights は、ログ、パフォーマンス、エラー データを収集し、パフォーマンスの異常を自動的に検出します。 Application Insights は、問題の診断や関数の使用状況の把握に役立つ強力な分析ツールを備えています。 アプリケーション データを可視化すると、パフォーマンスと使いやすさを継続的に向上させることができます。 ローカル関数アプリ プロジェクトの開発中に Application Insights を使用することもできます。 

必要な Application Insights インストルメンテーションは、Azure Functions に組み込まれています。 唯一必要なのは、関数アプリを Application Insights リソースに接続するための有効なインストルメンテーション キーです。 インストルメンテーション キーは、関数アプリのリソースが Azure で作成されるときに、アプリケーションの設定に追加する必要があります。 関数アプリにまだこのキーがない場合は、手動で設定できます。 詳細については、[Azure Functions の監視](../../azure-functions/functions-monitoring.md?tabs=cmd)に関するページを参照してください。

## <a name="distributed-tracing-for-java-applications-on-windows-public-preview"></a>Windows 上の Java アプリケーションの分散トレース (パブリック プレビュー)

> [!IMPORTANT]
> この機能は現在、Windows 上の Java Azure Functions のパブリック プレビュー段階にあります。Linux 上の Java Azure Functions の分散トレースはサポートされていません。 従量課金プランでは、8 ～ 9 秒のコールド スタートがあります。

アプリケーションが Java で記述されている場合は、関数アプリケーションからの豊富なデータ (要求、依存関係、ログ、メトリックなど) を表示できます。 この追加データによって、エンドツーエンドのトランザクションの表示や診断を行えるほか、アプリケーション マップで多数のトランザクションを集計して、システムの相互作用の状況を示すトポロジ ビューや、平均パフォーマンスとエラー率を確認できます。

このエンドツーエンドの診断とアプリケーション マップによって、トランザクション/要求を個別に可視化できます。 この 2 つの機能を併用すれば、信頼性に関する問題の根本原因やパフォーマンスのボトルネックを要求単位で特定できるようになります。

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Java 関数アプリの分散トレースを有効にする方法

関数アプリの [概要] ブレードに移動し、構成に移動します。 [アプリケーション設定] で、[+ 新しいアプリケーション設定] をクリックします。 次に示す 2 つのアプリケーション設定と値を追加し、左上の [保存] をクリックします。 これで完了です。

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## <a name="next-steps"></a>次の手順

* 「[Azure Functions を監視する](../../azure-functions/functions-monitoring.md)」で監視の手順と詳細情報を確認する
* [分散トレース](./distributed-tracing.md)の概要を確認する
* [アプリケーション マップ](./app-map.md?tabs=net)を業務で活用する方法を確認する
* [Java アプリの要求と依存関係](./java-in-process-agent.md)について確認する
* [Azure Monitor](../overview.md) と [Application Insights](./app-insights-overview.md) の詳細を確認する
