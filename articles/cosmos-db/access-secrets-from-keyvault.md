---
title: Key Vault を使用し Azure Cosmos DB キーを格納してアクセスする
description: Azure Key Vault を使用し、Azure Cosmos DB の接続文字列、キー、エンドポイントを格納し、アクセスします。
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 36b0a2f18cf2917251a87405456980811af1bc3d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242194"
---
# <a name="secure-azure-cosmos-keys-using-azure-key-vault"></a>Azure Key Vault を使用して Azure Cosmos キーをセキュリティ保護する 

アプリケーションで Azure Cosmos DB を使用すると、アプリケーションの構成ファイル内のエンドポイントとキーを使用して、データベース、コレクション、ドキュメントにアクセスできます。  ただし、キーと URLをアプリケーション コードに直接入力するのは安全ではありません。すべてのユーザーがクリア テキスト形式で入手できるためです。 セキュリティで保護されたメカニズムを通してエンドポイントとキーを提供する必要があります。 ここで Azure Key Vault が、アプリケーション シークレットを安全に格納および管理する際に役立ちます。

Azure Cosmos DB アクセス キーを Key Vault に格納して読み取るためには、次の手順が必要です。

* Key Vault の作成  
* Azure Cosmos DB アクセス キーの Key Vault への追加  
* Azure Web アプリケーションの作成  
* アプリケーションの登録および Key Vault を読み取るアクセス許可の付与  


## <a name="create-a-key-vault"></a>Key Vault の作成

1. [Azure portal](https://portal.azure.com/) にサインインします。  
2. **[リソースの作成] > [セキュリティ] > [Key Vault]** を選択します。  
3. **[キー コンテナーの作成]** セクションで、次の情報を入力します。  
   * **[名前]:** Key Vault の一意の名前を指定します。  
   * **[サブスクリプション]:** 使用するサブスクリプションを選択します。  
   * **[リソース グループ]** で、**[新規作成]** を選択し、リソース グループ名を入力します。  
   * [場所] プルダウン メニューで、場所を選択します。  
   * 他のオプションは既定値のままにしておきます。  
4. 上記の情報を指定したら、**[作成]** を選択します。  

## <a name="add-azure-cosmos-db-access-keys-to-the-key-vault"></a>Azure Cosmos DB アクセス キーの Key Vault への追加
1. 前の手順で作成した Key Vault に移動し、**[シークレット]** タブを開きます。  
2. **[+Generate/Import]\(+ 生成/インポート\)** を選択します。 

   * **[アップロード オプション]** として **[手動]** を選択します。
   * シークレットの **[名前]** を指定します。
   * Cosmos DB アカウントの接続文字列を **[値]** フィールドに指定します。 次に、**[作成]** を選択します。

   ![シークレットの作成](./media/access-secrets-from-keyvault/create-a-secret.png)

4. シークレットが作成されたら、開いて、次の形式の ** シークレット識別子をコピーします。 この識別子は次のセクションで使用します。 

   `https://<Key_Vault_Name>.vault.azure.net/secrets/<Secret _Name>/<ID>`

## <a name="create-an-azure-web-application"></a>Azure Web アプリケーションの作成

1. Azure Web アプリケーションを作成します。または、[GitHub リポジトリ](https://github.com/Azure/azure-cosmosdb-dotnet/tree/master/Demo/keyvaultdemo)からアプリケーションをダウンロードできます。 これは単純な MVC アプリケーションです。  

2. ダウンロードしたアプリケーションを解凍し、**HomeController.cs** ファイルを開きます。 次の行のシークレット ID を更新します。

   `var secret = await keyVaultClient.GetSecretAsync("<Your Key Vault’s secret identifier>")`

3. ファイルを**保存**し、ソリューションを**ビルド**します。  
4. 次は、Azure にアプリケーションをデプロイします。 プロジェクトを右クリックし、**[発行]** を選択します。 新しいアプリ サービス プロファイルを作成し (アプリの名前は WebAppKeyVault1 とします)、**[発行]** を選択します。   

5. アプリケーションがデプロイされたら、 Azure Portal で、デプロイした Web アプリケーションに移動し、そのアプリケーションの **[マネージド サービス ID]** をオンにします。  

   ![マネージド サービス ID](./media/access-secrets-from-keyvault/turn-on-managed-service-identity.png)

アプリケーションをすぐに実行すると、次のエラーが表示されます。このアプリケーションに Key Vault のアクセス許可を付与していないためです。

![アクセス許可なしでデプロイされたアプリケーション](./media/access-secrets-from-keyvault/app-deployed-without-access.png)

## <a name="register-the-application--grant-permissions-to-read-the-key-vault"></a>アプリケーションの登録および Key Vault を読み取るアクセス許可の付与

このセクションでは、アプリケーションを Azure Active Directory に登録し、Key Vault を読み取るためのアクセス許可をアプリケーションに付与します。 

1. Azure Portal に移動し、前のセクションで作成した **Key Vault** を開きます。  

2. **[アクセス ポリシー]** を開き、**[+ 新規追加]** を選択し、デプロイした Web アプリケーションを探して、アクセス許可を選択して **[OK]** を選択します。  

   ![アクセス ポリシーの追加](./media/access-secrets-from-keyvault/add-access-policy.png)

ここで、アプリケーションを実行すると、Key Vault からシークレットを読み取ることができます。

![シークレットと一緒にデプロイされたアプリケーション](./media/access-secrets-from-keyvault/app-deployed-with-access.png)
 
同様に、Key Vault にアクセスするユーザーを追加できます。 自分自身を Key Vaultに追加する必要があります。これには、**アクセスポリシー** をクリックしてから、Visual Studio でアプリケーションを実行するために必要なすべてのアクセス許可を付与します。 このアプリケーションをデスクトップから実行するとき、自分の ID が使用されます。

## <a name="next-steps"></a>次の手順

* Azure Cosmos DB のファイアウォールを構成するには、[ファイアウォールのサポート](firewall-support.md)に関する記事を参照してください。
* 仮想ネットワーク サービス エンドポイントを構成するには、[VNet サービス エンドポイントを使用してアクセスをセキュリティで保護する](vnet-service-endpoint.md)方法に関する記事をご覧ください。
