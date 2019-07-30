---
title: Azure Media Services v3 アカウントの管理 | Microsoft Docs
description: Azure でメディア コンテンツの管理、暗号化、エンコード、分析、およびストリーミングを開始するには、Media Services アカウントを作成する必要があります。 この記事では、Azure Media Services v3 アカウントを管理する方法について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: fa9720c2c29af184016d2903e60520e701b4cf79
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670680"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Azure Media Services v3 アカウントの管理

Azure でメディア コンテンツの管理、暗号化、エンコード、分析、およびストリーミングを開始するには、Media Services アカウントを作成する必要があります。 Media Services アカウントの作成では、Azure Storage アカウント リソースの名前を指定する必要があります。 指定されたストレージ アカウントは、Media Services アカウントに関連付けられます。 Media Services アカウントおよび関連するすべてのストレージ アカウントは、同じ Azure サブスクリプションに存在する必要があります。 詳細については、[ストレージ アカウント](storage-account-concept.md)に関するページを参照してください。

## <a name="moving-a-media-services-account-between-subscriptions"></a>サブスクリプション間での Media Services アカウントの移動 

Media Services アカウントを新しいサブスクリプションに移動する必要がある場合は、まず、Media Services アカウントを含むリソース グループ全体を新しいサブスクリプションに移動する必要があります。 接続されている次のすべてのリソースを移動する必要があります。Azure Storage アカウント、Azure CDN プロファイルなど。詳細については、「 [新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/resource-group-move-resources.md)」を参照してください。 Azure の他のリソースの場合と同様に、リソース グループの移動にはその完了まで時間がかかることがあります。

> [!NOTE]
> Media Services v3 では、マルチ テナント モデルをサポートしています。

### <a name="considerations"></a>考慮事項

* 別のサブスクリプションに移行する前に、ご利用のアカウント内のすべてのデータのバックアップを作成します。
* ストリーミング エンドポイントとライブ ストリーミング リソースをすべて停止する必要があります。 リソース グループの移動中、ユーザーはコンテンツにアクセスできません。 

> [!IMPORTANT]
> 移動が正常に完了するまで、ストリーミング エンドポイントを開始しないでください。

### <a name="troubleshoot"></a>トラブルシューティング 

リソース グループの移動後に、Media Services アカウントまたは関連付けられた Azure Storage アカウントが "切断" される場合、ストレージ アカウント キーの交換を試してください。 ストレージ アカウント キーの交換で Media Services アカウントの "切断" 状態が解決されない場合は、Media Services アカウントの [サポート + トラブルシューティング] メニューから新しいサポート リクエストを提出してください。  

## <a name="next-steps"></a>次の手順

[アカウントの作成](create-account-cli-quickstart.md)
