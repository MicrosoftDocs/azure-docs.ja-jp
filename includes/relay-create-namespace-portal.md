---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: bbcf38ab54632144920b729f433c222f426d96dc
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825733"
---
1. [Azure Portal][Azure portal] にサインインします。
2. 左側のメニューで、**[+ リソースの作成]** を選択します。 次に、**[統合]**、**[リレー]** の順に選択します。 一覧に **[リレー]** がない場合、右上隅にある **[すべて表示]** を選択してください。 
3. **[名前空間の作成]** の下に、名前空間の名前を入力します。 その名前が使用できるかどうかがすぐに自動で確認されます。
4. **[サブスクリプション]** ボックスで、名前空間を作成する Azure サブスクリプションを選択します。
5. [[リソース グループ]](../articles/azure-resource-manager/manage-resource-groups-portal.md) ボックスで、名前空間を配置する既存のリソース グループを選択するか、新しいリソース グループを作成します。  
6. **[場所]** で、名前空間をホストする国またはリージョンを選択します。
   
    ![[名前空間の作成]][create-namespace]
7. **作成**を選択します。 システムによってサービス名前空間が作成され、有効になります。 数分後に、アカウントのリソースがプロビジョニングされます。

### <a name="get-management-credentials"></a>管理資格情報を取得する

1. **[すべてのリソース]** を選択し、新しく作成した名前空間の名前を選択します。
2. リレー名前空間の下で、**[共有アクセス ポリシー]** を選択します。  
3. **[共有アクセス ポリシー]** の下で、**[RootManageSharedAccessKey]** を選択します。
   
    ![connection-info][connection-info]
4. **[ポリシー: RootManageSharedAccessKey]** の下で、**[接続文字列 – 主キー]** の横の **[コピー]** ボタンを選択します。 これで、後で使用できるように接続文字列がクリップボードにコピーされます。 この値をメモ帳などに一時的に貼り付けます。
   
    ![connection-string][connection-string]

5. 前の手順を繰り返し、**[主キー]** の値をコピーして、後で使用するために一時的な場所に貼り付けます。  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
