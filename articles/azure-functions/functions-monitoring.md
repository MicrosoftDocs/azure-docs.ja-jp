---
title: "Azure Functions の監視 | Microsoft Docs"
description: "Azure Functions を監視する方法について説明します。"
services: functions
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: 
tags: 
keywords: "Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ"
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/03/2016
ms.author: wesmc
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 3ab6123b6acfdec57f1ca71b404c9e1123d1ff6d
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---

# <a name="monitoring-azure-functions"></a>Azure Functions の監視

## <a name="overview"></a>Overview 


各関数の **[モニター]** タブでは、関数の各実行を確認することができます。

![Azure Functions の [モニター] タブ](./media/functions-monitoring/monitor-tab.png) 

実行をクリックすると、期間、入力データ、エラー、関連するログ ファイルを確認できます。 これは、関数のデバッグおよびパフォーマンス チューニングの際に便利です。

> [!IMPORTANT]
> Azure Functions で[従量課金ホスティング プラン](functions-overview.md#pricing)を使用する場合、Function App の **[監視]** タイルにはデータが表示されません。 これは、プラットフォームがコンピューティング インスタンスを動的にスケーリングおよび管理するためです。 これらのメトリックは、従量課金プランでは意味がありません。 関数アプリの使用状況を監視するには、代わりにこの記事のガイダンスを使用する必要があります。
> 
> 次のスクリーンショットは、例を示しています。
> 
> ![関数の監視](./media/functions-monitoring/app-service-overview-monitoring.png)


## <a name="real-time-monitoring"></a>リアルタイム監視

リアルタイム監視を行うには、次のように、**[ライブ イベント ストリーム]** をクリックします。 

![[監視] タブの [ライブ イベント ストリーム] オプション](./media/functions-monitoring/monitor-tab-live-event-stream.png)

ライブ イベント ストリームは、次の例ように、新しいブラウザー タブのグラフに表示されます。 

![ライブ イベント ストリームの例](./media/functions-monitoring/live-event-stream.png)


> [!NOTE]
> データの取り込みに失敗する可能性がある、既知の問題があります。 この問題が発生した場合は、イベント ストリーム データを正しく取り込むために、ライブ イベント ストリームが表示されているブラウザー タブを閉じて、**[ライブ イベント ストリーム]** をもう一度クリックする必要があります。 

ライブ イベント ストリームは、関数についての以下の統計情報をグラフ化します。

* 1 秒あたりの開始した実行
* 1 秒あたりの完了した実行
* 1 秒あたりの失敗した実行
* ミリ秒単位での平均実行時間

これらの統計はリアルタイムですが、実行データの実際のグラフには 10 秒程度の遅延がある場合があります。


## <a name="monitoring-log-files-from-a-command-line"></a>コマンド ラインからのログ ファイルの監視

Azure コマンド ライン インターフェイス (CLI) 1.0 または PowerShell を使用して、ローカル ワークステーションのコマンド ライン セッションにログ ファイルをストリーミングすることができます。

### <a name="monitoring-function-app-log-files-with-the-azure-cli-10"></a>Azure CLI 1.0 での関数アプリのログ ファイルの監視

開始するには、[Azure CLI 1.0 をインストール](../cli-install-nodejs.md)します

以下のコマンドを使用するか、[Azure CLI 1.0 から Azure へのログイン](../xplat-cli-connect.md)に関するページで説明されている他のいずれかのオプションを使用して、Azure アカウントにログインします。

    azure login

次のコマンドを使用して、従来のサービス管理モードで Azure CLI 1.0 コマンドを有効にします。

    azure config mode asm

複数のサブスクリプションがある場合は、次のコマンドを使用してサブスクリプションの一覧を表示し、現在のサブスクリプションを関数アプリが含まれているサブスクリプションに設定します。

    azure account list
    azure account set <subscriptionNameOrId>

次のコマンドは、関数アプリのログ ファイルをコマンド ラインにストリーミングします。

    azure site log tail -v <function app name>

### <a name="monitoring-function-app-log-files-with-powershell"></a>PowerShell での関数アプリのログ ファイルの監視

開始するには、[Azure PowerShell をインストールおよび構成](/powershell/azure/overview)します。

次のコマンドを実行して、Azure アカウントを追加します。

    PS C:\> Add-AzureAccount

複数のサブスクリプションがある場合は、次のコマンドでそれらの名前を一覧表示して、正しいサブスクリプションが現在選択されているかどうかを `IsCurrent` プロパティに基づいて確認できます。

    PS C:\> Get-AzureSubscription

アクティブなサブスクリプションを、関数アプリが含まれているサブスクリプションに設定する必要がある場合は、次のコマンドを使用します。

    PS C:\> Get-AzureSubscription -SubscriptionName "MyFunctionAppSubscription" | Select-AzureSubscription

次のコマンドを使用して、ログを PowerShell セッションにストリーミングします。

    PS C:\> Get-AzureWebSiteLog -Name MyFunctionApp -Tail

詳細については、[Web アプリのログをストリーミングする方法](../app-service-web/web-sites-enable-diagnostic-log.md#streamlogs)に関するページをご覧ください。 

## <a name="next-steps"></a>次のステップ
詳細については、次のリソースを参照してください。

* [関数のテスト](functions-test-a-function.md)
* [関数のスケール](functions-scale.md)


