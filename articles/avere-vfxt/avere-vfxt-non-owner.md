---
title: Avere vFXT の非所有者の回避策 - Azure
description: サブスクリプション所有者のアクセス許可を持たないユーザーが Avere vFXT for Azure をデプロイできるようにする回避策
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 0d9b1060ee35af6cbc2e1b95b0f7813072c52d2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85505377"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Avere vFXT のデプロイを所有者以外に承認する

以下の手順は、サブスクリプション所有者特権のないユーザーが Avere vFXT for Azure システムを作成できるようにするための回避策です 

(Avere vFXT システムをデプロイするための推奨される方法は、「[Prepare to create the Avere vFXT (Avere vFXT の作成準備)](avere-vfxt-prereqs.md)」で説明されているように、所有者特権を持つユーザーに作成手順を実行してもらう方法です)。  

この回避策では、クラスターをインストールするための十分なアクセス許可をユーザーに付与する追加のアクセス ロールを作成します。 このロールは、サブスクリプション所有者が作成したうえで、適切なユーザーに割り当てる必要があります。

サブスクリプション所有者は、Avere vFXT マーケットプレース イメージの[利用規約に同意](avere-vfxt-prereqs.md)する必要もあります。

> [!IMPORTANT]
> 以下のすべての手順は、クラスターに使用されるサブスクリプションに対して所有者特権を持つユーザーが行う必要があります。

1. 以下の行をコピーし、ファイル (たとえば `averecreatecluster.json`) に保存します。 `AssignableScopes` ステートメントでは、自分のサブスクリプション ID を使用します。

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",

           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. 次のコマンドを実行して、ロールを作成します。

   `az role definition create --role-definition <PATH_TO_FILE>`

    例:

    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. クラスターを作成するユーザーにこのロールを割り当てます。

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

このプロセスの完了後、このロールに割り当てられるすべてのユーザーに、サブスクリプションに対する以下のアクセス許可が付与されます。

* ネットワーク インフラストラクチャを作成および構成する
* クラスター コントローラーを作成する
* クラスター コントローラーからクラスター作成スクリプトを実行してクラスターを作成する
