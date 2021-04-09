---
title: Azure API Management と Azure Application Insights の統合
titleSuffix: Azure API Management
description: Azure Application Insights の Azure API Management でイベントをログに記録し、表示する方法について説明します。
services: api-management
author: mikebudzynski
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2021
ms.author: apimpm
ms.openlocfilehash: 97f4eb34b88b3454d65b65d236833e1256c98671
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564268"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Azure API Management と Azure Application Insights を統合する方法

Azure API Management は、複数のプラットフォームでアプリを構築し、管理する Web 開発者向けの拡張可能なサービスである Azure Application Insights と簡単に統合できます。 本ガイドでは、このような統合の手順を段階的に説明し、API Management サービス インスタンスに与えるパフォーマンス上の影響を抑えるための方法について説明します。

## <a name="prerequisites"></a>前提条件

本ガイドを実践するには、Azure API Management インスタンスを用意する必要があります。 用意していない場合、[こちらのチュートリアル](get-started-create-service-instance.md)を先に完了してください。

## <a name="create-an-application-insights-instance"></a>Application Insights インスタンスを作成する

Application Insights を使用する前に、このサービスのインスタンスを作成する必要があります。 Azure portal を使用してインスタンスを作成する手順については、「[ワークスペースベースの Application Insights リソース](../azure-monitor/app/create-workspace-resource.md)」を参照してください。
## <a name="create-a-connection-between-application-insights-and-api-management"></a>Application Insights と API Management 間の接続を作成する

1. **Azure Portal** で **Azure API Management サービス インスタンス** に移動します。
1. 左にあるメニューから **[Application Insights]** を選択します。
1. **[+ 追加]** をクリックします。  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="新しい接続を追加する場所が示されているスクリーンショット":::
1. 先の手順で作成した **Application Insights** を選択し、簡単な説明を入力します。
1. **Create** をクリックしてください。
1. これで、インストルメンテーション キーを持つ Application Insights ロガーが作成されました。 一覧に表示されるはずです。  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="インストルメンテーション キーを持つ、新しく生成された Application Insights ロガーが表示される場所を示すスクリーンショット":::

> [!NOTE]
> バックグラウンドで、API Management インスタンスのインストルメンテーション キーを含む[ロガー](/rest/api/apimanagement/2019-12-01/logger/createorupdate) エンティティが Application Insights インスタンス内に作成されます。

## <a name="enable-application-insights-logging-for-your-api"></a>API で Application Insights ログを有効にする

1. **Azure Portal** で **Azure API Management サービス インスタンス** に移動します。
1. 左にあるメニューから **[API]** を選択します。
1. API をクリックします。今回は **Demo Conference API** です。 構成されている場合は、バージョンを選択します。
1. 上のバーで **[設定]** 他部に移動します。
1. 下にスクロールし、 **[診断ログ]** セクションを表示します。  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="App Insights ロガー":::
1. **[有効化]** ボックスをオンにします。
1. **[Destination]\(出力先\)** ドロップダウンで、接続済みのロガーを選択します。
1. **[Sampling (%)]\(サンプリング (%)\)** に **100** と入力し、 **[Always log errors]\(エラーは常に記録する\)** チェックボックスをオンにします。
1. **[保存]** を選択します。

> [!WARNING]
> **[Number of payload bytes to log]\(ログに記録するペイロードのバイト数\)** 設定の既定値 **0** を上書きすると、API のパフォーマンスが大幅に低下する可能性があります。

> [!NOTE]
> バックグラウンドで、'applicationinsights' という名前の[診断](/rest/api/apimanagement/2019-12-01/diagnostic/createorupdate)エンティティが API レベルで作成されます。

