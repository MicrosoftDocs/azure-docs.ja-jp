---
title: Azure DevTest Labs での環境のセキュリティの警告
description: この記事では、DevTest Labs で環境のセキュリティの警告を表示し、適切なアクションを実行する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c9f7cf9fe7ab0e3f573470228ee1962aa92ccaef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91308692"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Azure DevTest Labs での環境のセキュリティの警告
ラボ ユーザーは、ラボ環境の Azure Security Center の警告を表示できるようになりました。 Security Center は、真の脅威を検出し、偽陽性を減らすために、Azure のリソースやネットワークのほか、接続されているパートナー ソリューション (ファイアウォールやエンドポイント保護ソリューションなど) から、自動的にログ データを収集、分析、統合します。 Security Center には、優先順位の付いたセキュリティの警告の一覧が表示されます。また、すぐに問題を調査する必要がある情報や、攻撃を受けたものを修復する方法についての推奨事項も表示されます。 [Azure Security Center のセキュリティの警告の詳細についてご覧ください](../security-center//security-center-alerts-overview.md)。  


## <a name="prerequisites"></a>前提条件
現在、ご利用のラボにデプロイされている、サービスとしてのプラットフォーム (PaaS) 環境についてのみ、セキュリティの警告を表示することができます。 この機能をテストまたは使用するには、[ご利用のラボに環境をデプロイします](devtest-lab-create-environment-from-arm.md)。 

## <a name="view-security-alerts-for-an-environment"></a>環境のセキュリティの警告を表示する

1. ご利用のラボのホームページで、左側のメニューにある **[セキュリティの警告]** を選択します。 セキュリティの警告 (高、中、低) の数が表示されるはずです。 [警告が分類される方法](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)について詳細を確認してください。

    ![セキュリティの警告 - 概要](./media/environment-security-alerts/security-alerts-overview-page.png)
2. 最後の列の 3 つのドット (...) を右クリックし、 **[セキュリティの警告の表示]** を選択します。 

    ![[セキュリティの警告] ページのスクリーンショット。[セキュリティの警告の表示] が選択されています。](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. 警告と Advisor の推奨事項の詳細が表示されます。 [Azure Security Center でのセキュリティの警告の管理と対応](../security-center/security-center-managing-and-responding-alerts.md)について詳細を確認してください。

    ![セキュリティの警告の表示](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>次のステップ
環境の詳細については、次の記事を参照してください。

- [Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する](devtest-lab-create-environment-from-arm.md)
- [パブリックな環境の構成と使用](devtest-lab-configure-use-public-environments.md)
