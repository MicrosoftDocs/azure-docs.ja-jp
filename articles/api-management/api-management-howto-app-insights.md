---
title: Azure API Management と Azure Application Insights を統合する方法 | Microsoft Docs
description: Azure Application Insights の Azure API Management でイベントをログに記録し、表示する方法について説明します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 10e8edcd3a1e781866eaee2cbe48d1536dbc1229
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073582"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Azure API Management と Azure Application Insights を統合する方法

Azure API Management は、複数のプラットフォームでアプリを構築し、管理する Web 開発者向けの拡張可能なサービスである Azure Application Insights と簡単に統合できます。 本ガイドでは、このような統合の手順を段階的に説明し、API Management サービス インスタンスに与えるパフォーマンス上の影響を抑えるための方法について説明します。

## <a name="prerequisites"></a>前提条件

本ガイドを実践するには、Azure API Management インスタンスを用意する必要があります。 用意していない場合、[こちらのチュートリアル](get-started-create-service-instance.md)を先に完了してください。

## <a name="create-an-azure-application-insights-instance"></a>Azure Application Insights インスタンスを作成する

Azure Application Insights を使用する前に、このサービスのインスタンスを作成する必要があります。

1. **Azure Portal** を開き、**Application Insights** に移動します。  
    ![App Insights 作成](media/api-management-howto-app-insights/apim-app-insights-instance-1.png)  
2. **[+ 追加]** をクリックします。  
    ![App Insights 作成](media/api-management-howto-app-insights/apim-app-insights-instance-2.png)  
3. フォームに入力します。 **[アプリケーションの種類]** として **[全般]** を選択します。
4. **Create** をクリックしてください。

## <a name="create-a-connection-between-azure-application-insights-and-azure-api-management-service-instance"></a>Azure Application Insights と Azure API Management サービス インスタンスの間の接続を作成する

1. **Azure Portal** で **Azure API Management サービス インスタンス**に移動します。
2. 左にあるメニューから **[Application Insights]** を選択します。
3. **[+ 追加]** をクリックします。  
    ![App Insights ロガー](media/api-management-howto-app-insights/apim-app-insights-logger-1.png)  
4. 先の手順で作成した **Application Insights** を選択し、簡単な説明を入力します。
5. **Create** をクリックしてください。
6. これで Azure Application Insights ロガーとインストルメンテーション キーが作成されました。 一覧に表示されるはずです。  
    ![App Insights ロガー](media/api-management-howto-app-insights/apim-app-insights-logger-2.png)  

> [!NOTE]
> バックグラウンドで、API Management インスタンスのインストルメンテーション キーを含む[ロガー](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate) エンティティが Application Insights インスタンス内に作成されます。

## <a name="enable-application-insights-logging-for-your-api"></a>API で Application Insights ログを有効にする

1. **Azure Portal** で **Azure API Management サービス インスタンス**に移動します。
2. 左にあるメニューから **[API]** を選択します。
3. API をクリックします。今回は **Demo Conference API** です。
4. 上のバーで **[設定]** 他部に移動します。
5. 下にスクロールし、 **[診断ログ]** セクションを表示します。  
    ![App Insights ロガー](media/api-management-howto-app-insights/apim-app-insights-api-1.png)  
6. **[有効化]** ボックスをオンにします。
7. **[Destination]\(出力先\)** ドロップダウンで、接続済みのロガーを選択します。
8. **[Sampling (%)]\(サンプリング (%)\)** に **100** と入力し、 **[Always log errors]\(エラーは常に記録する\)** チェックボックスをオンにします。
9. **[Save]** をクリックします。

> [!WARNING]
> **[First bytes of body]\(本文の最初のバイト\)** フィールドの既定値 **0** を上書きすると、API のパフォーマンスが大幅に低下する可能性があります。

> [!NOTE]
> バックグラウンドで、'applicationinsights' という名前の[診断](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/diagnostic/createorupdate)エンティティが API レベルで作成されます。

