---
title: インクルード ファイル
description: インクルード ファイル
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/14/2019
ms.openlocfilehash: 11287ee5ca0df3e51bca20b39c35eb5ce33b88bd
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231068"
---
1. 使用する Azure サブスクリプションの資格情報を使って、[Azure portal](https://portal.azure.com/) にサインインします。 

1. Azure portal の左上隅にある **[+ リソースの作成]** を選択します。

      ![新しいリソースを作成](media/aml-create-in-portal/portal-create-resource.png)

1. 検索バーを使用して、**Machine Learning service ワークスペース**を見つけます。

1. **[Machine Learning service ワークスペース]** を選択します。

1. **[Machine Learning サービス ワークスペース]** ウィンドウで、 **[作成]** を選択して開始します。

1. 新しいワークスペースを構成するには、次の情報を指定します。

   フィールド|説明 
   ---|---
   ワークスペース名 |ワークスペースを識別する一意名を入力します。 この例では、**docs-ws** を使用します。 名前は、リソース グループ全体で一意である必要があります。 覚えやすく、他のユーザーが作成したワークスペースと区別しやすい名前を使用します。  
   Subscription |使用する Azure サブスクリプションを選択します。
   Resource group | サブスクリプションの既存のリソース グループを使用するか、任意の名前を入力して新しいリソース グループを作成します。 リソース グループは、Azure ソリューションの関連するリソースを保持します。 この例では、**docs-aml** を使用します。 
   Location | ユーザーとデータ リソースに最も近い場所を選択し、ワークスペースを作成します。

1. ワークスペースの構成が完了したら、 **[作成]** を選択します。 

   > [!Warning] 
   > クラウドへのワークスペースの作成には数分かかる場合があります。

   プロセスが完了すると、デプロイ成功メッセージが表示されます。 
 
 1. 新しいワークスペースを表示するには、 **[Go to resource]\(リソースに移動\)** を選択します。

