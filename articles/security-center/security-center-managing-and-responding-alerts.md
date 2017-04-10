---
title: "Azure Security Center でのセキュリティの警告の管理 | Microsoft Docs"
description: "このドキュメントは、Azure セキュリティ センターの機能を使用してセキュリティの警告の管理と対応することに役立ちます。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: hero-article
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: bf3aa51b4b1224840ae6897ff7df86cf3d2b3477
ms.lasthandoff: 03/07/2017


---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>Azure セキュリティ センターでのセキュリティの警告の管理と対応
このドキュメントは、Azure Security Center を使用してセキュリティの警告を管理し、必要な対応を行う際にお役立てください。

> [!NOTE]
> 高度な検出を有効にするには、Azure Security Center Standard にアップグレードしてください。 60 日間の無料試用版が提供されています。 アップグレードするには、 [[セキュリティ ポリシー]](security-center-policies.md)の [価格レベル] を選択してください。 詳細については、「[Azure Security Center pricing (Azure Security Center の料金)](security-center-pricing.md)」を参照してください。
>
>

## <a name="what-are-security-alerts"></a>セキュリティの警告とは何か
Security Center は、真の脅威を検出し、偽陽性を減らすために、Azure のリソースやネットワークのほか、接続されているパートナー ソリューション (ファイアウォールやエンドポイント保護ソリューションなど) から、自動的にログ データを収集、分析、統合します。 Security Center には、優先順位の付いたセキュリティの警告の一覧が表示されます。また、すぐに問題を調査する必要がある情報や、攻撃を受けたものを修復する方法についての推奨事項も表示されます。


> [!NOTE]
> Security Center の検出機能に関する詳細については、「 [Azure Security Center の検出機能](security-center-detection-capabilities.md)」を参照してください。
>
>

## <a name="managing-security-alerts"></a>セキュリティの警告の管理
現在の警告は、 **[セキュリティ アラート]** タイルで確認できます。 各警告の詳細については、Azure ポータルを開いて以下の手順に従います。

1. [セキュリティ センター] ダッシュボードに **[セキュリティ アラート]** タイルが表示されます。

    ![Security alerts tile in Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2. タイルをクリックすると、次のように、警告についての詳細が示される **[セキュリティ アラート]** ブレードが開きます。

   ![The Security alerts blade in Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

このブレードの下部に、各警告の詳細が表示されます。 警告を並べ替えるには、並べ替えに使用する列をクリックします。 各列の定義を以下に示します。

* **説明**: 警告の短い説明。
* **カウント**: 特定の日に検出された、特定の種類の全警告の一覧。
* **検出元**: 警告をトリガーしたサービス。
* **日付**: イベントが発生した日付。
* **状態**: その警告の現在の状態。 ここに表示される状態は&2; 種類です。
  * **アクティブ**: セキュリティの警告が検出されました。
* **重大度**: 重大度。高、中、低で示します。

### <a name="filtering-alerts"></a>警告のフィルター処理
日付、状態と重要度に基づいて警告をフィルター処理することができます。 警告のフィルター処理は、セキュリティの警告の表示範囲を限定する必要がある場合に便利です。 たとえば、システム内の潜在的な違反を調査するために、過去 24 時間以内に発生したセキュリティの警告を確認することができます。

1. **[セキュリティ アラート]** ブレードの **[フィルター]** をクリックします。 **[フィルター]** ブレードが開いたら、確認する日付、状態、重要度の値を選択します。

    ![Filtering alerts in Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>セキュリティの警告への対応
セキュリティの警告を選択して、警告を発生させたイベントの詳細を確認します。必要に応じて、攻撃を受けたものを修復するために必要な手順を確認します。 セキュリティの警告は種類と日付別に分類されています。 セキュリティの警告をクリックすると、分類された警告の一覧が示されたブレードが開きます。

![Azure セキュリティ センターのセキュリティの警告への対応](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

この場合、トリガーされた警告が、リモート デスクトップ プロトコル (RDP) の疑わしいアクティビティを参照します。 1 列目には、攻撃を受けたリソースが表示されます。2 列目には、リソースが攻撃された回数が表示されます。3 列目には、攻撃の時刻が表示されます。4 列目には、警告の状態が表示されます。5 列目には、攻撃の重大度が表示されます。 この情報を確認し、攻撃を受けたリソースをクリックすると、新しいブレードが開きます。

![Azure セキュリティ センターのセキュリティの警告に対処する方法の推奨事項](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

このブレードの **[説明]** フィールドで、このイベントについての詳しい情報を確認できます。 詳しい情報を確認することで、セキュリティの警告を引き起こした原因、ターゲット リソース、(場合によっては) 発生元の IP アドレス、解決のための推奨事項を知ることができます。  Windows セキュリティ イベント ログによっては IP アドレスが含まれていないため、発生元の IP アドレスは空 (利用不可) である場合もあります。

セキュリティ センターから提案される修復方法は、セキュリティの警告によって変わります。 場合によっては、他の Azure 機能を使用して推奨される修復方法を実行する必要があります。 このような攻撃に対する修復の例として、[ネットワーク ACL](../virtual-network/virtual-networks-acl.md) または[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)規則を使用してこの攻撃の発信元の IP アドレスをブラックリストに指定することが挙げられます。

> [!NOTE]
> さまざまな警告の種類については、「 [Azure Security Center の種類別のセキュリティの警告](security-center-alerts-type.md)」を参照してください。
>
>

## <a name="see-also"></a>関連項目
このドキュメントでは、セキュリティ センターでのセキュリティ ポリシーの構成方法について説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ インシデントの処理](security-center-incident.md)
* [Azure Security Center の検出機能](security-center-detection-capabilities.md)
* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