| 設定名                        | 値の型                        | 説明                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 有効化                              | ブール値                           | この API のログ記録が有効になっているかどうかを指定します。                                                                                                                                                                                                                                                                                                |
| Destination                         | Azure Application Insights ロガー | 使用する Azure Application Insights ロガーを指定します。                                                                                                                                                                                                                                                                                           |
| [Sampling (%)]\(サンプリング (%)\)                        | decimal                           | 0 から 100 までの値 (パーセント)。 <br/> Azure Application Insights に記録する要求のパーセンテージを指定します。 0% サンプリングの場合、要求はまったく記録されません。100% サンプリングの場合、すべての要求が記録されます。 <br/> この設定は、Azure Application Insights に要求記録が与えるパフォーマンス上の影響を抑える目的で使用されます (下のセクションをご覧ください)。 |
| [Always log errors]\(エラーは常に記録する\)                   | ブール値                           | この設定が選択されている場合、 **[サンプリング]** 設定に関係なく、すべてのエラーが Azure Application Insights に記録されます。                                                                                                                                                                                                                  |
| 基本オプション: headers              | list                              | Azure Application Insights に記録する、要求と応答のヘッダーを指定します。  既定値: ヘッダーはログに記録されません。                                                                                                                                                                                                             |
| 基本オプション: [First bytes of body]\(本文の最初のバイト\)  | integer                           | Azure Application Insights に記録する、要求本文と応答本文の最初のバイト数を指定します。  既定値: 本文は記録されません。                                                                                                                                                                                              |
| 詳細オプション: [Frontend Request]\(フロントエンド要求\)  |                                   | *フロントエンド要求*を Azure Application Insights に記録するかどうか、また、その方法を指定します。 *フロントエンド要求*は、Azure API Management サービスで受信する要求です。                                                                                                                                                                        |
| 詳細オプション: [Frontend Response]\(フロントエンド応答\) |                                   | *フロントエンド応答*を Azure Application Insights に記録するかどうか、また、その方法を指定します。 *フロントエンド応答*は、Azure API Management サービスから発信される応答です。                                                                                                                                                                   |
| 詳細オプション: [Backend Request]\(バックエンド要求\)   |                                   | *バックエンド要求*を Azure Application Insights に記録するかどうか、また、その方法を指定します。 *バックエンド要求*は、Azure API Management サービスから発信される要求です。                                                                                                                                                                        |
| 詳細オプション: [Backend Response]\(バックエンド応答\)  |                                   | *バックエンド応答*を Azure Application Insights に記録するかどうか、また、その方法を指定します。 *バックエンド応答*は、Azure API Management サービスで受信する応答です。                                                                                                                                                                       |

> [!NOTE]
> ロガーは異なるレベルで指定できます。シングル API ロガーを指定するか、すべての API に 1 つのロガーを指定できます。
>  
> 両方を指定した場合、
> + ロガーが異なる場合、両方が使用されます (ログの多重化)。
> + ロガーが同じでも設定が異なる場合、(詳細度が上の) シングル API ロガーがすべての API のロガーにオーバーライドします。

## <a name="what-data-is-added-to-azure-application-insights"></a>Azure Application Insights に追加されるデータ

Azure Application Insights に届くデータ:

+ あらゆる受信要求 (*フロントエンド要求*、*フロントエンド応答*) の*要求*利用統計情報項目
+ バックエンド サービスに転送されるあらゆる要求 (*バックエンド要求*、*バックエンド応答*) の*依存関係*利用統計情報項目
+ 失敗したあらゆる要求の*例外*利用統計情報項目

失敗した要求とは次のような要求です。

+ クライアント接続が閉じられたため、失敗した要求
+ API ポリシーの*エラー状態*セクションをトリガーした要求
+ 応答 HTTP ステータス コードが 4xx または 5xx に一致する要求

## <a name="performance-implications-and-log-sampling"></a>パフォーマンス上の影響とログ サンプリング

> [!WARNING]
> すべてのイベントを記録すると、受信要求の頻度によっては、パフォーマンスに重大な影響を与える可能性があります。

社内負荷テストによると、要求のペースが毎秒 1,000 件を超えるとき、この機能を有効にすると、スループットに 40%-50% の減少がありました。 Azure Application Insights は、アプリケーション パフォーマンスの評価に統計分析を利用するように設計されています。 監査システムとしては設計されておらず、たくさんの API を対象に要求を 1 つずつ記録するのには適していません。

記録する要求の数は、 **[サンプリング]** 設定を調整することで操作できます (上の手順をご覧ください)。 値が 100% の場合、すべての要求が記録されます。値が 0% の場合、何も記録されません。 **サンプリング**を利用することで利用統計情報の量が減り、パフォーマンスの大きな低下を効果的に回避しながら、ログ利用の長所を維持できます。

要求と応答のヘッダーと本文を記録しないことも、パフォーマンス問題の解消に効果があります。

## <a name="video"></a>ビデオ

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>次の手順

+ [Azure Application Insights](https://docs.microsoft.com/azure/application-insights/) についてさらに学習します。
+ [Azure Event Hubs によるログ記録](api-management-howto-log-event-hubs.md)を検討します。
