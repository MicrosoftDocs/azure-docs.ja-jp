---
title: ホスト クォータを要求し、Azure VMware Solution を有効にする
description: ホスト クォータまたは容量をリクエストし、Azure VMware Solution リソース プロバイダーを有効にする方法について説明します。 また、Azure VMware Solution の既存のプライベート クラウドにさらに多くのホストをリクエストすることもできます。
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 02/17/2021
ms.openlocfilehash: 5d154f5c63ffccdbf1729e641133b54be478d884
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653170"
---
# <a name="request-host-quota-and-enable-azure-vmware-solution"></a>ホスト クォータを要求し、Azure VMware Solution を有効にする

この操作方法では、ホスト クォータまたは容量をリクエストし、[Azure VMware Solution](introduction.md) リソース プロバイダーを有効にする方法について説明します。これにより、このサービスが有効になります。 Azure VMware Solution を有効にするには、事前にサポート チケットを提出して、ホストが割り当てられるようにする必要があります。 Azure VMware Solution の既存のプライベート クラウドがあり、さらに多くのホストを割り当てる必要がある場合は、同じプロセスに従います。

>[!IMPORTANT]
>リクエストする数によっては、ホストの割り当てに数日かかることがあります。  そのため、プロビジョニングに必要な分をクエストすることで、クォータの引き上げを頻繁にリクエストする必要がないようにしてください。


全体のプロセスはシンプルで、次の 2 つの手順で構成されています。
- [EA のお客様](#request-host-quota-for-ea-customers)または [CSP のお客様](#request-host-quota-for-csp-customers)用に追加のホスト クォータまたは容量をリクエストする 
- Microsoft.AVS リソース プロバイダーを有効にする

## <a name="eligibility-criteria"></a>対象となる条件

Azure サブスクリプションの Azure アカウントが必要です。 Azure サブスクリプションは、次のいずれかの条件に従っている必要があります。

- Microsoft との [Azure Enterprise Agreement (EA)](../cost-management-billing/manage/ea-portal-agreements.md) に従ったサブスクリプション。
- 既存のクラウド ソリューション プロバイダー (CSP) のもとで Azure がコントラクトまたは Azure プランを提供している、CSP によって管理されているサブスクリプション。

## <a name="request-host-quota-for-ea-customers"></a>EA のお客様用のホスト クォータをリクエストする

1. Azure portal の **[ヘルプとサポート]** で、 **[新しいサポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)** を作成し、チケットに関する次の情報を提供します。
   - **問題の種類:** 技術
   - **サブスクリプション:** サブスクリプションを選択します。
   - **サービス:** [すべてのサービス] > [Azure VMware Solution]
   - **リソース:** 一般的な質問 
   - **概要:** 容量が必要です
   - **問題の種類:** 容量管理に関する問題
   - **問題のサブタイプ:** 追加のホストクォータおよび容量に関する顧客の要求

1. サポート チケットの **[詳細]** タブの **[説明]** で、次を指定します。

   - POC または運用環境 
   - リージョン名
   - ホストの数
   - その他の詳細

   >[!NOTE]
   >Azure VMware Solution では、プライベート クラウドを稼働させ、冗長 N + 1 ホストを提供するために、3 つ以上のホストを推奨しています。 

1. **[確認および作成]** を選択して、リクエストを送信します。


## <a name="request-host-quota-for-csp-customers"></a>CSP のお客様用のホスト クォータをリクエストする 

CSP は [Microsoft パートナー センター](https://partner.microsoft.com)を使用して、自社の顧客のために Azure VMware Solution を有効にする必要があります。 この記事では、[CSP Azure プラン](/partner-center/azure-plan-lp)を例として使用して、パートナー向けの購入手順を示します。

パートナー センターから **代理管理者** (AOBO) の手順を使用して、Azure portal にアクセスします。

>[!IMPORTANT] 
>Azure VMware Solution サービスでは、必要なマルチテナントに対応していません。 これを必要とするホスティング パートナーはサポートされていません。 

1. CSP Azure プランを構成します。

   1. **パートナー センター** で、 **[CSP]** を選択して、 **[顧客]** 領域にアクセスします。
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Microsoft パートナー センターの [顧客] 領域" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::
   
   1. 目的の顧客を選択し、 **[製品の追加]** を選択します。
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Microsoft パートナー センター" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::
   
   1. **[Azure プラン]** を選択し、 **[カートに追加]** を選択します。 
   
   1. 顧客の Azure プラン サブスクリプションの全般的な設定を確認し、終了します。 詳細については、[Microsoft パートナー センターに関するドキュメント](/partner-center/azure-plan-manage)を参照してください。

1. Azure プランを構成し、サブスクリプションに必要な [Azure RBAC アクセス許可](/partner-center/azure-plan-manage)を準備したら、Azure プラン サブスクリプションのクォータをリクエストします。 

   1. **代理管理者** (AOBO) の手順を使用して、[Microsoft パートナー センター](https://partner.microsoft.com)から Azure portal にアクセスします。
   
   1. **[CSP]** を選択して、 **[顧客]** 領域にアクセスします。
   
   1. 顧客の詳細を展開し、 **[Microsoft Azure の管理ポータル]** を選択します。
   
   1. Azure portal の **[ヘルプとサポート]** で、 **[新しいサポート リクエスト](https://rc.portal.azure.com/#create/Microsoft.Support)** を作成し、チケットに関する次の情報を入力します。
      - **問題の種類:** 技術
      - **サブスクリプション:** サブスクリプションを選択します。
      - **サービス:** [すべてのサービス] > [Azure VMware Solution]
      - **リソース:** 一般的な質問 
      - **概要:** 容量が必要です
      - **問題の種類:** 容量管理に関する問題
      - **問題のサブタイプ:** 追加のホストクォータおよび容量に関する顧客の要求
   
   1. サポート チケットの **[詳細]** タブの **[説明]** で、次を指定します。
   
      - POC または運用環境 
      - リージョン名
      - ホストの数
      - その他の詳細
      - 複数の顧客をホストすることが想定されているか。
   
      >[!NOTE]
      >Azure VMware Solution では、プライベート クラウドを稼働させ、冗長 N + 1 ホストを提供するために、3 つ以上のホストを推奨しています。 
   
   1. **[確認および作成]** を選択して、リクエストを送信します。

## <a name="register-the-microsoftavs-resource-provider"></a>**Microsoft.AVS** リソース プロバイダーを登録する

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="next-steps"></a>次のステップ

リソースを有効にし、適切なネットワークを準備したら、[プライベート クラウドを作成](tutorial-create-private-cloud.md)できます。
