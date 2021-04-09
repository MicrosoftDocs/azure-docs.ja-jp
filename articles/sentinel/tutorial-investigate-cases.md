---
title: Azure Sentinel でインシデントを調査する | Microsoft Docs
description: このチュートリアルでは、Azure Sentinel を使用して、割り当てや調査が可能なインシデントを生成する高度なアラート ルールを作成する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: yelevin
ms.openlocfilehash: 8853f3774bb35361746c8b706f38bc54079d74f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790988"
---
# <a name="tutorial-investigate-incidents-with-azure-sentinel"></a>チュートリアル:Azure Sentinel でインシデントを調査する

> [!IMPORTANT]
> この調査グラフは、現在 **一般公開** されています。 

このチュートリアルは、Azure Sentinel でインシデントを調査するためのものです。 Azure Sentinel にデータ ソースを接続した後で、不審な事態が起きたときに通知を受けるようにします。 これを実現するために、Azure Sentinel では、割り当てや調査が可能なインシデントを生成する高度なアラート ルールを作成できます。

この記事には、次の内容が含まれます。
> [!div class="checklist"]
> * インシデントの調査
> * 調査グラフを使用する
> * 脅威に対応する

インシデントには複数のアラートを含めることができます。 ケースは、特定の調査に関連するすべての証拠を集計したものです。 インシデントは、 **[分析]** ページで作成した分析ルールに基づいて作成されます。 重大度や状態など、アラートに関連するプロパティはインシデント レベルで設定されます。 探している脅威の種類とその検出方法が Azure Sentinel に認識された後、インシデントを調査することで検出された脅威を監視できます。

## <a name="prerequisites"></a>前提条件
- インシデントの調査が可能なのは、分析ルールを設定するときにエンティティ マッピング フィールドを使用した場合のみです。 調査グラフでは、元のインシデントにエンティティが含まれている必要があります。

