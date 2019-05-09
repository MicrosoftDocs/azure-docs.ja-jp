---
title: Azure DevOps と Azure Application Insights による DevOps リリース パイプラインの継続的監視 | Microsoft Docs
description: Application Insights で行う継続的な監視を迅速にセットアップする手順を説明します
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/13/2017
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 41999defb01e024773b6364f169a1ce3b1377237
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54056512"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>リリース パイプラインに継続的監視を追加する

Azure DevOps Services と Azure Application Insights を統合すると、ソフトウェア開発ライフ サイクルを通じて DevOps リリース パイプラインを継続的に監視できます。 

Azure DevOps Services が継続的な監視をサポートするようになりました。これにより、リリース パイプラインに Application Insights などの Azure リソースから監視データを組み込むことができます。 Application Insights のアラートが検出されたときは、展開をゲートしたままにするか、アラートが解決されるまでロールバックできます。 すべてのチェックが成功すると、展開はテストから実稼働まですべて自動で進められます。手動による介入は必要ありません。 

## <a name="configure-continuous-monitoring"></a>継続的監視を構成する

1. 既存の Azure DevOps Services プロジェクトを選択する。

2. **[ビルドとリリース]** にポインターを合わせ、**[リリース]** を選択して **プラス記号** > **[リリース定義の作成]** の順にクリックし、**[監視]** > **[Azure App Service Deployment with Continuous Monitoring]\(継続的監視を使用した Azure App Service の展開\)** を探します。

   ![新しい Azure DevOps Services リリース パイプライン](media/continuous-monitoring/001.png)

3. **[適用]** をクリックします。

4. 赤色の感嘆符の横にある青色のテキストを選択して**環境のタスクを表示**します。

   ![環境のタスクを表示](media/continuous-monitoring/002.png)

   構成ボックスが表示されたら、下の表を使用して入力フィールドに入力します。

    | パラメーター        | 値 |
   | ------------- |:-----|
   | **環境名**      | リリース パイプライン環境を説明する名前 |
   | **Azure サブスクリプション** | ドロップ ダウンに、Azure DevOps Services 組織にリンクされている Azure サブスクリプションがリストされます|
   | **App Service の名前** | 他のフィールドの選択内容によって、このフィールドに新しい値を手動で入力する必要がある場合があります。 |
   | **リソース グループ**    | ドロップ ダウンに、使用可能なリソース グループがリストされます |
   | **Application Insights のリソース名** | ドロップ ダウンに、以前に選択したリソース グループに対応するすべての Application Insights リソースがリストされます。

5. **[Configure Application Insights alerts]\(Application Insights のアラートの構成\)** を選択します。

6. 既定のアラート ルールにする場合は、**[保存]** を選択し、説明のコメントを入力して **[OK]** をクリックします。

## <a name="modify-alert-rules"></a>アラート ルールを変更する

1. 定義済みのアラートの設定を変更するには、**アラート ルール**の右側に省略記号 **[...]** が表示されているボックスをクリックします。

   (すぐに使える 4 つのアラート ルールとして、可用性、失敗した要求、サーバー応答時間、サーバーの例外があります。)

2. **[可用性]** の横にあるドロップダウン記号をクリックします。

3. 可用性の **[しきい値]** をサービス レベルの要件を満たすように変更します。

   ![アラートの変更](media/continuous-monitoring/003.png)

4. **[OK]** > **[保存]** の順に選択し、説明のコメントを入力して **[OK]** をクリックします。

## <a name="add-deployment-conditions"></a>展開の条件を追加する

1. **[パイプライン]** をクリックし、継続的監視ゲートを必要とするステージに応じて **[配置前の条件]** または **[配置後の条件]** 記号を選択します。

   ![配置前の条件](media/continuous-monitoring/004.png)

2. **[ゲート]** を **[有効]** > **[Approval gates]\(承認ゲート\)** の順に設定し、**[追加]** をクリックします。

3. **[Azure Monitor]** を選択します (このオプションにより、Azure Monitor と Application Insights の両方からアラートにアクセスできるようになります)

    ![Azure Monitor](media/continuous-monitoring/005.png)

4. **ゲートのタイムアウト**値を入力します。

5. **サンプリング間隔**を入力します。

## <a name="deployment-gate-status-logs"></a>展開ゲートのステータス ログ

展開ゲートを追加すると、事前に定義したしきい値を超えると発生する Application Insights のアラートによって、不要なリリース プロモーションから展開が保護されます。 アラートが解決すると、展開は自動的に進められます。

この動作を確認するには、**[リリース]** を選択し、リリース名を右クリックして **[開く]** > **[ログ]** の順にクリックします。

![ログ](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>次の手順

Azure Pipelines に関する詳細情報については、これらの[クイック スタート](https://docs.microsoft.com/azure/devops/pipelines)をお試しください。
