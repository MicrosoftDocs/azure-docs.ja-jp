---
title: "Azure Log Analytics での Active Directory 環境の最適化 | Microsoft Docs"
description: "Active Directory 評価ソリューションを使用して、サーバー環境のリスクと正常性を定期的に評価します。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 81eb41b8-eb62-4eb2-9f7b-fde5c89c9b47
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 24d86e17a063164c31c312685c0742ec4a5c2f1b
ms.openlocfilehash: d50d25e4ea594b5231d29a862f3a98f07de70324
ms.lasthandoff: 03/11/2017


---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-assessment-solution-in-log-analytics"></a>Log Analytics で Active Directory 評価ソリューションを使用して Active Directory 環境を最適化する

Active Directory 評価ソリューションを使用して、サーバー環境のリスクと正常性を定期的に評価します。 この記事は、潜在的な問題の修正措置を実行できるように、ソリューションをインストールして使用するために役立ちます。

このソリューションでは、デプロイされているサーバー インフラストラクチャに固有の優先順位付けされた推奨事項の一覧を提供します。 推奨事項は&4; つの対象領域に分類されているので、すばやくリスクを把握し、アクションを実行できます。

推奨事項は、マイクロソフトのエンジニアによる数多くの顧客訪問によって得られた知識と経験に基づいています。 各推奨事項では、問題が重要である理由と推奨される変更を実装する方法に関するガイダンスが提供されます。

組織にとって最も重要な対象領域を選択し、リスクのない正常な環境の実行に向けた進行状況を追跡できます。

ソリューションを追加し、評価が完了すると、環境のインフラストラクチャの **[AD Assessment (AD 評価)]** ダッシュボードに対象領域の概要情報が表示されます。 次のセクションでは、**[AD Assessment (AD 評価)]** ダッシュボードの情報を使用する方法について説明します。ここでは、Active Directory サーバー インフラストラクチャを確認し、推奨された解決方法を実行できます。

![[SQL 評価] タイルの画像](./media/log-analytics-ad-assessment/ad-tile.png)

![[SQL 評価] ダッシュボードの画像](./media/log-analytics-ad-assessment/ad-assessment.png)

## <a name="installing-and-configuring-the-solution"></a>ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。

* エージェントは、評価されるドメインのメンバーであるドメイン コントローラーにインストールする必要があります。
* Active Directory 評価ソリューションには、OMS エージェントがある各コンピューターにインストールされている、サポートされているバージョンの .NET Framework 4 (4.5.2 以降) が必要です。
* Active Directory 評価ソリューションを OMS ワークスペースに追加します。[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ADAssessmentOMS?tab=Overview) から追加するか、[ソリューション ギャラリーからの Log Analytics ソリューションの追加](log-analytics-add-solutions.md)に関するページで説明されている手順に従って追加してください。  さらに手動で構成する必要はありません。

  > [!NOTE]
  > ソリューションを追加した後、AdvisorAssessment.exe ファイルがエージェントを含むサーバーに追加されます。 構成データが読み取られ、処理のためにクラウドの OMS サービスに送信されます。 受信したデータにロジックが適用され、クラウド サービスによってそのデータが記録されます。
  >
  >

## <a name="active-directory-assessment-data-collection-details"></a>Active Directory 評価データ収集の詳細
Active Directory 評価では、有効になっているエージェントを使用して、WMI データ、レジストリ データ、パフォーマンス データを収集します。

次の表に、エージェントのデータ収集方法、Operations Manager (SCOM) が必要であるかどうか、およびどのくらいの頻度でデータがエージェントによって収集されるかを示します。

| プラットフォーム | 直接エージェント | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![あり](./media/log-analytics-ad-assessment/oms-bullet-green.png) |![あり](./media/log-analytics-ad-assessment/oms-bullet-green.png) |![なし](./media/log-analytics-ad-assessment/oms-bullet-red.png) |![いいえ](./media/log-analytics-ad-assessment/oms-bullet-red.png) |![あり](./media/log-analytics-ad-assessment/oms-bullet-green.png) |7 日 |

## <a name="understanding-how-recommendations-are-prioritized"></a>推奨事項の優先順位設定方法について
提供されるすべての推奨事項には、推奨事項の相対的な重要度を示す重み付け値が与えられます。 最も重要な&10; 個の推奨事項のみが表示されます。

