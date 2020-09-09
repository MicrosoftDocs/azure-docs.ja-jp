---
title: Azure Security Center でのセキュリティの警告の管理 | Microsoft Docs
description: このドキュメントは、Azure セキュリティ センターの機能を使用してセキュリティの警告の管理と対応することに役立ちます。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: f8b09c71e9ad55528788f97fb986606f21e8b0ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84769781"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Azure Security Center でのセキュリティ アラートの管理と対応

このトピックでは、リソースを保護するために、受信したアラートを表示および処理する方法について説明します。 

* さまざまな種類のアラートについては、「[セキュリティ アラートの種類](alerts-reference.md)」をご覧ください。
* Security Center によってアラートが生成される方法の概要については、「[Azure Security Center での脅威の検出と対応](security-center-alerts-overview.md)」をご覧ください。

> [!NOTE]
> 高度な検出を有効にするには、Azure Security Center Standard にアップグレードしてください。 無料試用版が提供されています。 アップグレードするには、 [[セキュリティ ポリシー]](tutorial-security-policy.md)の [価格レベル] を選択してください。 詳細については、「[Azure Security Center pricing (Azure Security Center の料金)](security-center-pricing.md)」を参照してください。

## <a name="what-are-security-alerts"></a>セキュリティの警告とは何か
Security Center は、真の脅威を検出し、偽陽性を減らすために、Azure のリソースやネットワークのほか、接続されているパートナー ソリューション (ファイアウォールやエンドポイント保護ソリューションなど) から、自動的にログ データを収集、分析、統合します。 Security Center には、優先順位の付いたセキュリティの警告の一覧が表示されます。また、すぐに問題を調査する必要がある情報や、攻撃を受けたものを修復する方法についての推奨事項も表示されます。

> [!NOTE]
> Security Center 検出機能の動作について詳しくは、「[Azure Security Center での脅威の検出と対応](security-center-alerts-overview.md#detect-threats)」をご覧ください。

## <a name="manage-your-security-alerts"></a>セキュリティ アラートの管理

1. Security Center ダッシュボードの **[脅威の防止]** タイルを参照して、アラートの概要を確認します。

    ![Security alerts tile in Security Center](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. アラートの詳細を表示するには、タイルをクリックします。

   ![Security Center のセキュリティ アラート](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 表示されるアラートをフィルター処理するには、 **[フィルター]** をクリックし、開いた **[フィルター]** ブレードから、適用するフィルター オプションを選択します。 選択したフィルターに従って一覧が更新されます。 フィルター処理はとても有益な機能です。 たとえば、システム内の潜在的な違反を調査するために、過去 24 時間以内に発生したセキュリティの警告を確認することができます。

    ![Filtering alerts in Security Center](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>セキュリティの警告への対応

1. **[セキュリティのアラート]** の一覧で、セキュリティのアラートをクリックします。 関連するリソースと、攻撃を修復するために実行する必要のある手順が表示されます。

    ![セキュリティの警告への対応](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. この情報を確認した後、攻撃を受けたリソースをクリックします。

    セキュリティ アラート ページの左側のウィンドウには、セキュリティ アラートに関する概要情報 (タイトル、重要度、状態、アクティビティ時間、疑わしいアクティビティの説明、影響を受けるリソース) が表示されます。 影響を受けるリソースと共に、リソースに関連する Azure タグがあります。 これらを使用して、アラートを調査するときにリソースの組織コンテキストを推測します。

    右側のペインには、問題の調査に役立つアラートの詳細が含まれている **[アラートの詳細]** タブがあります。IP アドレス、ファイル、プロセスなど。
     
    ![セキュリティのアラートに対処する方法の推奨事項](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    また、右側のペインには **[アクションの実行]** タブがあります。このタブを使用して、セキュリティ アラートに関するその他のアクションを実行します。 次のようなアクションがあります。
    - *[Mitigate the threat]\(脅威の軽減\)* - このセキュリティ アラートに対する手動の修復手順を提供します
    - *[Prevent future attacks]\(将来の攻撃防止\)* - セキュリティに関する推奨事項を提供して、攻撃対象を減らし、セキュリティ体制を強化し、将来の攻撃を防ぐことができるようにします
    - *[Trigger automated response]\(自動応答のトリガー\)* - このセキュリティ アラートへの応答としてロジック アプリをトリガーするオプションを提供します
    - *[Suppress similar alerts]\(類似のアラートの抑制\)* - 組織に関連しないアラートの場合、同様の特性を持つ今後のアラートを抑制するオプションを提供します

    ![[アクションの実行] タブ](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>関連項目

このドキュメントでは、セキュリティ アラートを表示する方法について説明しました。 関連資料については、次のページを参照してください。

- [アラートの抑制ルールを構成する](alerts-suppression-rules.md)
- [ワークフローの自動化でアラートや推奨事項に対する応答を自動化する](workflow-automation.md)
