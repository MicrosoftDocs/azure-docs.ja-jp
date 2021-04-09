---
title: Azure API Management をリージョン間で移行する方法
description: API Management インスタンスを 1 つのリージョンから別のリージョンに移行する方法を学習します。
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 0eed2328aca78402c5f4691bb9b3d07d4f36472e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86250228"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Azure API Management をリージョン間で移行する方法
API Management インスタンスを 1 つの Azure リージョンから別のリージョンに移行するには、[バックアップと復元](api-management-howto-disaster-recovery-backup-restore.md)機能を使用できます。 ソース リージョンとターゲット リージョンで同じ API Management 価格レベルを選択する必要があります。 

> [!NOTE]
> 種類が異なるクラウド間で移行を行う場合、バックアップと復元は機能しません。 そのため、リソースを[テンプレートとして](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)エクスポートする必要があります。 次に、エクスポートしたテンプレートをターゲットの Azure リージョンに合わせて調整し、リソースを再作成します。 

## <a name="option-1-use-a-different-api-management-instance-name"></a>オプション 1: 別の API Management インスタンス名を使用する

1. ターゲット リージョンで、ソース API Management インスタンスと同じ価格レベルで新しい API Management インスタンスを作成します。 新しいインスタンスには別の名前を付ける必要があります。 
1. 既存の API Management インスタンスをストレージ アカウントにバックアップします。
1. 手順 2 で作成したバックアップを、手順 1 で新しいリージョンに作成した新しい API Management インスタンスに復元します。
1. ソース リージョンの API Management インスタンスを指すカスタム ドメインがある場合は、新しい API Management インスタンスを指すようにカスタム ドメインの CNAME を更新します。 


## <a name="option-2-use-the-same-api-management-instance-name"></a>オプション 2:同じ API Management インスタンス名を使用する

> [!NOTE]
> このオプションでは、移行中にダウンタイムが発生します。

1. ソース リージョンの API Management インスタンスをストレージ アカウントにバックアップします。
1. ソース リージョンの API Management を削除します。 
1. ターゲット リージョンで、ソース リージョンのインスタンスと同じ名前を持つ新しい API Management インスタンスを作成します。
1. 手順 1 で作成したバックアップを、ターゲット リージョンの新しい API Management インスタンスに復元します。  


## <a name="next-steps"></a><a name="next-steps"> </a>次の手順
* バックアップと復元機能の詳細については、[ディザスター リカバリーを実装する方法](api-management-howto-disaster-recovery-backup-restore.md)に関するページを参照してください。
* Azure リソースの移行の詳細については、[Azure のリージョン間の移行に関するガイダンス](https://github.com/Azure/Azure-Migration-Guidance)を参照してください。
* [ご自分のクラウドの支出を最適化して節約する](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
