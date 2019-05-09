---
title: Azure Batch サービス ジョブ
titleSuffix: Azure Machine Learning Studio
description: Machine Learning Studio ジョブ用の Azure Batch サービスの概要。 Batch プール処理では、バッチ ジョブを送信できるプールを作成できます。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18, previous-title='Dedicated capacity for batch execution service jobs - Azure Machine Learning Studio | Microsoft Docs'
ms.date: 04/19/2017
ms.openlocfilehash: 24efa3caba3918a38c09b1c921c600b117dedbc1
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57871377"
---
# <a name="azure-batch-service-for-azure-machine-learning-studio-jobs"></a>Azure Machine Learning Studio ジョブ用の Azure Batch サービス

Machine Learning の Batch プール処理では、Azure Machine Learning バッチ実行サービスのスケールをユーザーが管理できるようにしています。 Machine Learning のクラシック バッチ処理はマルチ テナント環境で実行され、送信できる同時実行ジョブの数が制限されており、ジョブは先入れ先出しベースでキューに配置されます。 この不確定性により、ユーザーはジョブがいつ実行されるかを正確に予測することはできません。

Batch プール処理では、バッチ ジョブを送信できるプールを作成できます。 プールのサイズと、ジョブの送信先となるプールはユーザーが制御します。 BES ジョブは独自の処理領域で実行されるため、処理パフォーマンスを予測し、送信する処理の負荷に対応するリソース プールを作成することができます。

> [!NOTE]
> プールを作成するには、新しい Resource Manager ベースの Machine Learning Web サービスが必要です。 一度作成したら、プールで任意の BES Web サービス (新しい Resource Manager ベースとクラシックの両方) を実行できます。

## <a name="how-to-use-batch-pool-processing"></a>Batch プール処理を使用する方法

現時点では、Azure Portal で Batch プール処理を構成することはできません。 Batch プール処理を使用するには、以下が必要です。

-   CSS に連絡して Batch プール アカウントを作成し、プール サービスの URL と承認キーを取得する
-   新しい Resource Manager ベースの Web サービスと料金プランを作成する

アカウントを作成するには、Microsoft カスタマー サービス & サポート (CSS) に連絡し、サブスクリプション ID を伝えます。 CSS が協力して、シナリオに適した容量を決定します。 次に、CSS は、作成できるプールの最大数と各プール内に配置できる仮想マシン (VM) の最大数を設定してアカウントを構成します。 アカウントが構成されると、プール サービスの URL と承認キーが提供されます。

アカウントが作成されたら、プール サービスの URL と承認キーを使用して Batch プールのプール管理操作を実行します。

![Batch プール サービスのアーキテクチャ](./media/dedicated-capacity-for-bes-jobs/pool-architecture.png)

プールを作成するには、CSS から提供されたプール サービスの URL でプール作成操作を呼び出します。 プールを作成する場合は、VM の数と新しい Resource Manager ベースの Machine Learning Web サービスの swagger.json の URL を指定します。 この Web サービスは、料金の関連付けを確立するために提供されます。 Batch プール サービスでは、swagger.json を使用して、プールを料金プランに関連付けます。 プールで任意の BES Web サービス (新しい Resource Manager ベースとクラシックの両方) を実行できます。

新しい Resource Manager ベースの Web サービスを使用できますが、ジョブの料金はそのサービスに関連付けられている料金プランに基づいて課金される点に注意してください。 Batch プール ジョブの実行専用の Web サービスと新しい料金プランを作成できます。

Web サービスの作成の詳細については、「[Azure Machine Learning Web サービスをデプロイする](publish-a-machine-learning-web-service.md)」を参照してください。

プールを作成したら、Web サービスのバッチ要求 URL を使用して BES ジョブを送信します。 プールに送信するか、クラシック バッチ処理に送信するかを選択できます。 Batch プール処理にジョブを送信するには、ジョブの送信要求本文に次のパラメーターを追加します。

