---
title: "Resource Manager テンプレートでの Key Vault シークレット | Microsoft Docs"
description: "デプロイメント時にパラメーターとして Key Vault からシークレットを渡す方法について説明します。"
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c582c144-4760-49d3-b793-a3e1e89128e2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 23fb716997145152bd09d177b75973ad0b4ca9f3
ms.openlocfilehash: 171cfe2a8750025914545701fa3423c7b9baa5f2


---
# <a name="use-key-vault-to-pass-secure-parameter-value-during-deployment"></a>デプロイ時に Key Vault を使用して、セキュリティで保護されたパラメーター値を渡す

デプロイ時に、セキュリティで保護された値 (パスワードなど) をパラメーターとして渡す必要がある場合は、[Azure Key Vault](../key-vault/key-vault-whatis.md) からその値を取得できます。 値を取得するには、キー コンテナーとパラメーター ファイル内のシークレットを参照します。 参照するのは Key Vault ID だけであるため、値が公開されることはありません。 リソースをデプロイするたびに、シークレットの値を手動で入力する必要はありません。 キー コンテナーは、デプロイ先のリソース グループとは異なるサブスクリプションに存在していてもかまいません。 キー コンテナーを参照するときに、サブスクリプション ID を含めます。

このトピックでは、キー コンテナーとシークレットを作成し、Resource Manager テンプレート用にシークレットへのアクセスを構成して、シークレットをパラメーターとして渡す方法について説明します。 既にキー コンテナーとシークレットがあるものの、テンプレートとユーザー アクセスを確認する必要がある場合は、「[シークレットへのアクセスの有効化](#enable-access-to-the-secret)」セクションを参照してください。 既にキー コンテナーとシークレットがあり、テンプレートとユーザー アクセス用に構成されていることがわかっている場合は、「[固定 ID でのシークレットの参照](#reference-a-secret-with-static-id)」セクションを参照してください。 

## <a name="deploy-a-key-vault-and-secret"></a>Key Vault とシークレットのデプロイ

Resource Manager テンプレートを通じてキー コンテナーとシークレットをデプロイできます。 例については、[キー コンテナーのテンプレート](resource-manager-template-keyvault.md)に関するページと[キー コンテナー シークレットのテンプレート](resource-manager-template-keyvault-secret.md)に関するページを参照してください。 Key Vault を作成するときは、他の Resource Manager テンプレートから参照できるように、**enabledForTemplateDeployment** プロパティを **true** に設定します。 

または、Azure Portal を通じてキー コンテナーとシークレットを作成できます。 

1. **[新規]** -> **[セキュリティ + ID]** -> **[Key Vault]** の順に選択します。

   ![新しいキー コンテナーの作成](./media/resource-manager-keyvault-parameter/new-key-vault.png)

2. キー コンテナーの値を指定します。 ここでは、**[アクセス ポリシー]** と **[高度なアクセス ポリシー]** の設定は無視してかまいません。 これらの設定については、このセクションで取り上げます。 **[作成]**を選択します。

   ![キー コンテナーの設定](./media/resource-manager-keyvault-parameter/create-key-vault.png)

3. これでキー コンテナーが作成されました。 そのキー コンテナーを選択します。

4. キー コンテナー ブレードで、**[シークレット]** を選択します。

   ![シークレットの選択](./media/resource-manager-keyvault-parameter/select-secret.png)

5. **[追加]**を選択します。

   ![select add](./media/resource-manager-keyvault-parameter/add-secret.png)

6. アップロード オプションとして **[手動]** を選択します。 シークレットの名前と値を指定します。 **[作成]**を選択します。

   ![シークレットの指定](./media/resource-manager-keyvault-parameter/provide-secret.png)

キー コンテナーとシークレットを作成しました。

## <a name="enable-access-to-the-secret"></a>シークレットへのアクセスの有効化

新しいキー コンテナーと既存のキー コンテナーのどちらを使用する場合も、テンプレートをデプロイするユーザーがシークレットにアクセスできることを確認してください。 シークレットを参照するテンプレートをデプロイするユーザーには、キー コンテナーに対する `Microsoft.KeyVault/vaults/deploy/action` アクセス許可が必要です。 このアクセスは、[所有者](../active-directory/role-based-access-built-in-roles.md#owner)ロールと[共同作成者](../active-directory/role-based-access-built-in-roles.md#contributor)ロールが許可します。 このアクセス許可を付与する[カスタム ロール](../active-directory/role-based-access-control-custom-roles.md)を作成し、そのロールにユーザーを追加することもできます。 また、Resource Manager がデプロイ中にキー コンテナーにアクセスできるようにする必要もあります。

この手順はポータルで確認または実行できます。

1. **[アクセス制御 (IAM)]** を選択します。

   ![アクセス制御の選択](./media/resource-manager-keyvault-parameter/select-access-control.png)

2. テンプレートをデプロイするために使用するアカウントがまだ所有者または共同作業者になっていない場合 (または、`Microsoft.KeyVault/vaults/deploy/action` アクセス許可を付与するカスタム ロールに追加されている場合) は、**[追加]** をクリックします。

   ![add user](./media/resource-manager-keyvault-parameter/add-user.png)

3. 共同作業者または所有者ロールを選択し、そのロールに割り当てる ID を検索します。 **[OK]** をクリックしてロールへの ID の追加を完了します。

   ![add user](./media/resource-manager-keyvault-parameter/search-user.png)

4. デプロイ時にテンプレートからのアクセスを有効にするには、**[高度なアクセス制御]** を選択します。 **[テンプレートの展開に対して Azure Resource Manager へのアクセスを有効にする]** をオンにします。

   ![テンプレート アクセスの有効化](./media/resource-manager-keyvault-parameter/select-template-access.png)

## <a name="reference-a-secret-with-static-id"></a>固定 ID でのシークレットの参照

シークレットは、値をテンプレートに渡す**パラメーター ファイル (テンプレートではありません)** 内から参照します。 シークレットを参照するには、Key Vault のリソース識別子とシークレットの名前を渡します。 次の例では、Key Vault シークレットが既に存在しているという前提で、リソース ID に静的な値を指定します。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "sqlsvrAdminLoginPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          },
          "secretName": "adminPassword"
        }
      },
      "sqlsvrAdminLogin": {
        "value": "exampleadmin"
      }
    }
}
```

テンプレートで、シークレットを受け取るパラメーターは **securestring**です。 次に、管理者のパスワードが必要な、SQL Server をデプロイするテンプレートの関連するセクションを示します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlsvrAdminLogin": {
            "type": "string",
            "minLength": 4
        },
        "sqlsvrAdminLoginPassword": {
            "type": "securestring"
        },
        ...
    },
    "resources": [
        {
          "name": "[variables('sqlsvrName')]",
          "type": "Microsoft.Sql/servers",
          "location": "[resourceGroup().location]",
          "apiVersion": "2014-04-01-preview",
          "properties": {
              "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
              "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
          },
          ...
        }
    ],
    "variables": {
        "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
    },
    "outputs": { }
}
```



