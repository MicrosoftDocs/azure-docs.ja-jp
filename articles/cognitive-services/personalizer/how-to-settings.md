---
title: 設定を構成する - Personalizer
titleSuffix: Azure Cognitive Services
description: サービス構成には、サービスによる報酬の処理方法、サービスによる探索の頻度、モデルの再トレーニング頻度、格納するデータ量などがあります。
services: cognitive-services
author: edjez
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: edjez
ms.openlocfilehash: 6f5028f093a9fd8c17928c2167039599d4db897c
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722344"
---
# <a name="personalizer-settings"></a>Personalizer 設定

サービス構成には、サービスによる報酬の処理方法、サービスによる探索の頻度、モデルの再トレーニング頻度、格納するデータ量などがあります。

## <a name="create-personalizer-resource"></a>Personalizer リソースを作成する

フィードバック ループごとに Personalizer リソースを作成します。 

1. [Azure ポータル](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)にサインインします。 前のリンクから、Personalizer サービスの**作成**ページに移動できます。 
1. サービス名を入力し、サブスクリプション、場所、価格レベル、リソース グループを選択します。
1. 確認を選択し、 **[作成]** を選択します。

## <a name="configure-service-settings-in-the-azure-portal"></a>Azure portal でサービス設定を構成する

1. [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) にサインインします。
1. Personalizer リソースを検索します。 
1. **リソース管理**セクションで、 **[設定]** を選択します。

    Azure portal を終了する前に、 **[キー]** のページからいずれかのリソース キーをコピーします。 これは、[Personalizer SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer) を使用するために必要になります。

### <a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>ユース ケースに基づいてフィードバック ループの報酬設定を構成する

フィードバック ループでの報酬の使用に対してサービスの設定を構成します。 以下の設定を変更すると、現在の Personalizer モデルはリセットされ、過去 2 日分のデータで再トレーニングされます。

![フィードバック ループの報酬設定を構成する](media/settings/configure-model-reward-settings.png)

|Setting|目的|
|--|--|
|Reward wait time (報酬の待機時間)|Personalizer が、Rank 呼び出しに対する報酬値を収集する時間 (Rank 呼び出しの時点から) を設定します。 この値を設定するには、次のことを決定します。「Personalizer で報酬呼び出しをどの程度待機する必要があるか。」 このウィンドウの後に到着した報酬はログには記録されますが、学習には使用されません。|
|Default reward (既定の報酬)|Rank 呼び出しに関連付けられた報酬の待機時間ウィンドウ中に Personalizer が報酬呼び出しを受信しなかった場合、Personalizer は既定の報酬を割り当てます。 既定では、またほとんどのシナリオでは、既定の報酬は 0 です。|
|Reward aggregation (報酬の集計)|同じ Rank API 呼び出しに対して複数の報酬を受信した場合は、集計方式として **[Sum]\(合計\)** または **[Earliest]\(最も早い\)** が使用されます。 [Earliest]\(最も早い\) の場合、最も早く受信したスコアが採用され、残りは破棄されます。 これは、重複する可能性がある呼び出しの中から一意の報酬を選択する場合に便利です。 |

これらの設定を変更した後は、必ず **[保存]** を選択してください。

### <a name="exploration-setting"></a>探索の設定 

パーソナル化では、新しいパターンを検出し、代替手段を探ることによって、時間の経過に伴うユーザーの行動の変化に適応できます。 **[Exploration]\(探索\)** 設定により、Rank 呼び出しの何パーセントに対して探索により応答するかが決まります。 

この設定を変更すると、現在の Personalizer モデルはリセットされ、過去 2 日分のデータで再トレーニングされます。

![探索設定により、Rank 呼び出しの何パーセントに対して探索により応答するかが決まります](media/settings/configure-exploration-setting.png)

この設定を変更した後は、必ず **[保存]** を選択してください。

### <a name="model-update-frequency"></a>モデルの更新頻度

すべてのアクティブ イベントからの Reward API 呼び出しからトレーニングされた最新のモデルは、Personalizer の Rank 呼び出しで自動的に使用されません。 **[Model update frequency]\(モデルの更新頻度\)** は、Rank 呼び出しによって使用されるモデルが更新される頻度を設定します。 

モデルの更新頻度を高くすると、ユーザーの動作の変化を詳細に追跡したい場合に便利です。 例としては、ライブ ニュース、バイラル コンテンツ、またはライブ商品入札で実行されるサイトがあります。 これらのシナリオでは、15 分間の頻度を使用できます。 ほとんどのユース ケースでは、更新頻度が低い方が効果的です。 1 分間の更新頻度は、Personalizer を使用してアプリケーションのコードをデバッグするとき、デモを実行するとき、または機械学習の側面を対話形式でテストするときに有用です。

![[Model update frequency]\(モデルの更新頻度\) により、新しい Personalizer モデルを再トレーニングする頻度を設定します。](media/settings/configure-model-update-frequency-settings.png)

この設定を変更した後は、必ず **[保存]** を選択してください。

### <a name="data-retention"></a>データの保持

**[Data retention period]\(データ保持期間\)** により、Personalizer がデータのログを保持する日数を設定します。 Personalizer の効果を測定し、学習ポリシーを最適化するために使用する[オフライン評価](concepts-offline-evaluation.md)を実行するには、過去のデータ ログが必要です。

この設定を変更した後は、必ず **[保存]** を選択してください。

## <a name="export-the-personalizer-model"></a>Personalizer モデルをエクスポートする

**[Model and Policy]\(モデルとポリシー\)** のリソース管理セクションで、モデルの作成日と最終更新日を確認し、現在のモデルをエクスポートします。 Azure portal または Personalizer API を使用すると、アーカイブ目的でモデル ファイルをエクスポートすることができます。 

![現在の Personalizer モデルをエクスポートする](media/settings/export-current-personalizer-model.png)

## <a name="import-and-export-learning-policy"></a>学習ポリシーのインポートとエクスポート

**[Model and Policy]\(モデルとポリシー\)** のリソース管理セクションで、新しい学習ポリシーをインポートしたり、現在の学習ポリシーをエクスポートしたりします。

## <a name="next-steps"></a>次の手順

<!--
[How to use the Personalizer container](https://go.microsoft.com/fwlink/?linkid=2083923&clcid=0x409)
-->
[リージョンの可用性の詳細](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)