"AzureBatchPoolId":"&lt;プール ID&gt;"

パラメーターを追加しない場合、ジョブはクラシック バッチ処理環境で実行されます。 プールに使用可能なリソースがある場合、ジョブはすぐに実行が開始されます。 プールに空きリソースがない場合、リソースが利用可能になるまでジョブはキューに配置されます。

頻繁にプールの容量に到達するため、容量の増加が必要な場合は、CSS に連絡し、担当者と協力してクォータを増やすことができます。

要求の例:

https://ussouthcentral.services.azureml.net/subscriptions/80c77c7674ba4c8c82294c3b2957990c/services/9fe659022c9747e3b9b7b923c3830623/jobs?api-version=2.0

```json
{

    "Input":{
    
        "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpoint
        =https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://zhguim
        l.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;;",
        
        "BaseLocation":null,
        
        "RelativeLocation":"testint/AdultCensusIncomeBinaryClassificationDataset.csv",
        
        "SasBlobToken":null
    
    },
    
    "GlobalParameters":{ },
    
    "Outputs":{
    
        "output1":{
        
            "ConnectionString":"DefaultEndpointsProtocol=https;BlobEndpoint=https://sampleaccount.blob.core.windows.net/;TableEndpo
            int=https://sampleaccount.table.core.windows.net/;QueueEndpoint=https://sampleaccount.queue.core.windows.net/;FileEndpoint=https://sampleaccount.file.core.windows.net/;AccountName=sampleaccount;AccountKey=&lt;Key&gt;",
            "BaseLocation":null,
            "RelativeLocation":"testintoutput/testint\_results.csv",
            
            "SasBlobToken":null
        
        }
    
    },
    
    "AzureBatchPoolId":"8dfc151b0d3e446497b845f3b29ef53b"

}
```

## <a name="considerations-when-using-batch-pool-processing"></a>Batch プール処理を使用する際の考慮事項

Batch プール処理は、常時課金対象サービスで、Resource Manager ベースの料金プランに関連付ける必要があります。 プールが実行されているコンピューティング時間に対してのみ課金されます。その時間にプールで実行されたジョブの数は関係ありません。 プールを作成した場合、プールでバッチ ジョブが実行されていなくても、プールが削除されるまで、プール内の各仮想マシンのコンピューティング時間に対して課金されます。 仮想マシンに対する課金は、プロビジョニングが完了した時点で開始し、削除された時点で停止します。 [Machine Learning の料金ページ](https://azure.microsoft.com/pricing/details/machine-learning/)に記載されているいずれかのプランを使用できます。

課金の例:

仮想マシンが 2 台ある Batch プールを作成し、24 時間後に削除した場合、その期間中に実行されたジョブの数に関係なく、料金プランに基づいて 48 コンピューティング時間分の金額が引き落とされます。

仮想マシンが 4 台ある Batch プールを作成し、12 時間後に削除した場合も、料金プランに基づいて 48 コンピューティング時間分の金額が引き落とされます。

ジョブの状態をポーリングして、ジョブが完了した日時を確認することをお勧めします。 すべてのジョブの実行が完了したら、プールのサイズ変更操作を呼び出して、プール内の仮想マシンの数を 0 に設定します。 プールのリソースが不足しており、(たとえば、別の料金プランに基づいて課金される) 新しいプールを作成する必要がある場合は、すべてのジョブの実行が完了した時点で代わりにプールを削除することができます。


| **Batch プール処理を使用する状況**    | **クラシック バッチ処理を使用する状況**  |
|---|---|
|多数のジョブを実行する必要がある<br>または<br/>ジョブがすぐに実行される必要がある<br/>または<br/>保証されたスループットが必要である (たとえば、所定の時間枠でたくさんのジョブを実行し、ニーズに合わせてコンピューティング リソースをスケールアウトする必要がある)    | 少数のジョブを実行する<br/>と<br/> ジョブがすぐに実行される必要がない |