## <a name="reference-a-secret-with-dynamic-id"></a>動的 ID でのシークレットの参照

前のセクションでは、Key Vault シークレットの静的リソース ID を渡す方法を紹介しました。 しかし参照すべき Key Vault シークレットがデプロイごとに変わる状況も考えられます。 その場合、パラメーター ファイルでリソース ID をハードコーディングすることはできません。 パラメーター ファイルではテンプレート式が使用できないので、パラメーター ファイルでリソース ID を動的に生成することはできません。

Key Vault シークレットのリソース ID を動的に生成するには、そのシークレットを必要とするリソースを、入れ子になったテンプレートに移す必要があります。 マスター テンプレートに、その入れ子になったテンプレートを追加し、動的に生成されたリソース ID をパラメーターに格納して渡します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }],
    "outputs": {}
}
```

## <a name="next-steps"></a>次のステップ
* Key Vault の全般的な情報については、「[Azure Key Vault の概要](../key-vault/key-vault-get-started.md)」をご覧ください。
* 仮想マシンで Key Vault を使用する方法については、「 [Azure Resource Manager のセキュリティに関する考慮事項](best-practices-resource-manager-security.md)」をご覧ください。
* キー シークレットの詳細な参照例については、 [Key Vault の例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)を参照してください。




<!--HONumber=Dec16_HO2-->


