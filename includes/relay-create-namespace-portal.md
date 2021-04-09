---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 500db9f6d299ea81b1f1b1be864df5d91ba1eae7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "76021293"
---
1. [Azure portal][Azure portal] にサインインします。
1. **[リソースの作成]** を選択します。 次に、 **[統合]**  > 、 **[リレー]** の順に選択します。 一覧に **[リレー]** がない場合、右上隅にある **[すべて表示]** を選択してください。
1. **[作成]** を選択し、 **[名前]** フィールドに名前空間の名前を入力します。 その名前が使用できるかどうかが Azure portal で確認されます。
1. 名前空間を作成する Azure サブスクリプションを選択します。
1. [[リソース グループ]](../articles/azure-resource-manager/management/manage-resource-groups-portal.md) で、名前空間を追加する既存のリソース グループを選択するか、新しいリソース グループを作成します。  
1. 名前空間をホストする国またはリージョンを選択します。

    ![[名前空間の作成]][create-namespace]

1. **［作成］** を選択します Azure portal によってサービス名前空間が作成され、有効になります。 数分後に、アカウントのリソースがプロビジョニングされます。

### <a name="get-management-credentials"></a>管理資格情報を取得する

1. **[すべてのリソース]** を選択し、新しく作成した名前空間の名前を選択します。
1. **[共有アクセス ポリシー]** を選択します。  
1. **[共有アクセス ポリシー]** の下で、 **[RootManageSharedAccessKey]** を選択します。
1. **[SAS ポリシー:RootManageSharedAccessKey]** の下で、 **[プライマリ接続文字列]** の横の **[コピー]** ボタンを選択します。 これで、後で使用できるように接続文字列がクリップボードにコピーされます。 この値をメモ帳などに一時的に貼り付けます。
1. 前の手順を繰り返し、 **[主キー]** の値をコピーして、後で使用するために一時的な場所に貼り付けます。  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
