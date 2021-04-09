---
title: 攻撃ベクトル レポートの作成
description: 攻撃ベクトル レポートには、悪用可能なデバイスの脆弱性チェーンがグラフィカルに表示されています。
ms.date: 12/17/2020
ms.topic: how-to
ms.openlocfilehash: 6c178f379561ca4a572a7ae8f556ae6753d6e2be
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784375"
---
# <a name="attack-vector-reporting"></a>攻撃ベクトル レポートの作成

## <a name="about-attack-vector-reports"></a>攻撃ベクトル レポートについて

攻撃ベクトル レポートには、悪用可能なデバイスの脆弱性チェーンがグラフィカルに表示されています。 これらの脆弱性により、攻撃者は重要なネットワーク デバイスにアクセスできます。 攻撃ベクトル シミュレーターでは、攻撃ベクトルがリアルタイムで計算され、特定のターゲットに対するすべての攻撃ベクトルが分析されます。

攻撃ベクトルを使用することにより、攻撃シーケンスにおける軽減アクティビティの効果を評価できます。 これにより、たとえば、システムをアップグレードして攻撃チェーンを破ることによって攻撃者のパスが中断されるかどうか、または代替の攻撃パスが残るかどうかを判断できます。 この情報は、修復および軽減アクティビティの優先順位を付けるうえで役立ちます。

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="コントロール センターでアラートを表示する。":::

> [!NOTE]
> このセクションで説明されている手順を実行できるのは、管理者とセキュリティ アナリストです。

## <a name="create-an-attack-vector-report"></a>攻撃ベクトル レポートの作成

攻撃ベクトルのシミュレーションを作成するには、次のようにします。

1. サイドメニューの :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="プラス記号"::: を選択して、シミュレーションを追加します。

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="攻撃ベクトルのシミュレーション。":::

2. シミュレーションのプロパティを入力します。

   - **[名前]** : シミュレーションの名前。

   - **Maximum vectors \(最大ベクトル数\)** : 1 つのシミュレーションでのベクトルの最大数。

   - **Show in Device map \(デバイス マップに表示する\)** : デバイス マップにフィルターとして攻撃ベクトルを表示します。

   - **All Source devices \(すべてのソース デバイス\)** : 攻撃ベクトルで、すべてのデバイスが攻撃ターゲットと見なされます。

   - **Attack Source \(攻撃ソース\)** : 攻撃ベクトルで、指定されたデバイスのみが攻撃ソースと見なされます。

   - **All Target devices \(すべてのターゲット デバイス\)** : 攻撃ベクトルで、すべてのデバイスが攻撃ターゲットと見なされます。

   - **Attack Target \(攻撃ターゲット\)** : 攻撃ベクトルで、指定されたデバイスのみが攻撃ターゲットと見なされます。

   - **Exclude devices \(次のデバイスを除外する\)** : 指定されたデバイスは、攻撃ベクトルのシミュレーションから除外されます。

   - **Exclude Subnets \(次のサブネットを除外する\)** : 指定されたサブネットは、攻撃ベクトルのシミュレーションから除外されます。

3. **[Add Simulation]\(シミュレーションの追加\)** を選択します。 シミュレーションがシミュレーション一覧に追加されます。

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="新しいシミュレーションを追加する。":::

4. シミュレーションを編集する場合は、:::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: を選択します。

   シミュレーションを削除する場合は、:::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: を選択します。

   シミュレーションをお気に入りとしてマークする場合は、:::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: を選択します。

5. 攻撃ベクトルの一覧が表示され、ベクトル スコア (100 に対する割合)、攻撃ソース デバイス、および攻撃ターゲット デバイスが含まれます。 特定の攻撃を選択すると、攻撃ベクトルがグラフィカルに表示されます。

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="攻撃ベクトル。":::

## <a name="see-also"></a>関連項目

[攻撃ベクトル レポートの作成](how-to-create-attack-vector-reports.md)