### <a name="how-weights-are-calculated"></a>重み付けの計算方法
重み付けは、次の&3; つの重要な要因に基づく集計値です。

* 識別された注意点によって問題が発生する *確率* 。 確率が高いほど、推奨事項に割り当てられる総合スコアが大きくなります。
* 問題が発生する原因となった場合の注意点の組織への *影響度* 。 影響度が高いほど、推奨事項に割り当てられる総合スコアが大きくなります。
* 推奨実行を実装するために必要な *作業量* 。 作業量が多いほど、推奨事項に割り当てられる総合スコアが小さくなります。

各推奨事項に対する重み付けは、各対象領域で使用可能な合計スコアの割合として表されます。 たとえば、セキュリティとコンプライアンス対象領域の推奨事項のスコアが 5% である場合、その推奨事項を実装するとセキュリティとコンプライアンスの総合スコアが 5% 上昇します。

### <a name="focus-areas"></a>対象領域
**セキュリティとコンプライアンス** - この対象領域は、潜在的なセキュリティの脅威と侵害、企業のポリシー、および技術、法律、規制のコンプライアンス要件に関する推奨事項を示しています。

**可用性とビジネス継続性** - この対象領域は、サービスの可用性、インフラストラクチャの回復性、およびビジネス保護に関する推奨事項を示しています。

**パフォーマンスとスケーラビリティ** - この対象領域は、組織の IT インフラストラクチャを拡張し、IT 環境が現在のパフォーマンス要件を満たして、インフラストラクチャのニーズの変化に対応できるようにするために役立つ推奨事項を示しています。

**アップグレード、移行、デプロイ** - この対象領域は、既存のインフラストラクチャへの Active Directory のアップグレード、移行、およびデプロイに役立つ推奨事項を示しています。

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>すべての対象領域で 100% のスコアを目指す必要がありますか?
必ずしもその必要はありません。 推奨事項は、マイクロソフトのエンジニアによる数多くの顧客訪問によって得られた知識と経験に基づいています。 ただし、まったく同じサーバー インフラストラクチャは存在せず、関連性の高い、または低い推奨事項が存在する可能性があります。 たとえば、仮想マシンがインターネットに対して公開されていない場合、セキュリティに関する一部の推奨事項は関連性が低い場合があります。 優先度の低いアドホックなデータ収集とレポート作成を提供するサービスの場合、可用性に関する一部の推奨事項は関連性が低い可能性があります。 成熟したビジネスにとって重要な問題は、起業したばかりの会社には重要性が低い場合があります。 ユーザーが優先度の高い対象領域を識別して、スコアの経時変化を監視できます。

すべての推奨事項には、重要である理由についてのガイダンスが含まれます。 ユーザーはこのガイダンスを使用し、IT サービスの性質と組織のビジネス ニーズに基づいて、推奨事項を実装することが会社にとって適切かどうかを評価する必要があります。