- インシデントを割り当てる必要があるゲスト ユーザーがいる場合は、そのユーザーに Azure AD テナントの[ディレクトリ閲覧者](../active-directory/roles/permissions-reference.md#directory-readers)ロールを割り当てる必要があります。 通常の (ゲスト以外の) ユーザーには、既定でこのロールが割り当てられています。

## <a name="how-to-investigate-incidents"></a>インシデントの調査方法

1. **[インシデント]** を選択します。 **[インシデント]** ページには、自分に割り当てられたインシデントの数、開いているインシデントの数、 **[処理中]** に設定したインシデントの数、閉じたインシデントの数が表示されます。 各インシデントについて、その発生時刻とインシデントの状態を確認できます。 重大度を調べて、最初に処理するインシデントを決定します。

    ![インシデントの重大度の表示](media/tutorial-investigate-cases/incident-severity.png)

1. 状態や重要度など、必要に応じてインシデントをフィルター処理できます。

1. 調査を開始するには、特定のインシデントを選択します。 右側に、その重大度、関連するエンティティの数の概要、このインシデントをトリガーした生イベント、インシデントの一意の ID など、インシデントの詳細情報が表示されます。

1. インシデント内のアラートとエンティティの詳細を表示するには、インシデントのページで **[View full details]\(完全な詳細の表示\)** を選択し、インシデントの情報がまとめられている関連タブを確認します。 **[アラート]** タブで、アラート自体を確認します。 アラートをトリガーしたクエリ、クエリごとに返された結果の数、アラートに対してプレイブックを実行する機能といった、アラートに関するすべての関連情報を確認できます。 インシデントをさらにドリルダウンするには、 **[イベント]** の数を選択します。 これにより、結果を生成したクエリと、アラートをトリガーしたイベントが Log Analytics で開きます。 **[エンティティ]** タブで、アラート ルールの定義の一部としてマップしたすべてのエンティティを表示できます。

    ![アラートの詳細の表示](media/tutorial-investigate-cases/alert-details.png)

1. 積極的にインシデントを調査している場合、インシデントを閉じるまではその状態を **[処理中]** に設定することをお勧めします。

1. インシデントは特定のユーザーに割り当てることができます。 インシデントごとに、 **[Incident owner]\(インシデントの所有者\)** フィールドを設定することで、所有者を割り当てることができます。 すべてのインシデントは、開始時点では未割り当ての状態です。 また、調査した内容やインシデントに関する懸念事項を他のアナリストが理解できるように、コメントを追加することもできます。

    ![ユーザーへのインシデントの割り当て](media/tutorial-investigate-cases/assign-incident-to-user.png)

1. **[調査]** を選択して、調査マップを表示します。

## <a name="use-the-investigation-graph-to-deep-dive"></a>調査グラフを使用して詳細に調査する

調査グラフを使用すると、アナリストは調査ごとに適切な質問を行うことができます。 調査グラフを使用すると、関連データとすべての関連するエンティティを関連付けることによって、潜在的なセキュリティ脅威の範囲を理解し、根本原因を特定するのに役立ちます。 グラフに表示される任意のエンティティを選択して、さまざまな展開オプションを選択することにより、より詳細に調査できます。  
  
調査グラフには、次のものがあります。

- **生データからのビジュアル コンテキスト**:ライブのビジュアル グラフには、生データから自動的に抽出されたエンティティのリレーションシップが表示されます。 これにより、さまざまなデータ ソース間の接続を簡単に確認できます。

- **調査範囲全体の検出**:組み込みの探索クエリを使用して調査範囲を拡大し、侵害の全範囲を明らかにします。

- **組み込みの調査手順**:定義済みの探索オプションを使用して、脅威の発生時に適切な質問をするようにします。

調査グラフを使用するには:

1. インシデントを選択し、 **[調査]** を選択します。 これにより、調査グラフが表示されます。 グラフには、アラートに直接接続されているエンティティと、さらに接続されている各リソースのイラスト マップが示されます。

   > [!IMPORTANT] 
   > - インシデントの調査が可能なのは、分析ルールを設定するときにエンティティ マッピング フィールドを使用した場合のみです。 調査グラフでは、元のインシデントにエンティティが含まれている必要があります。
   >
   > - 現在、Azure Sentinel では、**過去 30 日間まで** のインシデントの調査がサポートされています。

   ![マップを表示する](media/tutorial-investigate-cases/map1.png)

1. エンティティを選択すると、 **[エンティティ]** ウィンドウが開き、そのエンティティに関する情報を確認できます。

    ![マップでのエンティティの表示](media/tutorial-investigate-cases/map-entities.png)
  
1. 各エンティティの上にカーソルを置くと、エンティティの種類ごとにセキュリティ エクスパートやアナリストによって作成された質問の一覧が表示され、調査を深めることができます。 これらのオプションを **探索クエリ** と呼びます。

    ![詳細の探索](media/tutorial-investigate-cases/exploration-cases.png)

   たとえば、コンピューターで、関連するアラートを要求できます。 探索クエリを選択すると、結果として得られたエンティティがグラフに追加されます。 この例では、 **[Related alerts]\(関連するアラート\)** を選択すると、グラフに次のアラートが返されます。

    ![関連するアラートの表示](media/tutorial-investigate-cases/related-alerts.png)

1. 探索クエリごとに、 **[イベント]\>** を選択して、生イベントの結果と、Log Analytics で使用されるクエリを開くオプションを選択できます。

1. インシデントの理解のために、グラフには平行したタイムラインが表示されます。

    ![マップでのタイムラインの表示](media/tutorial-investigate-cases/map-timeline.png)

1. タイムライン上にカーソルを移動して、グラフ上のどの項目がどの時点で発生したかを確認します。

    ![マップのタイムラインを使用してアラートを調査する](media/tutorial-investigate-cases/use-timeline.png)

## <a name="closing-an-incident"></a>インシデントを閉じる

特定のインシデントを解決した後 (調査が結論に達した場合など)、インシデントの状態を **[Closed]\(終了\)** に設定する必要があります。 この場合、インシデントを閉じる理由を指定して、インシデントを分類するよう求められます。 この手順は必須です。 **[Select classification]\(分類を選択\)** をクリックし、ドロップダウン リストから次のいずれかを選択します。

- [True Positive - suspicious activity]\(真陽性 - 疑わしいアクティビティ\)
- [Benign Positive - suspicious but expected]\(無害な陽性 - 不審だが、予期されている)
- [False Positive - incorrect alert logic]\(偽陽性 - 間違ったアラート ロジック\)
- [False Positive - incorrect data]\(偽陽性 - 不適切なデータ\)
- [Undetermined]\(不明\)

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-dropdown.png" alt-text="[分類の選択] リストで使用できる分類が強調表示されているスクリーンショット。":::

適切な分類を選択した後、 **[コメント]** フィールドに説明文を追加します。 これは、このインシデントを参照する必要がある場合に役立ちます。 完了したら **[適用]** をクリックすると、インシデントが閉じられます。

:::image type="content" source="media/tutorial-investigate-cases/closing-reasons-comment-apply.png" alt-text="{alt-text}":::

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Sentinel を使用して、インシデントの調査を開始する方法について説明しました。 [自動プレイブックを使用した脅威への対応](tutorial-respond-threats-playbook.md)に関するチュートリアルに進んでください。
> [!div class="nextstepaction"]
> [[Respond to threats]](tutorial-respond-threats-playbook.md) (脅威への対応) を行って、脅威への対応を自動化します。

