---
title: Azure Media Services v2 アカウントの管理 | Microsoft Docs
description: Azure でメディア コンテンツの管理、暗号化、エンコード、分析、およびストリーミングを開始するには、Media Services アカウントを作成する必要があります。 この記事では、Azure Media Services v2 アカウントを管理する方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.openlocfilehash: b4c19b1f502d079d7dfcc1edef4674d21f78ac3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67622074"
---
# <a name="manage-azure-media-services-v2-accounts"></a>Azure Media Services v2 アカウントの管理

Azure でメディア コンテンツの管理、暗号化、エンコード、分析、およびストリーミングを開始するには、Media Services アカウントを作成する必要があります。 Media Services アカウントの作成では、Azure Storage アカウント リソースの名前を指定する必要があります。 指定されたストレージ アカウントは、Media Services アカウントに関連付けられます。 Media Services アカウントおよび関連するすべてのストレージ アカウントは、同じ Azure サブスクリプションに存在する必要があります。  

## <a name="moving-a-media-services-account-between-subscriptions"></a>サブスクリプション間での Media Services アカウントの移動 

Media Services アカウントを新しいサブスクリプションに移動する必要がある場合は、まず、Media Services アカウントを含むリソース グループ全体を新しいサブスクリプションに移動する必要があります。 接続されている次のすべてのリソースを移動する必要があります。Azure Storage アカウント、Azure CDN プロファイルなど。詳細については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/resource-group-move-resources.md)」を参照してください。 Azure の他のリソースの場合と同様に、リソース グループの移動にはその完了まで時間がかかることがあります。

Media Services v2 では、マルチ テナント モデルはサポートされていません。 新しいテナント内のサブスクリプションに Media Services アカウントを移動する必要がある場合は、その新しいテナント内に新しい Azure Active Directory (Azure AD) アプリケーションを作成します。 次にご自分のアカウントを新しいテナント内のサブスクリプションに移動します。 テナント移動が完了したら、v2 API を使用して Media Services アカウントにアクセスするために、新しいテナントから Azure AD アプリケーションの使用を開始することができます。 

> [!IMPORTANT]
> Media Services v2 API にアクセスするには、[Azure AD 認証](media-services-portal-get-started-with-aad.md)に関する情報をリセットする必要があります。  
### <a name="considerations"></a>考慮事項

* 別のサブスクリプションに移行する前に、ご利用のアカウント内のすべてのデータのバックアップを作成します。
* ストリーミング エンドポイントとライブ ストリーミング リソースをすべて停止する必要があります。 リソース グループの移動中、ユーザーはコンテンツにアクセスできません。 

> [!IMPORTANT]
> 移動が正常に完了するまでストリーミング エンドポイントを開始しないでください。

### <a name="troubleshoot"></a>トラブルシューティング 

リソース グループの移動後に、Media Services アカウントまたは関連付けられた Azure Storage アカウントが "切断" される場合、ストレージ アカウント キーの交換を試してください。 ストレージ アカウント キーの交換で Media Services アカウントの "切断" 状態が解決されない場合は、Media Services アカウントの [サポート + トラブルシューティング] メニューから新しいサポート リクエストを提出してください。  
 
## <a name="next-steps"></a>次の手順

[アカウントの作成](media-services-portal-create-account.md)