## <a name="use-assessment-focus-area-recommendations"></a>評価の関心領域に関する推奨事項の使用
OMS の評価ソリューションを使用するには、ソリューションが事前にインストールされている必要があります。 ソリューションのインストールの詳細については、「 [ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」を参照してください。 インストール後は、OMS の [概要] ページの [評価] タイルを使用して、推奨事項の概要を表示できます。

インフラストラクチャの準拠に関する評価の概要を表示してから、推奨事項を確認します。

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>対象領域の推奨事項を表示して修正措置を行うには
1. **[概要]** ページで、サーバー インフラストラクチャの **[評価]** タイルをクリックします。
2. **[評価]** ページの対象領域のいずれかのブレードで概要情報を確認し、いずれかの情報をクリックして、その対象領域の推奨事項を表示します。
3. いずれの対象領域ページでも、ユーザーの環境を対象とした、優先順位が付けられた推奨事項を表示できます。 推奨事項の理由の詳細を確認するには、 **[影響を受けるオブジェクト]** でその推奨事項をクリックします。  
    ![評価に関する推奨事項の画像](./media/log-analytics-ad-assessment/ad-focus.png)
4. **[推奨する解決方法]**で推奨された修正措置を実行することができます。 項目に対応すると、それ以降の評価では、推奨されたアクションが行われたと記録され、準拠のスコアが上がります。 修正された項目は **[合格したオブジェクト]**として表示されます。

## <a name="ignore-recommendations"></a>推奨事項を無視する
無視する推奨事項がある場合は、OMS が使用するテキスト ファイルを作成して、推奨事項が評価結果に表示されないようにすることができます。

### <a name="to-identify-recommendations-that-you-will-ignore"></a>無視する推奨事項を識別するには
1. ワークスペースにサインインして、ログ検索を開きます。 次のクエリを使用して、環境内のコンピューターで失敗した推奨事項の一覧を表示します。

   ```
   Type=ADAssessmentRecommendation RecommendationResult=Failed | select  Computer, RecommendationId, Recommendation | sort  Computer
   ```

   ログ検索のクエリを示すスクリーン ショットを次に示します。![失敗した推奨事項](./media/log-analytics-ad-assessment/ad-failed-recommendations.png)
2. 無視する推奨事項を選択します。 次の手順で RecommendationId の値を使用します。

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>IgnoreRecommendations.txt テキスト ファイルを作成および使用するには
1. IgnoreRecommendations.txt という名前のファイルを作成します。
2. Log Analytics に個別の行で無視させ、ファイルを保存して閉じさせるには、推奨事項ごとにそれぞれ RecommendationId を貼り付けるか入力します。
3. OMS に推奨事項を無視させる各コンピューターの次のフォルダーにファイルを配置します。
   * Microsoft Monitoring Agent がインストールされたコンピューター (直接または Operations Manager 経由で接続されている) - *SystemDrive*:\Program Files\Microsoft Monitoring Agent\Agent
   * Operations Manager 管理サーバー - *SystemDrive*:\Program Files\Microsoft System Center 2012 R2\Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>推奨事項が無視されていることを確認するには
次回スケジュールされている評価 (既定では 7 日おき) の実行後、推奨事項は *Ignored* とマークされ、評価ダッシュボードには表示されません。

1. 次のログ検索クエリを使用して、無視されるすべての推奨事項の一覧を表示します。

    ```
    Type=ADAssessmentRecommendation RecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```
2. 無視された推奨事項を表示することを後で決定する場合は、IgnoreRecommendations.txt ファイルを削除します。また、そのファイルから RecommendationID を削除することもできます。

## <a name="ad-assessment-solutions-faq"></a>AD 評価ソリューションに関する FAQ
*評価はどのくらいの頻度で実行されますか?*

* 評価は 7 日おきに実行されます。

*評価の実行頻度を構成する方法がありますか?*

* 現時点ではありません。

*評価ソリューションを追加後、別のサーバーが検出された場合、それは評価されますか?*

* はい。検出されると、それ以降 7 日おきに評価されます。

*サーバーを使用停止にした場合、それはいつ評価対象から除外されますか?*

* サーバーは、3 週間にわたりデータを送信しない場合、除外されます。

*データ収集を行うプロセスの名前は何ですか?*

* AdvisorAssessment.exe です。

*データの収集にはどれくらいの時間がかかりますか?*

* サーバー上での実際のデータ収集には約 1 時間かかります。 Active Directory サーバーの数が多いサーバーでは、もっと長くなる可能性があります。

*どのような種類のデータが収集されますか?*

* 次の種類のデータが収集されます。
  * WMI
  * レジストリ
  * パフォーマンス カウンター

*データが収集されるタイミングを構成する方法はありますか?*

* 現時点ではありません。

*上位 10 個の推奨事項しか表示されないのはなぜですか?*

* タスクの一覧を余すことなく完全に提供するのでなく、まず優先的な推奨事項への対処に重点を置くことをお勧めしています。 優先的な推奨事項に対処すると、追加の推奨事項が表示されます。 詳細な一覧を確認する場合は、ログ検索を使用してすべての推奨事項を表示することができます。

*推奨事項を無視する方法はありますか?*

* はい。前のセクション「[推奨事項を無視する](#ignore-recommendations)」を参照してください。

## <a name="next-steps"></a>次のステップ
* 「 [Log Analytics におけるログの検索](log-analytics-log-searches.md) 」を参照し、詳細な AD 評価データと推奨事項を確認してください。

