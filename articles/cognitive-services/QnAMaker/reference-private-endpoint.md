---
title: プライベート エンドポイントを設定する方法 - QnA Maker
description: QnA Maker マネージドで使用できるプライベート エンドポイントの作成について理解します。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 7907c81e45680de49f6653891fb4204a59db1002
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "101710551"
---
# <a name="private-endpoints"></a>プライベート エンドポイント

Azure プライベート エンドポイントは、Azure Private Link を使用するサービスにプライベートかつ安全に接続するネットワーク インターフェイスです。 現在、QnA Maker では、Azure Search サービスへのプライベート エンドポイントの作成がサポートされています。 この機能は、QnA Maker マネージドで使用できます。 

プライベート エンドポイントは、別サービスとして、[Azure Private Link](../../private-link/private-link-overview.md) によって提供されます。 コストの詳細については、[価格ページ](https://azure.microsoft.com/pricing/details/private-link/)を参照してください。 

## <a name="prerequisites"></a>前提条件
> [!div class="checklist"]
> * Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/cognitive-services/) を作成してください。
> * Azure portal で作成された QnA Maker [マネージド リソース](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)。 リソースを作成するしたに選択した Azure Active Directory ID、サブスクリプション、QnA リソース名を覚えておいてください。

## <a name="steps-to-enable-private-endpoint"></a>プライベート エンドポイントを有効にするための手順
1. Azure Search サービス インスタンスの QnA Maker マネージド サービスに *[共同作成者]* ロールを割り当てます。 この操作では、サブスクリプションへの "*所有者*" アクセス権が必要です。 サービス リソースの [ID] タブに移動して、ID を取得します。
![マネージド サービス ID](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. [Azure Search Service IAM]\(Azure Search サービスの IAM\) タブに移動して、上記の ID を *[共同作成者]* として追加します。![マネージド サービス IAM](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. *[ロールの割り当てを追加する]* をクリックします。ID を追加し、 *[保存]* をクリックします。
![マネージド ロールの割り当て](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. 次に、Azure Search サービス インスタンスの *[ネットワーク]* タブに移動し、エンドポイント接続データを *[パブリック]* から *[プライベート]* に切り替えます。 この操作は実行時間の長いプロセスであり、完了するまで最大で 30 分かかることがあります。 
![マネージド Azure 検索ネットワーク](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. QnA Maker マネージド サービスの *[ネットワーク]* タブに移動し、 *[許可するアクセス元]* で *[選択したネットワークとプライベート エンドポイント]* オプションを選択し、 *[保存]* をクリックします。 
![マネージド QnA Maker ネットワーク](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

これにより、QnA Maker サービスと Azure Cognitive Search サービス インスタンスの間にプライベート エンドポイント接続が確立されます。 Azure Search サービス インスタンスの *[ネットワーク]* タブで、プライベート エンドポイント接続を確認できます。 操作全体が完了したら、QnA Maker サービスを使用できます。 
![マネージド ネットワーク サービス](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>サポートの詳細
 * QnAMaker サービスへのプライベート アクセスを有効にした後で Azure Search サービスを変更することはサポートされていません。 プライベート アクセスを有効にした後で [構成] タブで Azure Search サービスを変更すると、QnAMaker サービスを使用できなくなります。
 * プライベート エンドポイント接続を確立した後で Azure Search サービス ネットワークを [パブリック] に切り替えると、QnAMaker サービスを使用できなくなります。 プライベート エンドポイント接続を機能させるには、Azure Search サービス ネットワークが [プライベート] になっている必要があります。