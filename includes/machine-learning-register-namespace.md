---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: e92d3ac9ed4330cc1680428a426e881538cb0e78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102623192"
---
* 新しいワークスペースを作成する場合は、ワークスペースに必要なサービスを自動的に作成するか、既存のサービスを使用することができます。 ワークスペースとは __異なる Azure サブスクリプション の既存のサービス__ を使用する場合は、それらのサービスを含むサブスクリプションに Azure Machine Learning 名前空間を登録する必要があります。 たとえば、サブスクリプション B のストレージ アカウントを使用するサブスクリプション A でワークスペースを作成する場合、ワークスペースでストレージ アカウントを使用するには、Azure Machine Learning 名前空間がサブスクリプション B に登録されている必要があります。

    Azure Machine Learning のリソース プロバイダーは、__Microsoft.MachineLearningServices__ です。 登録されているかどうかの確認方法、および登録方法の詳細については、「[Azure リソース プロバイダーと種類](../articles/azure-resource-manager/management/resource-providers-and-types.md)」の記事を参照してください。

    > [!IMPORTANT]
    > これは、ワークスペースの作成時に提供されるリソースである、Azure Storage アカウント、Azure Container Register、Azure Key Vault、Application Insights にのみ適用されます。
