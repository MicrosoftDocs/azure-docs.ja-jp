---
title: Azure マネージド ID を使用して DevTest Labs で環境を作成する | Microsoft Docs
description: Azure でマネージ ID を使用して、Azure DevTest Labs のラボに環境をデプロイする方法について説明します。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: spelluru
ms.openlocfilehash: d1dd059f1a6f9ce96b27d4fe1f214978dfc06a8f
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71816001"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Azure マネージド ID を使用してラボに環境をデプロイする 
ラボの所有者は、マネージド ID を使用してラボに環境をデプロイできます。 この機能は、環境にキー コンテナー、共有イメージ ギャラリー、ネットワークなど、環境のリソース グループの外部にある Azure リソースが含まれている場合、またはそれらのリソースを参照している場合に役立ちます。 これにより、その環境のリソース グループに限定されないサンドボックス環境を作成できるようになります。

> [!NOTE]
> 現在、単一のユーザー割り当て ID がラボごとにサポートされています。 

## <a name="prerequisites"></a>前提条件
- [Azure portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 

## <a name="use-azure-portal"></a>Azure Portal の使用
このセクションでは、ラボの所有者として Azure portal を使用して、ユーザー マネージド ID をラボに追加します。 

1. ラボのページで、 **[構成とポリシー]** を選択します。 
1. **[設定]** セクションで **[ID]** を選択します。
1. ユーザー割り当て ID を追加するために、ツールバーの **[追加]** を選択します。 
1. あらかじめ設定されているドロップダウン リストから **ID** を選択します。
1. **[OK]** を選択します。

    ![ユーザー マネージド ID の追加](./media/use-managed-identities-environments/add-user-managed-identity.png)
2. 追加したユーザー マネージド ID が一覧に表示されます。 

    ![ユーザー マネージド ID の一覧](./media/use-managed-identities-environments/identity-in-list.png)

保存すると、ラボはすべてのラボ環境をデプロイするときにこの ID を使用します。 一覧から ID を選択することで、Azure の ID リソースにアクセスすることもできます。 

ラボに追加された ID が、環境でアクセスする必要がある外部リソースへのアクセス許可を持っている限り、ラボ所有者は、環境のデプロイ時に特別な操作を行う必要はありません。 

ラボに割り当てられているユーザー マネージド ID を変更するには、最初にラボに関連付けられている ID を削除してから、ラボに別の ID を追加します。 ラボに関連付けられている ID を削除するには、 **[...] (省略記号)** を選択し、 **[削除]** をクリックします。 

![ユーザー マネージド ID の一覧](./media/use-managed-identities-environments/replace-identity.png)  

## <a name="use-api"></a>API を使用する

1. ID を作成したら、この ID のリソース ID をメモしておきます。 これは次のサンプルのようになります。 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`
1. 次の例のように、新しい `ServiceRunner` リソースをラボに追加するには、PUT Https メソッドを実行します。 サービス ランナー リソースは、DevTest Labs でマネージド ID を管理および制御するためのプロキシ リソースです。 サービス ランナー名は任意の有効な名前にすることができますが、マネージド ID リソースの名前を使用することをお勧めします。 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
    }
    ```
 
    次に例を示します。 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
    }
    ```
 
ユーザー割り当て ID がラボに追加されると、Azure DevTest Labs サービスは Azure Resource Manager 環境をデプロイするときにそれを使用します。 たとえば、Resource Manager テンプレートで外部の共有イメージ ギャラリーのイメージにアクセスする必要がある場合は、ラボに追加した ID に、共有イメージ ギャラリー リソースに最低限必要なアクセス許可があることを確認してください。 
