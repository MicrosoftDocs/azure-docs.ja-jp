---
title: 新しい Azure Application Insights リソースの作成 | Microsoft Docs
description: 新しいライブ アプリケーションを対象にした Application Insights 監視を手動でセットアップします。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 878b007e-161c-4e36-8ab2-3d7047d8a92d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mbullwin
ms.openlocfilehash: 9da52e5a9dfa3b55431d66ed3162172226f71a40
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073302"
---
# <a name="create-an-application-insights-resource"></a>Application Insights リソースの作成

Azure Application Insights には、Microsoft Azure *リソース*のアプリケーションに関するデータが表示されます。 したがって、新しいリソースの作成は、[新しいアプリケーションを監視するための Application Insights の設定][start]の一部です。 新しいリソースを作成した後、そのインストルメンテーション キーを取得し、それを使用し Application Insights SDK を構成できます。 インストルメンテーション キーにより、リソースとテレメトリがリンクされます。

## <a name="sign-in-to-microsoft-azure"></a>Microsoft Azure にサインインする

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="create-an-application-insights-resource"></a>Application Insights リソースの作成

[Azure portal](https://portal.azure.com) にサインインし、Application Insights リソースを作成します。

![左上隅にある + 記号をクリックします。 開発者ツールを選択し、Application Insights を選択する](./media/create-new-resource/new-app-insights.png)

   | 設定        |  値           | 説明  |
   | ------------- |:-------------|:-----|
   | **Name**      | グローバルに一意の値 | 監視しているアプリを識別する名前。 |
   | **リソース グループ**     | myResourceGroup      | App Insights データをホストする新しいリソース グループまたは既存のリソース グループの名前。 |
   | **Location** | 米国東部 | お近くの場所か、アプリがホストされている場所の近くを選択します。 |

必須フィールドに適切な値を入力し、 **[確認と作成]** を選択します。

![必須フィールドに値を入力し、[確認と作成] を選択します。](./media/create-new-resource/review-create.png)

アプリが作成されると、新しいウィンドウが開きます。 そのウィンドウには、監視対象アプリケーションに関するパフォーマンスと使用状況データが表示されます。 

## <a name="copy-the-instrumentation-key"></a>インストルメンテーション キーのコピー

インストルメンテーション キーにより、利用統計情報と関連付けるリソースが識別されます。 インストルメンテーション キーをコピーしてアプリケーションのコードに追加する必要があります。

![インストルメンテーション キーをクリックしてコピーする](./media/create-new-resource/instrumentation-key.png)

## <a name="install-the-sdk-in-your-app"></a>アプリケーションでの SDK のインストール

アプリで Application Insights SDK をインストールします。 この手順は、アプリケーションの種類に大きく依存します。

インストルメンテーション キーを使用して、[アプリケーションにインストールする SDK][start] を構成します。

SDK には、追加コードを記述せずにテレメトリを送信する標準的なモジュールが含まれています。 ユーザーの操作を追跡したり、問題をより詳しく診断したりするには、[API を使用][api]して、独自のテレメトリを送信します。

## <a name="creating-a-resource-automatically"></a>リソースの自動作成
[PowerShell スクリプト](../../azure-monitor/app/powershell.md) を作成して、リソースを自動で作成できます。

## <a name="next-steps"></a>次の手順
* [診断検索](../../azure-monitor/app/diagnostic-search.md)
* [メトリックを探索する](../../azure-monitor/app/metrics-explorer.md)
* [Analytics クエリを作成する](../../azure-monitor/app/analytics.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[start]: ../../azure-monitor/app/app-insights-overview.md