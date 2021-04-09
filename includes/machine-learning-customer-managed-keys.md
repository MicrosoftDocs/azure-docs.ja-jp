---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 2bba53410834aadce5627a34a759e65aa0e11c28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94573815"
---
> [!IMPORTANT]
> Cosmos DB インスタンスは、必要なリソースと共に __お使いのサブスクリプション__ の、Microsoft が管理するリソース グループ内に作成されます。 これは、この Cosmos DB インスタンスに対して課金されることを意味します。 マネージド リソース グループの名前は、`<AML Workspace Resource Group Name><GUID>` という形式で指定されます。 Azure Machine Learning ワークスペースでプライベート エンドポイントが使用される場合は、Cosmos DB インスタンスの仮想ネットワークも作成されます。 この VNet は、Cosmos DB と Azure Machine Learning の間の通信をセキュリティで保護するために使用されます。
> 
> * このグループに自動的に作成されたリソース、またはこの Cosmos DB インスタンスが含まれる __リソース グループを削除しないでください__。 Cosmos DB インスタンスなどのリソース グループを削除する必要がある場合は、それが使用されている Azure Machine Learning ワークスペースを削除してください。 関連付けられているワークスペースが削除されると、リソース グループ、Cosmos DB インスタンス、および自動的に作成されたその他のリソースが削除されます。
> * この Cosmos DB アカウントの既定の [__要求ユニット__](../articles/cosmos-db/request-units.md)は __8000__ に設定されています。
> * 作成された __Cosmos DB インスタンスでの使用のために、独自の VNet を指定することはできません__。 また、__仮想ネットワークを変更することはできません__。 たとえば、使用される IP アドレスの範囲を変更することはできません。