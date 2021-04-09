---
title: Azure マネージド ID を使用して DevTest Labs で環境を作成する | Microsoft Docs
description: Azure でマネージ ID を使用して、Azure DevTest Labs のラボに環境をデプロイする方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f3e4b4d7030eb26c25b291e03caaa430d1979c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98185786"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Azure マネージド ID を使用してラボに環境をデプロイする 

ラボの所有者は、マネージド ID を使用してラボに環境をデプロイできます。 この機能は、環境にキー コンテナー、共有イメージ ギャラリー、ネットワークなど、環境のリソース グループの外部にある Azure リソースが含まれている場合、またはそれらのリソースを参照している場合に役立ちます。 これにより、その環境のリソース グループに限定されないサンドボックス環境を作成できるようになります。

> [!NOTE]
> 現在、単一のユーザー割り当て ID がラボごとにサポートされています。 

## <a name="prerequisites"></a>前提条件

- [Azure portal を使用してユーザー割り当てマネージド ID を作成、一覧表示、削除したり、それにロールを割り当てたりする](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)。 
    
    ラボと同じリージョンおよびサブスクリプションでマネージド ID が作成されたことを確認します。 マネージド ID のリソース グループは同じである必要がありません。

## <a name="use-azure-portal"></a>Azure Portal の使用

このセクションでは、ラボの所有者として Azure portal を使用して、ユーザー マネージド ID をラボに追加します。 

1. [Azure portal](https://portal.azure.com) にサインインする
1. **DevTest Labs** を見つけます。
1. ラボの一覧から、目的のラボを選択します。
1. **[構成とポリシー]**  ->  **[ID (プレビュー)]** を選択します。 
1. ユーザー割り当て ID を追加するには、 **[ユーザー割り当て済み]** を選択します。
1. **[追加]** を押します。
1. 自分で作成した、あるいはアクセス権が与えられている既存ユーザーをドロップダウンから選択します。
 
    ![ユーザー マネージド ID の追加](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. ページ上部にある **[保存]** を押します。

    保存すると、ラボはすべてのラボ環境をデプロイするときにこの ID を使用します。 一覧から ID を選択することで、Azure の ID リソースにアクセスすることもできます。 

ラボに追加された ID が、環境でアクセスする必要がある外部リソースへのアクセス許可を持っている限り、ラボ所有者は、環境のデプロイ時に特別な操作を行う必要はありません。 

ラボに割り当てられているユーザー マネージド ID を変更するには、最初にラボに関連付けられている ID を削除してから、ラボに別の ID を追加します。 ラボに関連付けられている ID を削除するには、**[...] (省略記号)** を選択し、**[削除]** をクリックします。 

## <a name="use-api"></a>API を使用する

1. ID を作成したら、この ID のリソース ID をメモしておきます。 これは次のサンプルのようになります。 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
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
        "properties":{
            "identityUsageType":"Environment"
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
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
ユーザー割り当て ID がラボに追加されると、Azure DevTest Labs サービスは Azure Resource Manager 環境をデプロイするときにそれを使用します。 たとえば、Resource Manager テンプレートで外部の共有イメージ ギャラリーのイメージにアクセスする必要がある場合は、ラボに追加した ID に、共有イメージ ギャラリー リソースに最低限必要なアクセス許可があることを確認してください。 
