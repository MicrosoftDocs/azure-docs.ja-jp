---
title: Personalizer を構成する
description: サービス構成には、サービスによる報酬の処理方法、サービスによる探索の頻度、モデルの再トレーニング頻度、格納するデータ量などがあります。
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 9b7347cb98bcbf2e1d92f115d404197083acef4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91292610"
---
# <a name="configure-personalizer-learning-loop"></a>Personalizer の学習ループを構成する

サービス構成には、サービスによる報酬の処理方法、サービスによる探索の頻度、モデルの再トレーニング頻度、格納するデータ量などがあります。

Azure portal で、その Personalizer リソースの **[構成]** ページで学習ループを構成します。

<a name="configure-service-settings-in-the-azure-portal"></a>
<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

## <a name="planning-configuration-changes"></a>構成の変更の計画

構成の変更によっては[モデルのリセット](#settings-that-include-resetting-the-model)が行われるため、構成の変更を計画する必要があります。

[見習いモード](concept-apprentice-mode.md)の使用を計画している場合は、見習いモードに切り替える前に、お使いの Personalizer の構成を確認してください。

<a name="clear-data-for-your-learning-loop"></a>

## <a name="settings-that-include-resetting-the-model"></a>モデルのリセットを含む設定

次のアクションを実行すると、過去 2 日間の利用可能なデータを使用して、モデルの再トレーニングがトリガーされます。

* 報酬
* 探索

すべてのデータを [クリア](how-to-manage-model.md)するには、 **[モデルと学習設定]** ページを使用します。

## <a name="configure-rewards-for-the-feedback-loop"></a>フィードバック ループの報酬を構成する

学習ループの報酬を使用するようにサービスを構成します。 以下の値を変更すると、現在の Personalizer モデルがリセットされ、過去 2 日分のデータで再トレーニングされます。

> [!div class="mx-imgBorder"]
> ![フィードバック ループの報酬値を構成する](media/settings/configure-model-reward-settings.png)

|値|目的|
|--|--|
|Reward wait time (報酬の待機時間)|Personalizer が、Rank 呼び出しに対する報酬値を収集する時間 (Rank 呼び出しの時点から) を設定します。 この値を設定するには、次のことを決定します。「Personalizer で報酬呼び出しをどの程度待機する必要があるか。」 このウィンドウの後に到着した報酬はログには記録されますが、学習には使用されません。|
|Default reward (既定の報酬)|Rank 呼び出しに関連付けられた報酬の待機時間ウィンドウ中に Personalizer が報酬呼び出しを受信しなかった場合、Personalizer は既定の報酬を割り当てます。 既定では、またほとんどのシナリオでは、既定の報酬はゼロ (0) です。|
|Reward aggregation (報酬の集計)|同じ Rank API 呼び出しに対して複数の報酬を受信した場合は、集計方式として **[Sum]\(合計\)** または **[Earliest]\(最も早い\)** が使用されます。 [Earliest]\(最も早い\) の場合、最も早く受信したスコアが採用され、残りは破棄されます。 これは、重複する可能性がある呼び出しの中から一意の報酬を選択する場合に便利です。 |

これらの値を変更した後は、必ず **[保存]** を選択してください。

## <a name="configure-exploration-to-allow-the-learning-loop-to-adapt"></a>学習ループを調整できるように探索を構成する

パーソナル化では、トレーニングされたモデルの予測を使用する代わりに代替手段を探ることによって、新しいパターンを検出し、時間の経過に伴うユーザーの行動の変化に適応できます。 **[探索]** の値は、Rank 呼び出しの何パーセントが探索で応答されるかを決定します。

この値を変更すると、現在の Personalizer モデルはリセットされ、過去 2 日分のデータで再トレーニングされます。

![[探索] の値は、Rank 呼び出しの何パーセントが探索で応答されるかを決定します](media/settings/configure-exploration-setting.png)

この値を変更した後は、必ず **[保存]** を選択してください。

<a name="model-update-frequency"></a>

## <a name="configure-model-update-frequency-for-model-training"></a>モデル トレーニングのためのモデル更新頻度を構成する

**[Model update frequency]\(モデルの更新頻度\)** は、モデルがトレーニングされる頻度を設定します。

|頻度の設定|目的|
|--|--|
|1 分|1 分間の更新頻度は、Personalizer を使用してアプリケーションのコードを **デバッグ** するとき、デモを実行するとき、または機械学習の側面を対話形式でテストするときに有用です。|
|約 15 分|モデルの更新頻度を高くすると、ユーザー動作の **変化を詳細に追跡** したい場合に便利です。 例としては、ライブ ニュース、バイラル コンテンツ、またはライブ商品入札で実行されるサイトがあります。 これらのシナリオでは、15 分間の頻度を使用できます。 |
|1 時間|ほとんどのユース ケースでは、更新頻度が低い方が効果的です。|

![[Model update frequency]\(モデルの更新頻度\) により、新しい Personalizer モデルを再トレーニングする頻度を設定します。](media/settings/configure-model-update-frequency-settings-15-minutes.png)

この値を変更した後は、必ず **[保存]** を選択してください。

## <a name="data-retention"></a>データの保持

**[Data retention period]\(データ保持期間\)** により、Personalizer がデータのログを保持する日数を設定します。 Personalizer の効果を測定し、学習ポリシーを最適化するために使用する[オフライン評価](concepts-offline-evaluation.md)を実行するには、過去のデータ ログが必要です。

この値を変更した後は、必ず **[保存]** を選択してください。



## <a name="next-steps"></a>次のステップ

[モデルを管理する方法について学習する](how-to-manage-model.md)
