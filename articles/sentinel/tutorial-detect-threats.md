---
title: Azure Sentinel プレビューでアラートを調査する | Microsoft Docs
description: このチュートリアルでは、Azure Sentinel でアラートを調査する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: b5fbc5ac-68b2-4024-9c1b-bd3cc41a66d0
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/20/2019
ms.author: rkarlin
ms.openlocfilehash: 2cc33a9ac55ae9e906d88b72476d4b5ee244d2c8
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780421"
---
# <a name="tutorial-detect-threats-with-azure-sentinel-preview"></a>チュートリアル:Azure Sentinel プレビューを使用して脅威を検出する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure Sentinel に[データ ソースを接続した](quickstart-onboard.md)後で、不審な事態が起きたときに通知を受けるようにします。 これを実現するために、Azure Sentinel では高度なアラート ルールを作成して、環境での異常や脅威を詳しく調査するために割り当てて使用できるインシデントを生成できます。 

このチュートリアルは、Azure Sentinel で脅威を検出するためのものです。
> [!div class="checklist"]
> * 検出ルールの作成
> * 脅威への対応を自動化する

## <a name="create-detection-rules"></a>検出ルールの作成

インシデントを調査するには、最初に検出ルールを作成する必要があります。 

> [!NOTE]
> Azure Sentinel で生成されたアラートは、[Microsoft Graph Security](https://aka.ms/securitygraphdocs) を通じて使用できます。 詳細および統合パートナーについては、[Microsoft Graph Security アラートのドキュメント](https://aka.ms/graphsecurityreferencebetadocs)を参照してください。

検出ルールは、環境内で脅威や異常の疑いのあるもので、直ちに知る必要のあるものに基づいて作成され、これらを明らかにし、調査し、修復できるようにします。 

1. Azure portal の Azure Sentinel の下で **[Analytics]** を選択します。

   ![Analytics](./media/tutorial-detect-threats/alert-rules.png)

2. 上部のメニュー バーで **[+ 追加]** をクリックします。  

   ![アラート ルールの作成](./media/tutorial-detect-threats/create-alert-rule.png)

3. **[アラート ルールの作成]** の下で、わかりやすい名前を入力し、必要に応じて **[重大度]** を設定します。 

4. Log Analytics でクエリを作成し、 **[Set alert rule]** (アラート ルールの設定) フィールドに貼り付けます。 異常な数のリソースが Azure アクティビティで作成されたときにアラートを発するサンプル クエリを次に示します。

        AzureActivity
        | where OperationName == "Create or Update Virtual Machine" or OperationName == "Create Deployment"
        | where ActivityStatus == "Succeeded"
        | make-series dcount(ResourceId)  default=0 on EventSubmissionTimestamp in range(ago(7d), now(), 1d) by Caller

   > [!NOTE]
   > クエリの長さは 1 から 10,000 文字にする必要があります。また、"search *" と "union *" を含めることはできません。


5. **[エンティティ マッピング]** セクションで、 **[エンティティ型]** の下のフィールドを使用して、クエリ内の列を Azure Sentinel が認識するエンティティ フィールドにマップします。 フィールドごとに、Log Analytics で作成したクエリで関連する列を適切なエンティティのフィールドにマップします。 **[プロパティ]** の下で関連する列名を選択します。 各エンティティには、SID や GUID などの複数のフィールドが含まれています。上のレベルのエンティティだけでなく、任意のフィールドに従ってエンティティをマップすることができます。

6. **[Alert trigger]** (アラートのトリガー) の下でアラートのトリガー条件を定義します。 これには、アラートをトリガーする条件を定義します。 

7. クエリを実行する頻度について **[頻度]** を、5 分ごとか 1 日に 1 度かに設定します。 

8. クエリを実行するデータ量について時間枠を制御するように、 **[期間]** を設定します。たとえば、60 分のデータに対し毎時間実行できます。

9. **[Suppression]** (抑制) を設定することもできます。 抑制は、同じインシデントについて重複するアラートがトリガーされないようにする場合に便利です。 このようにして、特定の期間中に、アラートがトリガーされないようにすることができます。 これは、同じインシデントに対する重複したアラートを回避する場合に役立ち、一定の期間、連続したアラートを抑制することができます。 たとえば、 **[Alert scheduling** **Frequency]** (アラート スケジュール頻度) が 60 分に設定され、 **[Alert scheduling Period]** (アラート スケジュール期間) が 2 時間に設定されており、クエリ結果が定義されたしきい値を超えた場合、アラートは 2 度 (1 度は最後の 60 分を過ぎて最初に検出されたときに、もう 1 度はサンプリングされている 2 時間のデータの最初の 60 分で検出されたとき) トリガーされます。 アラートがトリガーされる場合、抑制は、アラート期間で設定された時間の量に対して行うことをお勧めします。 例では、60 分間の抑制を設定でき、その結果、直近の 1 時間中に起きたイベントについてのみアラートはトリガーされます。

8. クエリを **[Set alert rule]** (アラート ルールの設定) フィールドに貼り付けると、直後に **[Logic alert simulation]** (ロジック アラート シミュレーション) の下でアラートのシミュレーションを確認でき、作成したアラートの特定の時間間隔を超えて生成されるデータの量を把握できます。 これは、 **[頻度]** と **[しきい値]** に対して設定したものに応じて異なります。 アラートが平均して過度に頻繁にトリガーされることがわかった場合は、平均のベースラインの上になるように、結果の数をより高く設定できます。

9. **[作成]** をクリックして、アラート ルールを初期化します。 アラートが作成されると、アラートが含まれるインシデントが作成されます。 **[Security Analytics]** (Security Analytics) タブで、定義された検出ルールを行として確認できます。各ルールの一致 (トリガーされるアラート) 数も確認できます。 この一覧から、各ルールを有効にしたり、無効にしたり、削除したりできます。 また、各アラートの行の末尾の省略記号 (...) を右クリックで選択して、一致結果の編集、無効化、複製、表示、またはルールの削除を行えます。 **[Analytics]** ページは、有効にするテンプレートやテンプレートに基づいて作成するアラート ルールを含むアクティブなアラート ルールすべてのギャラリーです。

1. アラート ルールの結果を **[インシデント]** ページで確認できます。このページではトリアージを行い、[インシデントを調査](tutorial-investigate-cases.md)し、脅威を修復できます。



## <a name="automate-threat-responses"></a>脅威への対応を自動化する

SIEM や SOC チームには、定期的にセキュリティ アラートが殺到することがあります。 生成されるアラートの量が非常に多いため、対応するセキュリティ管理者は途方に暮れています。 この結果、多くのアラートを調査することができず、組織は見過ごした攻撃に対して脆弱なままになっていることがほとんどです。 

これらのアラートの多くは、ほとんどではないにしても、定義された特定の修復アクションで対処できる繰り返し発生するパターンに準拠しています。 Azure Sentinel では、プレイブック内で修復を定義することが既にできるようになっています。 また、プレイブック定義の一部としてリアルタイム オートメーションを設定して、特定のセキュリティ アラートに対する定義済みの対応を完全に自動化することもできます。 リアルタイム オートメーションを使用すると、対応チームは、繰り返し発生するタイプのアラートに対する所定の対応を完全に自動化することにより、作業負荷を大幅に削減できます。これにより、一般的でないアラート、パターンの分析、脅威ハンティングなどに専念することができます。

対応を自動化するには:

1. 対応を自動化するアラートを選択します。
1. Azure Sentinel ワークスペースのナビゲーション メニューから、 **[分析]** を選択します。
1. 自動化するアラートを選択します。 
1. **[アラート ルールの編集]** ページの **[Real-time automation]\(リアルタイム オートメーション\)** で、このアラート ルールが一致したときに実行する**トリガー対象のプレイブック**を選択します。
1. **[保存]** を選択します。

   ![リアルタイム オートメーション](./media/tutorial-detect-threats/rt-configuration.png)


さらに、 **[プレイブックの表示]** をクリックし、続いて実行するプレイブックを選択してアラート内部からプレイブックを実行することにより、アラートを手動で修復することができます。 新しいプレイブックを作成する方法や既存のプレイブックを編集する方法については、[Azure Sentinel でのプレイブックの使用方法](tutorial-respond-threats-playbook.md)に関するページを参照してください。



## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure Sentinel を使用して、脅威の検出を開始する方法について説明しました。 

脅威への応答を自動化する方法については、[自動プレイブックを使用した脅威への対応](tutorial-respond-threats-playbook.md)に関するチュートリアルを参照してください。
> [!div class="nextstepaction"]
> [[Respond to threats]](tutorial-respond-threats-playbook.md) (脅威への対応) を行って、脅威への対応を自動化します。

