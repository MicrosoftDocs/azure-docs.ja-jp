---
title: Azure Data Box のロックボックスを構成する
description: Azure Data Box でカスタマー ロックボックスを使用する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.topic: how-to
ms.date: 07/10/2020
ms.author: alkohli
ms.subservice: pod
ms.openlocfilehash: 178ad169c1b576458f38b440ca79f4bb9eb012f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92124913"
---
# <a name="use-customer-lockbox-for-azure-data-box-preview"></a>Azure Data Box のカスタマー ロックボックスを使用する (プレビュー)

Azure Data Box は、顧客データを Azure との間で転送するために使用されます。 サポート リクエスト中、Microsoft サポートが顧客データにアクセスする必要がある場合があります。 カスタマー ロックボックスは、これらのデータ アクセス要求を確認して承認または拒否するためのインターフェイスとして使用できます。 

この記事では、カスタマー ロックボックス要求を開始し、Data Box のインポート注文とエクスポート注文を追跡する方法について説明します。 この記事は Azure Data Box デバイスと Azure Data Box Heavy デバイスの両方に適用されます。 

## <a name="devops-workflow-for-data-access"></a>データ アクセスのための DevOps ワークフロー

通常、Microsoft のサポート チームおよび Data Box 運用チームが顧客データにアクセスすることはありません。 標準ツールとテレメトリを使用して問題解決を試みます。 <!--The only scenarios where there is a need to access customer data is when there is an issue with the data that needs to be fixed. For example, if the data is copied to a wrong folder or is in an incorrect format and is likely to result in an upload or download failure, then Microsoft will try to access your data in the Azure datacenter.--> 

この問題を解決できず、データの調査または修復に Microsoft サポートが必要な場合は、Just-In-Time (JIT) ポータルを使用して昇格されたアクセス権を要求します。 JIP ポータルは、権限レベルの検証、および多要素認証を提供し、内部の Microsoft 承認者からの承認も含まれています。 承認者の例には、DevOps Manager などがあります。 

昇格されたアクセス権の要求が JIT ポータル経由で承認された後、ロックボックスが有効になっている場合は、Microsoft からデータにアクセスするための明示的な同意も求められます。 このアクセス許可は、ポータルのカスタマー ロックボックス サービスを介して要求および追跡されます。 

ロックボックスが有効になっていない場合、データにアクセスするための同意は必要ありません。


## <a name="prerequisites-for-access-request"></a>アクセス要求の前提条件

開始する前に次の点を確認します。

1. 次の指示に従って、Azure Data Box の注文を作成していること:
    1. [チュートリアル:Azure Data Box を注文する](data-box-deploy-ordered.md) (インポート注文用)。
    1. [チュートリアル:Azure Data Box を注文する](data-box-deploy-export-ordered.md) (エクスポート注文用)。

2. Data Box のカスタマー ロックボックスが構成済みであること。 これはオプトイン サービスです。 

    1. 現在、Data Box サービスのカスタマー ロックボックスはプレビュー段階です。 組織で Data Box のカスタマー ロックボックスを有効にするには、[Azure パブリック プレビュー用カスタマー ロックボックス](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Kwz02N6XVCoKNpxIpqE_hUNzlTUUNYVkozOVlFNVRSWDVHRkkwTFQyViQlQCN0PWcu)にサインアップします。
    2. カスタマー ロックボックスは、Developer レベル以上の Azure サポート プランをお持ちのすべてのお客様が自動的に利用できます。 <!--How do you enable Lockbox? change this for Azure Data Box, perhaps you need a different support plan When you have an eligible support plan, no action is required by you to enable Customer Lockbox. Customer Lockbox requests are initiated by a Microsoft engineer if this action is needed to progress a support ticket that is filed from somebody in your organization.-->

3. この問題に関するサービス要求またはサポート チケットが既に開かれていること。 サポート チケットの詳細については、[Data Box のサービス要求を提出する](data-box-disk-contact-microsoft-support.md)方法に関する記事を参照してください。


## <a name="track-approve-request-via-lockbox"></a>ロックボックスを使用した要求の追跡と承認

顧客データへのアクセス要求を追跡して承認するには、次の手順を実行します。

1. Microsoft が、Azure データセンターでのデータのアップロードまたはダウンロード中に問題が発生したことを検出します。 たとえば、Data Box の注文が、**データ コピー** の段階中に停止したとします。 

    サポート エンジニアは、サポート セッションを通じて Data Box に接続し、標準ツールとテレメトリを使用して問題のトラブルシューティングを試みます。 Data Box ディスクがロックされていて、共有にアクセスできない場合、サポート エンジニアはロックボックス要求を作成します。 
 
2. 要求が作成されると、通常、サブスクリプション管理者に通知が送られますが、通知先としてグループを構成することもできます。 

3. 承認待ちのロックボックス要求は、Azure portal に表示されます。 

    ![Azure portal 上の要求](./media/data-box-customer-lockbox/3-lockbox-request-azure-portal.png)

    ポータルからロックボックス要求を承認するには、 **[承認]** を選択します。

    ![要求の承認](./media/data-box-customer-lockbox/4-lockbox-request-details-azure-portal.png)


    要求が承認されると、デバイス ディスクのロックが解除され、サポート セッションで共有にアクセスできるようになります。

4. サポート エンジニアは、アップロードの問題を解決してから、サポート セッションを無効にします。

問題が解決すると、データ コピー ジョブは完了に向けて進行します。


## <a name="next-steps"></a>次のステップ

- [Microsoft Azure 用カスタマー ロックボックス](../security/fundamentals/customer-lockbox-overview.md)

<!--- [Approve, audit support access requests to VMs using Customer Lockbox for Azure](https://azure.microsoft.com/blog/approve-audit-support-access-requests-to-vms-using-customer-lockbox-for-azure/)-->