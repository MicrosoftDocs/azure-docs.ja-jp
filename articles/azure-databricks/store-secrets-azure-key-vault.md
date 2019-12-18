---
title: チュートリアル - Azure Databricks からキー コンテナーを使用して Blob Storage にアクセスする
description: このチュートリアルでは、キー コンテナーに格納されたシークレットを使用して Azure Databricks から Azure Blob Storage にアクセスする方法について説明します。
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: 27e166a8798f851f6c086c025dd82957b2dcfb84
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849277"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>チュートリアル:Azure Key Vault を使用して Azure Databricks から Azure Blob Storage にアクセスする

このチュートリアルでは、キー コンテナーに格納されたシークレットを使用して Azure Databricks から Azure Blob Storage にアクセスする方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ストレージ アカウントと BLOB コンテナーを作成する
> * Azure キー コンテナーを作成してシークレットを追加する
> * Azure Databricks ワークスペースを作成してシークレット スコープを追加する
> * Azure Databricks から自分の BLOB コンテナーにアクセスする

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure Portal](https://portal.azure.com/) にサインインします。

> [!Note]
> **Azure 無料試用版サブスクリプション**を使用してこのチュートリアルを実行することはできません。
> 無料アカウントをお持ちの場合は、お使いのプロファイルにアクセスし、サブスクリプションを **[従量課金制]** に変更します。 詳細については、[Azure 無料アカウント](https://azure.microsoft.com/free/)に関するページをご覧ください。 次に、リージョン内の vCPU について[使用制限を削除し](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)、[クォータの増加を依頼](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)します。 Azure Databricks ワークスペースを作成するときに、 **[Trial (Premium - 14-Days Free DBUs)]\(試用版 (Premium - 14 日間の無料 DBU)\)** の価格レベルを選択し、ワークスペースから 14 日間無料の Premium Azure Databricks DBU にアクセスできるようにします。

## <a name="create-a-storage-account-and-blob-container"></a>ストレージ アカウントと BLOB コンテナーを作成する

1. Azure portal で、 **[リソースの作成]**  >  **[ストレージ]** の順に選択します。 次に、 **[ストレージ アカウント]** を選択します。

   ![Azure ストレージ アカウント リソースを見つける](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. サブスクリプションとリソース グループを選択するか、新しいリソース グループを作成します。 次に、ストレージ アカウント名を入力し、場所を選択します。 **[確認および作成]** を選択します。

   ![ストレージ アカウント プロパティの設定](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. 検証に失敗した場合は、問題を解決してからもう一度やり直してください。 検証に成功した場合は、 **[作成]** を選択し、ストレージ アカウントが作成されるのを待ちます。

4. 新しく作成されたストレージ アカウントに移動し、 **[概要]** ページの **[サービス]** の下にある **[BLOB]** を選択します。 次に、 **[+ コンテナー]** を選択し、コンテナー名を入力します。 **[OK]** を選択します。

   ![新しいコンテナーを作成する](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. 自分の Blob Storage コンテナーにアップロードするファイルを指定します。 ファイルがない場合は、テキスト エディターを使用して、何らかの情報を含む新しいテキスト ファイルを作成します。 この例では、**hw.txt** という名前のファイルに "hello world" というテキストが含まれています。 テキスト ファイルをローカルに保存し、自分の Blob Storage コンテナーにアップロードします。

   ![ファイルをコンテナーにアップロードする](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. ストレージ アカウントに戻り、 **[設定]** の下にある **[アクセス キー]** を選択します。 このチュートリアルで後から使用できるように、 **[ストレージ アカウント名]** と **[キー 1]** をテキスト エディターにコピーしておきます。

   ![ストレージ アカウントのアクセス キーを見つける](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Azure キー コンテナーを作成してシークレットを追加する

1. Azure portal で、 **[リソースの作成]** に移動し、検索ボックスに「**Key Vault**」と入力します。

   ![Azure リソースの検索ボックスを作成する](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. Key Vault リソースが自動的に選択されます。 **作成** を選択します。

   ![Key Vault リソースを作成する](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. **[Create key vault]\(キー コンテナーの作成\)** ページで、次の情報を入力し、残りのフィールドには既定値をそのまま使用します。

   |プロパティ|Description|
   |--------|-----------|
   |Name|自分のキー コンテナーの一意の名前。|
   |サブスクリプション|サブスクリプションを選択します。|
   |Resource group|リソース グループを選択するか、新しく作成します。|
   |Location|場所を選択します。|

   ![Azure キー コンテナーのプロパティ](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. 上記の情報を指定したら、 **[作成]** を選択します。 

4. Azure portal で、新しく作成されたキー コンテナーに移動し、 **[シークレット]** を選択します。 次に、 **[+ Generate/Import]\(+ 生成/インポート\)** を選択します。 

   ![新しいキー コンテナー シークレットを生成する](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. **[シークレットの作成]** ページで、次の情報を指定し、残りのフィールドには既定値をそのまま使用します。

   |プロパティ|Value|
   |--------|-----------|
   |Upload options|マニュアル|
   |Name|自分のストレージ アカウント キーのフレンドリ名。|
   |Value|自分のストレージ アカウントからのキー 1。|

   ![新しいキー コンテナー シークレットのプロパティ](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. このチュートリアルで後から使用できるように、キー名をテキスト エディターに保存し、 **[作成]** を選択します。 次に、 **[プロパティ]** メニューに移動します。 このチュートリアルで後から使用できるように、**DNS 名**と**リソース ID** をテキスト エディターにコピーします。

   ![Azure キー コンテナーの DNS 名とリソース ID をコピーする](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Azure Databricks ワークスペースを作成してシークレット スコープを追加する

1. Azure portal で、 **[リソースの作成]**  >  **[分析]**  >  **[Azure Databricks]** の順に選択します。

    ![Azure portal での Databricks](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. **[Azure Databricks サービス]** で次の値を指定して、Databricks ワークスペースを作成します。

   |プロパティ  |説明  |
   |---------|---------|
   |ワークスペース名     | Databricks ワークスペースの名前を指定します        |
   |サブスクリプション     | ドロップダウンから Azure サブスクリプションを選択します。        |
   |Resource group     | 自分のキー コンテナーが含まれているリソース グループと同じリソース グループを選択します。 |
   |Location     | 自分の Azure キー コンテナーと同じ場所を選択します。 使用可能なすべてのリージョンについては、[リージョン別の利用可能な Azure サービス](https://azure.microsoft.com/regions/services/)に関するページを参照してください。        |
   |価格レベル     |  **Standard** と **Premium** のいずれかを選択します。 これらのレベルの詳細については、[Databricks の価格に関するページ](https://azure.microsoft.com/pricing/details/databricks/)を参照してください。       |

   ![Databricks ワークスペースのプロパティ](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   **作成** を選択します。

3. Azure portal で、新しく作成された Azure Databricks リソースに移動し、 **[Launch Workspace]\(ワークスペースの起動\)** を選択します。

   ![Azure Databricks ワークスペースを起動する](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. 自分の Azure Databricks ワークスペースが別のウィンドウで開かれたら、URL の末尾に **#secrets/createScope** を追加します。 URL の形式は次のとおりです。 

   **https://<\location>.azuredatabricks.net/?o=<\orgID>#secrets/createScope**。
   

5. スコープ名を入力し、前の手順で保存した Azure キー コンテナーの DNS 名とリソース ID を入力します。 このチュートリアルで後から使用できるように、スコープ名をテキスト エディターに保存します。 そのうえで **[Create]\(作成\)** を選択します。

   ![Azure Databricks ワークスペースでシークレット スコープを作成する](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Azure Databricks から自分の BLOB コンテナーにアクセスする

1. 自分の Azure Databricks ワークスペースのホーム ページで、 **[共通タスク]** の下にある **[新しいクラスター]** を選択します。

   ![新しい Azure Databricks ノートブックを作成する](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. クラスター名を入力し、 **[クラスターの作成]** を選択します。 クラスターの作成が完了するまでに数分かかります。

3. クラスターが作成されたら、自分の Azure Databricks ワークスペースのホーム ページに移動し、 **[共通タスク]** の下にある **[新しいノートブック]** を選択します。

   ![新しい Azure Databricks ノートブックを作成する](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. ノートブック名を入力し、言語を Python に設定します。 クラスターを、前の手順で作成したクラスターの名前に設定します。

5. 次のコマンドを実行して、自分の Blob Storage コンテナーをマウントします。 次のプロパティの値を必ず変更してください。

   * your-container-name
   * your-storage-account-name
   * mount-name
   * config-key
   * scope-name
   * key-name

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **mount-name** は、Blob Storage コンテナーまたは (ソースで指定された) コンテナー内のフォルダーがマウントされる場所を表す DBFS パスです。
   * **conf-key** には `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` と `fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net` のいずれかを指定できます。
   * **scope-name** は、前のセクションで作成したシークレット スコープの名前です。 
   * **key-name** は、自分のキー コンテナー内のストレージ アカウント キー用に作成したシークレットの名前です。

   ![ノートブック内で Blob Storage のマウントを作成する](./media/store-secrets-azure-key-vault/command1.png)

6. 次のコマンドを実行して、自分の Blob Storage コンテナー内のテキスト ファイルをデータフレームに読み取ります。 マウント名とファイル名に一致するようにコマンドの値を変更します。

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![データフレームにファイルを読み取る](./media/store-secrets-azure-key-vault/command2.png)

7. 次のコマンドを使用して、使用するファイルの内容を表示します。

   ```python
   df.show()
   ```
   ![データフレームを表示する](./media/store-secrets-azure-key-vault/command3.png)

8. 自分の BLOB ストレージのマウントを解除するには、次のコマンドを実行します。

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![ストレージ アカウントのマウントを解除する](./media/store-secrets-azure-key-vault/command4.png)

9. マウントが解除されると、自分の Blob Storage アカウントからの読み取りはできなくなることに注意してください。

   ![ストレージ アカウントのマウント解除エラー](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このアプリケーションを引き続き使用しない場合は、次の手順でリソース グループ全体を削除します。

1. Azure portal の左側のメニューで、 **[リソース グループ]** を選択し、自分のリソース グループに移動します。

2. **[リソース グループの削除]** を選択し、自分のリソース グループ名を入力します。 次に、 **[削除]** を選択します。 

## <a name="next-steps"></a>次の手順

次の記事に進み、Cosmos DB に対して有効なサービス エンドポイントを使用して、VNet に導入された Databricks 環境を構築する方法について説明します。
> [!div class="nextstepaction"]
> [チュートリアル:Cosmos DB エンドポイントを使用して Azure Databricks を構築する](service-endpoint-cosmosdb.md)
