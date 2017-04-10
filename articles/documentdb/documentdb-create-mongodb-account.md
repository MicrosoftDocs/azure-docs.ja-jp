---
redirect_url: https://docs.microsoft.com/azure/documentdb/documentdb-create-account
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 21a388894278c9086b40812e5cfa7dc9b662a5ef
ms.lasthandoff: 03/10/2017



---

# <a name="create-a-documentdb-account-with-mongodb-api"></a>MongoDB API を使用して DocumentDB アカウントを作成する
MongoDB 向けに作成されたアプリのデータ ストアとして DocumentDB データベースを使用できるようになりました。 この機能を使用するには、Azure アカウントと DocumentDB アカウントが必要です。 このチュートリアルでは、MongoDB アプリで使用するための DocumentDB アカウントを作成するプロセスについて説明します。 

MongoDB アカウントをサポートする DocumentDB は、Azure Portal を使用するか、Azure CLI で Azure Resource Manager テンプレートを使用して作成できます。 この記事では、Azure Portal を使用して、MongoDB アカウントをサポートする DocumentDB アカウントを作成する方法について説明します。 Azure CLI で Azure Resource Manager を使用してアカウントを作成する方法については、[Azure CLI 2.0 を使用した Azure DocumentDB アカウント管理の自動化](documentdb-automation-resource-manager-cli.md)に関する記事をご覧ください。

## <a name="prerequisite"></a>前提条件
Azure アカウント。 Azure アカウントがない場合は、[無料の Azure アカウント](https://azure.microsoft.com/free/)を今すぐ作成できます。
## <a name="create-a-documentdb-account"></a>DocumentDB アカウントを作成する

1. インターネット ブラウザーで [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のナビゲーションで、**[NoSQL (DocumentDB)]** をクリックします。

    ![DocumentDB NoSQL エントリが強調表示された、ポータルの左側のナビゲーションのスクリーン ショット](./media/documentdb-create-mongodb-account/portalleftnav.png)

3. または、**[その他のサービス >]** をクリックして、上部の検索バーに「**DocumentDB**」と入力し、**[NoSQL (DocumentDB)]** をクリックします。

    ![DocumentDB NoSQL エントリを検索するための [その他のサービス] ブレードのスクリーン ショット](./media/documentdb-create-mongodb-account/more-services-search.PNG)

4. **[NoSQL (DocumentDB)]** ブレードの上部で、トップの操作バーの **[+ 追加]** をクリックします。

    ![DocumentDB NoSQL リソースのブレードの [追加] ボタンのスクリーン ショット](./media/documentdb-create-mongodb-account/add-documentdb-account.png)

5. **[DocumentDB アカウント]** ブレードで、アカウントに必要な構成を指定します。

   ![Screen shot of the New DocumentDB with protocol support for MongoDB blade](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-account.PNG)

    - **[ID]** ボックスに、アカウントを識別する名前を入力します。  **ID** が検証されると、緑色のチェック マークが **[ID]** ボックスに表示されます。 この **ID** の値は、URI 内のホスト名になります。 **ID** に含めることができるのは英小文字、数字、および "-" のみで、文字数は 3 ～ 50 文字にする必要があります。 選択したエンドポイント名に *documents.azure.com* が追加され、これがアカウントのエンドポイントになります。

    - **[NoSQL API]** で、**[MongoDB]** を選択します。 これにより、DocumentDB データベースとのやり取りに使用する通信 API を指定します。

    - **[サブスクリプション]**で、アカウントに使用する Azure サブスクリプションを選択します。 アカウントのサブスクリプションが&1; つである場合は、既定ではそのアカウントが選択されます。

    - **[リソース グループ]**で、アカウントのリソース グループを選択または作成します。  既定では、Azure サブスクリプションの既存のリソース グループが選択されます。  ただし、アカウントを追加する新しいリソース グループの作成を選択することもできます。 詳細については、「 [Azure ポータルを使用した Azure リソースの管理](../azure-portal/resource-group-portal.md)」を参照してください。

    - **[場所]** を使用して、アカウントのホストとなる地理的場所を指定します。

6. 新しいアカウント オプションを構成したら、 **[作成]**をクリックします。  アカウントの作成には数分かかる場合があります。

   通知ハブから進行状況を監視できます。  

   ![DocumentDB アカウントを作成中であることを示す通知ハブのスクリーン ショット](./media/documentdb-create-mongodb-account/create-documentdb-mongodb-deployment-status.png)  

7. 新しいアカウントにアクセスするには、左側のメニューの **[DocumentDB (NoSQL)]** をクリックします。 通常の DocumentDB と Mongo プロトコル サポート アカウントを持つ DocumentDB が含まれるリストで、新しいアカウントの名前をクリックします。
8. DocumentDB アカウントは、MongoDB アプリで使用する準備ができました。

   ![Screen shot of the default account blade](./media/documentdb-create-mongodb-account/defaultaccountblade.png)

## <a name="next-steps"></a>次のステップ
* MongoDB のプロトコル対応の DocumentDB アカウントに [接続](documentdb-connect-mongodb-account.md) する方法を確認します。

