---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 493c674fa161bf33436e560fdcbb9196410db931
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102209472"
---
> [!TIP]
> ログインすると、ご使用のアカウントに関連付けられているサブスクリプションの一覧が表示されます。 `isDefault: true` が示されているサブスクリプション情報が、Azure CLI コマンドに対して現在アクティブになっているサブスクリプションです。 このサブスクリプションは、Azure Machine Learning ワークスペースが含まれているものと同じである必要があります。 サブスクリプション ID を確認するには、[Azure portal](https://portal.azure.com) からワークスペースの概要ページにアクセスします。 SDK を使用してワークスペース オブジェクトからサブスクリプション ID を取得することもできます。 たとえば、「 `Workspace.from_config().subscription_id` 」のように入力します。
> 
> 別のサブスクリプションを選択するには、`az account set -s <subscription name or ID>` コマンドを使用して、切り替えるサブスクリプション名または ID を指定します。 サブスクリプションの選択の詳細については、「[複数の Azure サブスクリプションの使用](/cli/azure/manage-azure-subscriptions-azure-cli)」を参照してください。