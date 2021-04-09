---
title: Azure Security Center の統合ワークロード保護を有効にする
description: Azure Defender を有効にして、Azure Security Center の保護をお使いのハイブリッドおよびマルチクラウドのリソースに拡張する方法について説明します
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 02/24/2021
ms.openlocfilehash: 6496b18c8c22cbbd4fd3344736a4b38b7a998890
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102108147"
---
# <a name="quickstart-enable-azure-defender"></a>クイックスタート: Azure Defender を有効にする

Azure Defender の利点の詳細については、「[Security Center (無料) と有効化された Azure Defender](security-center-pricing.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Security Center のクイックスタートおよびチュートリアルの目的上、Azure Defender を有効にする必要があります。 

Azure Defender を使用して Azure サブスクリプション全体を保護できます。保護は、サブスクリプション内のすべてのリソースに継承されます。

30 日間の無料試用版が提供されています。 選択した通貨でのお住まいのリージョンに応じた価格の詳細については、「[Security Center の価格](https://azure.microsoft.com/pricing/details/security-center/)」を参照してください。

## <a name="enable-azure-defender-from-the-azure-portal"></a>Azure portal から Azure Defender を有効にする

脅威防止機能を含め、Security Center のすべての機能を有効にするには、適用可能なワークロードを含むサブスクリプションで Azure Defender を有効にする必要があります。 ワークスペース レベルで有効にしても、Just-In-Time VM アクセス、適応型アプリケーション制御、Azure リソースのネットワーク検出は有効になりません。 さらに、ワークスペース レベルで使用できる Azure Defender プランは、Azure Defender for servers と Azure Defender for SQL servers on machines のみです。

- **Azure Defender for Storage accounts** は、サブスクリプション レベルまたはリソース レベルで有効にすることができます
- **Azure Defender for SQL** は、サブスクリプション レベルまたはリソース レベルで有効にすることができます
- **Azure Database for MariaDB、MySQL、PostgreSQL** の脅威の防止は、リソース レベルでのみ有効にできます

### <a name="to-enable-azure-defender-on-your-subscriptions-and-workspaces"></a>サブスクリプションとワークスペースで Azure Defender を有効にするには:

- 1 つのサブスクリプションで Azure Defender を有効にするには:

    1. Security Center のメイン メニューから、 **[価格と設定]** を選択します。
    1. 保護するサブスクリプションまたはワークスペースを選択します。
    1. **[Azure Defender on]\(Azure Defender を有効化\)** を選択してアップグレードします。
    1. **[保存]** を選択します。

    > [!TIP]
    > Azure Defender の各プランは個別に価格設定されているので、個別に有効/無効に設定できます。 たとえば、Azure App Service プランが関連付けられていないサブスクリプションで Azure Defender for App Service をオフにすることができます。 

    :::image type="content" source="./media/security-center-pricing/pricing-tier-page.png" alt-text="ポータルの Security Center の価格ページ":::

- 複数のサブスクリプションまたはワークスペースで Azure Defender を有効にするには:

    1. Security Center のサイドバーで、 **[作業の開始]** を選択します。

        **[アップグレード]** タブに、オンボードの対象となるサブスクリプションとワークスペースが一覧表示されます。

        :::image type="content" source="./media/enable-azure-defender/get-started-upgrade-tab.png" alt-text="[はじめに] ページの [アップグレード] タブ"::: 

    1. **[Azure Defender を有効にするサブスクリプションとワークスペースを選択してください]** ボックスの一覧で、アップグレードするサブスクリプションとワークスペースを選択し、 **[アップグレード]** を選択して Azure Defender を有効にします。

       - 試用版の対象にならないサブスクリプションやワークスペースを選択した場合、次の手順でそれらがアップグレードされ、課金が開始されます。
       - 無料試用版の対象となるワークスペースを選択した場合、次の手順で試用版が開始されます。

        :::image type="content" source="./media/enable-azure-defender/upgrade-selected-workspaces-and-subscriptions.png" alt-text="[作業の開始] ページから、選択したすべてのワークスペースとサブスクリプションをアップグレードする":::


## <a name="next-steps"></a>次のステップ

Azure Defender を有効にした後は、[Azure Security Center からのエージェントと拡張機能の自動プロビジョニング](security-center-enable-data-collection.md)に関するページに説明されている、必要なエージェントと拡張機能による自動データ収集を有効にします。