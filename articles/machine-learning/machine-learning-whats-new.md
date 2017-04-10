---
title: "Azure Machine Learning の新機能 | Microsoft Docs"
description: "Azure Machine Learning で利用できる新しい機能。"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: ddc716ed-2615-4806-bf27-6c9a5662a7f2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: v-donglo
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 551b977b90612ddbfa1514a9c2358ebf8179c385
ms.lasthandoff: 03/29/2017


---
# <a name="whats-new-in-azure-machine-learning"></a>Azure Machine Learning の新機能

### <a name="the-march-2017-release-of-microsoft-azure-machine-learning-updates-provides-the-following-feature"></a>2017年 3 月、Microsoft Azure Machine Learning の更新プログラムで次の機能が提供されました。



* Azure Machine Learning BES ジョブ専用の容量

    Machine Learning の Batch プール処理では、[Azure Batch](../batch/batch-technical-overview.md) サービスを使用して、Azure Machine Learning バッチ実行サービスのスケールをユーザーが管理できるようにしています。 Batch プール処理では、バッチ ジョブを送信できる Azure Batch プールを作成し、予測可能な方法で実行することができます。

    詳細については、「[Machine Learning ジョブ用の Azure Batch サービス](machine-learning-dedicated-capacity-for-bes-jobs.md)」を参照してください。


### <a name="the-august-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>2016年 8 月、Microsoft Azure Machine Learning の更新プログラムで次の機能が提供されました。
* クラシック Web サービスを、新しい [Microsoft Azure Machine Learning Web サービス](https://services.azureml.net/) ポータル (Web サービスのすべての側面を管理するための 1 つの場所) で管理できるようになりました。    
  * Web サービスの [使用状況の統計](machine-learning-manage-new-webservice.md)が提供されます。
  * サンプル データを使用して Azure Machine Learning リモート要求呼び出しのテストが簡略化されます。
  * サンプル データとジョブの送信履歴が表示される新しいバッチ実行サービス テスト ページが提供されます。
  * エンドポイントの管理が容易になります。

### <a name="the-july-2016-release-of-microsoft-azure-machine-learning-updates-provide-the-following-features"></a>2016年 7 月、Microsoft Azure Machine Learning の更新プログラムで次の機能が提供されました。
* Web サービスは Azure のリソースとして [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) インターフェイスから管理されるようになり、次の拡張機能を使用できるようになりました。
  * Resource Manager ベースの Web サービスをデプロイおよび管理するための新しい [REST API](https://msdn.microsoft.com/library/azure/Dn950030.aspx)
  * Web サービスのすべての側面を管理するための 1 つの場所を提供する新しい [Microsoft Azure Machine Learning Web サービス](https://services.azureml.net/) ポータル
* Web サービスの Resource Manager のリソース プロバイダーを活用する Resource Manager ベースの API を使用する、新しい、サブスクリプション ベースの複数リージョン対応 Web サービス デプロイ モデルが組み込まれました。
* 新たに [料金プラン](https://azure.microsoft.com/pricing/details/machine-learning/) と、新しい Resource Manager RP を課金の目的で使用するプラン管理機能が導入されました。
  * 各リージョンにサブスクリプションを作成せずに、[Web サービスを複数のリージョンにデプロイ](machine-learning-how-to-deploy-to-multiple-regions.md)できるようになりました。
* Web サービスの [使用状況の統計](machine-learning-manage-new-webservice.md)が提供されるようになりました。
* サンプル データを使用して Azure Machine Learning リモート要求呼び出しのテストが簡略化されます。
* サンプル データとジョブの送信履歴が表示される新しいバッチ実行サービス テスト ページが提供されます。

さらに、Machine Learning Studio が更新され、新しい Web サービス モデルをデプロイするか、従来の Web サービス モデルのデプロイを継続できるようになりました。 