| 設定名                        | 値の型                        | 説明                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 有効化                              | boolean                           | この API のログ記録が有効になっているかどうかを指定します。                                                                                                                                                                                                                                                                                                |
| 宛先                         | Azure Application Insights ロガー | 使用する Azure Application Insights ロガーを指定します。                                                                                                                                                                                                                                                                                           |
| [Sampling (%)]\(サンプリング (%)\)                        | decimal                           | 0 から 100 までの値 (パーセント)。 <br/> Application Insights に記録する要求のパーセンテージを指定します。 0% サンプリングの場合、要求はまったく記録されません。100% サンプリングの場合、すべての要求が記録されます。 <br/> Application Insights に要求を記録するときのパフォーマンスへの影響を軽減するには、この設定を使用します。 「[パフォーマンス上の影響とログ サンプリング](#performance-implications-and-log-sampling)」を参照してください。 |
| [Always log errors]\(エラーは常に記録する\)                   | boolean                           | この設定が選択されている場合、 **[サンプリング]** 設定に関係なく、すべてのエラーが Application Insights に記録されます。   
| [Log client IP address]\(クライアント IP アドレスを記録する\) | |  この設定を選択すると、API 要求のクライアント IP アドレスが Application Insights に記録されます。                                         |
| 詳細度         |                                   | 詳細レベルを指定します。 重大度レベルが高いカスタム トレースだけがログに記録されます。 既定値はInformation です。      | 
| [Correlation protocol]\(相関プロトコル\) |  |  複数のコンポーネントによって送信されたテレメトリを関連付けるために使用されるプロトコルを選択します。 既定: [Legacy]\(レガシ\) <br/>詳細については、「[Application Insights におけるテレメトリの相関付け](../azure-monitor/app/correlation.md)」を参照してください。  |
| 基本オプション: [Headers to log]\(ログに記録するヘッダー\)              | list                              | Application Insights に記録する、要求と応答のヘッダーを指定します。  既定値: ヘッダーはログに記録されません。                                                                                                                                                                                                             |
| 基本オプション: [Number of payload bytes to log]\(ログに記録するペイロードのバイト数\) | 整数 (integer)                           | Application Insights に記録する、要求本文と応答本文の最初のバイト数を指定します。  既定値は0。                                                                                                                                                                                                    |
| 詳細オプション: [Frontend Request]\(フロントエンド要求\)  |                                   | "*フロントエンド要求*" を Application Insights に記録するかどうか、また、その方法を指定します。 *フロントエンド要求* は、Azure API Management サービスで受信する要求です。                                                                                                                                                                        |
| 詳細オプション: [Frontend Response]\(フロントエンド応答\) |                                   | "*フロントエンド応答*" を Application Insights に記録するかどうか、また、その方法を指定します。 *フロントエンド応答* は、Azure API Management サービスから発信される応答です。                                                                                                                                                                   |
| 詳細オプション: [Backend Request]\(バックエンド要求\)   |                                   | "*バックエンド要求*" を Application Insights に記録するかどうか、また、その方法を指定します。 *バックエンド要求* は、Azure API Management サービスから発信される要求です。                                                                                                                                                                        |
| 詳細オプション: [Backend Response]\(バックエンド応答\)  |                                   | "*バックエンド応答*" を Application Insights に記録するかどうか、また、その方法を指定します。 *バックエンド応答* は、Azure API Management サービスで受信する応答です。                                                                                                                                                                       |

> [!NOTE]
> ロガーは異なるレベルで指定できます。シングル API ロガーを指定するか、すべての API に 1 つのロガーを指定できます。
>  
> 両方を指定した場合、
> + ロガーが異なる場合、両方が使用されます (ログの多重化)。
> + ロガーが同じでも設定が異なる場合、(詳細度が上の) シングル API ロガーがすべての API のロガーにオーバーライドします。

## <a name="what-data-is-added-to-application-insights"></a>Application Insights に追加されるデータ

Application Insights に届くデータ:

+ あらゆる受信要求 (*フロントエンド要求*、*フロントエンド応答*) の *要求* 利用統計情報項目
+ バックエンド サービスに転送されるあらゆる要求 (*バックエンド要求*、*バックエンド応答*) の *依存関係* 利用統計情報項目
+ 失敗したあらゆる要求の "*例外*" 利用統計情報項目:
    + クライアント接続が閉じられたため、失敗した要求
    + API ポリシーの "*エラー状態*" セクションをトリガーした要求
    + 応答 HTTP ステータス コードが 4xx または 5xx に一致する要求
+ [トレース](api-management-advanced-policies.md#Trace) ポリシーを構成した場合の "*トレース*" テレメトリ項目。 `trace` ポリシーの `severity` 設定は、Application Insights ログの `verbosity` 設定と同じかそれ以上である必要があります。

> [!NOTE]
> Application Insights インスタンスあたりのメトリックとイベントに設定されている最大のサイズと数については、[Application Insights の制限](../azure-monitor/service-limits.md#application-insights)に関する記事を参照してください。

## <a name="performance-implications-and-log-sampling"></a>パフォーマンス上の影響とログ サンプリング

> [!WARNING]
> すべてのイベントを記録すると、受信要求の頻度によっては、パフォーマンスに重大な影響を与える可能性があります。

社内負荷テストによると、要求のペースが毎秒 1,000 件を超えるとき、この機能を有効にすると、スループットに 40%-50% の減少がありました。 Application Insights は、アプリケーション パフォーマンスの評価に統計分析を利用するように設計されています。 監査システムとしては設計されておらず、たくさんの API を対象に要求を 1 つずつ記録するのには適していません。

記録する要求の数は、 **[サンプリング]** 設定を調整することで操作できます (前の手順をご覧ください)。 値が 100% の場合、すべての要求が記録されます。値が 0% の場合、何も記録されません。 **サンプリング** を利用することで利用統計情報の量が減り、パフォーマンスの大きな低下を効果的に回避しながら、ログ利用の長所を維持できます。

要求と応答のヘッダーと本文を記録しないことも、パフォーマンス問題の解消に効果があります。

## <a name="video"></a>ビデオ

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>次のステップ

+ [Azure Application Insights](/azure/application-insights/) についてさらに学習します。
+ [Azure Event Hubs によるログ記録](api-management-howto-log-event-hubs.md)を検討します。
