---
title: モデルと学習設定の管理 - Personalizer
description: 機械学習モデルと学習設定は、独自のソース管理システムでバックアップ用にエクスポートできます。
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: 48e954eab9da5dfc638f93a7c4e55c675c4f2ac9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97797249"
---
# <a name="how-to-manage-model-and-learning-settings"></a>モデルと学習設定を管理する方法

機械学習モデルと学習設定は、独自のソース管理システムでバックアップ用にエクスポートできます。

## <a name="export-the-personalizer-model"></a>Personalizer モデルをエクスポートする

**[モデルと学習設定]** のリソース管理セクションで、モデルの作成日と最終更新日を確認し、現在のモデルをエクスポートします。 Azure portal または Personalizer API を使用すると、アーカイブ目的でモデル ファイルをエクスポートすることができます。

![現在の Personalizer モデルをエクスポートする](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>学習ループのデータを消去する

1. Azure portal の Personalizer リソースで、**[モデルと学習設定]** ページの **[データの消去]** を選択します。
1. すべてのデータを消去し、学習ループを元の状態にリセットするには、3 つのチェック ボックスをすべてオンにします。

    ![Azure portal で、Personalizer リソースからデータを消去します。](./media/settings/clear-data-from-personalizer-resource.png)

    |値|目的|
    |--|--|
    |ログに記録された個人用設定と報酬のデータ。|このログ データは、オフライン評価で使用されます。 リソースをリセットする場合は、データを消去します。|
    |Personalizer モデルのリセット。|このモデルは、再トレーニングのたびに変わります。 このトレーニング頻度は、**[構成]** ページの **[upload model frequency]\(モデルのアップロードの頻度\)** で指定されています。 |
    |学習ポリシーを既定に設定する。|オフライン評価の一部として学習ポリシーを変更した場合、これにより元の学習ポリシーにリセットされます。|

1. **[選択したデータの消去]** を選択して、消去プロセスを開始します。 状態は、右上のナビゲーションにある Azure 通知で報告されます。

## <a name="import-a-new-learning-policy"></a>新しい学習ポリシーをインポートする

[学習ポリシー](concept-active-learning.md#understand-learning-policy-settings)設定により、モデル トレーニングの "_ハイパーパラメーター_" が決定されます。 [オフライン評価](how-to-offline-evaluation.md) を実行して、新しい学習ポリシーを見つけます。

1. [Azure portal](https://portal.azure.com) を開いて、お使いの Personalizer リソースを選択します。
1. **[リソース管理]** セクションで **[モデルと学習設定]** を選択します。
1. **[学習設定のインポート]** で、前述の JSON 形式で作成したファイルを選択し、**[アップロード]** ボタンを選択します。

    学習ポリシーが正しくアップロードされたという通知があるまで待機します。

## <a name="export-a-learning-policy"></a>学習ポリシーをエクスポートする

1. [Azure portal](https://portal.azure.com) を開いて、お使いの Personalizer リソースを選択します。
1. **[リソース管理]** セクションで **[モデルと学習設定]** を選択します。
1. **[学習設定のインポート]** で **[学習設定をエクスポートする]** ボタンを選択します。 これにより、`json` ファイルがローカル コンピューターに保存されます。

## <a name="next-steps"></a>次の手順

[オフライン評価で学習ループを分析する](how-to-offline-evaluation.md)
