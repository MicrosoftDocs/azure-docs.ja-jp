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
ms.date: 08/27/2019
ms.author: memildin
ms.openlocfilehash: c3134988452efe16044ab7b7740cafd02e8cf27a
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201928"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Azure Security Center でのセキュリティ アラートの管理と対応

このトピックでは、リソースを保護するために、受信したアラートを表示および処理する方法について説明します。 

* さまざまな種類のアラートについては、「[セキュリティ アラートの種類](security-center-alerts-overview.md#security-alert-types)」をご覧ください。
* Security Center によってアラートが生成される方法の概要については、「[Azure Security Center での脅威の検出と対応](security-center-alerts-overview.md#detect-threats)」をご覧ください。

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

    ![セキュリティのアラートに対処する方法の推奨事項](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    **[一般情報]** セクションには、セキュリティ アラートをトリガーしたものに関する分析情報があります。 ターゲット リソース、ソース IP アドレス (該当する場合)。アラートが現在もアクティブかどうか、推奨修復方法などの情報が表示されます。  

    > [!NOTE]
    >一部の Windows セキュリティ イベント ログには IP アドレスが含まれていないため、発生元の IP アドレスが利用不可の場合もあります。

1. Security Center から提案される修復手順は、セキュリティ アラートによって異なります。 各アラートに従います。 場合によっては、脅威検出アラートを軽減するために、他の Azure コントロールやサービスを使用して、推奨される修復を実装することが必要になる場合があります。 

    次のトピックでは、リソースの種類に応じてさまざまなアラートを説明します。
    
    * [IaaS VM とサーバーのアラート](security-center-alerts-iaas.md)
    * [ネイティブ コンピューティングのアラート](security-center-alerts-compute.md)
    * [データ サービスのアラート](security-center-alerts-data-services.md)
    
    次のトピックでは、Security Center が、Azure にデプロイされたリソースに追加の保護レイヤーを適用するために、Azure インフラストラクチャとの統合から収集したさまざまなテレメトリを活用する方法について説明します。
    
    * [サービス層のアラート](security-center-alerts-service-layer.md)
    * [Azure のセキュリティ製品との統合](security-center-alerts-integration.md)
    
## <a name="see-also"></a>関連項目

このドキュメントでは、セキュリティ センターでのセキュリティ ポリシーの構成方法について説明しました。 セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center のセキュリティ アラート](security-center-alerts-overview.md)。
* [セキュリティ インシデントの処理](security-center-incident.md)
* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
