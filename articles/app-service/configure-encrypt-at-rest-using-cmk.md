---
title: 保存時にアプリケーション ソースを暗号化する
description: Azure Storage でアプリケーション データを暗号化し、パッケージ ファイルとしてデプロイする方法について説明します。
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: 5524b749b1e15342dd0133920d7190e33ced18ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92146046"
---
# <a name="encryption-at-rest-using-customer-managed-keys"></a>カスタマー マネージド キーを使用した保存時の暗号化

Web アプリのアプリケーション保存データを暗号化するには、Azure Storage アカウントと Azure Key Vault が必要です。 これらのサービスは、デプロイ パッケージからアプリを実行するときに使用されます。

  - [Azure Storage では保存時に暗号化を行うことができます](../storage/common/storage-service-encryption.md)。 システムによって提供されるキー、または独自のカスタマー マネージド キーを使用できます。 これは、Azure の Web アプリで実行されていないアプリケーション データが格納される場所です。
  - [デプロイ パッケージからの実行](deploy-run-package.md)は、App Service のデプロイ機能です。 これにより、Shared Access Signature (SAS) URL を使用して、Azure Storage アカウントからサイト コンテンツをデプロイできます。
  - [Key Vault 参照](app-service-key-vault-references.md)は、App Service のセキュリティ機能です。 これにより、実行時にアプリケーション設定としてシークレットをインポートできます。 これを使用して、Azure Storage アカウントの SAS URL を暗号化します。

## <a name="set-up-encryption-at-rest"></a>保存時の暗号化の設定

### <a name="create-an-azure-storage-account"></a>Azure Storage アカウントの作成

最初に、[Azure Storage アカウントを作成し](../storage/common/storage-account-create.md)、[それをカスタマー マネージド キーを使用して暗号化します](../storage/common/customer-managed-keys-overview.md)。 ストレージ アカウントが作成された後、[Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) を使用してパッケージ ファイルをアップロードします。

次に、Storage Explorer を使用して [SAS を生成します](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#generate-a-sas-in-storage-explorer)。 

> [!NOTE]
> この SAS URL を保存します。これは後で、実行時にデプロイ パッケージに安全にアクセスできるようにするために使用されます。

### <a name="configure-running-from-a-package-from-your-storage-account"></a>ストレージ アカウントからパッケージを実行するように構成する
  
ファイルを BLOB ストレージにアップロードし、ファイルの SAS URL を取得した後、`WEBSITE_RUN_FROM_PACKAGE` アプリケーション設定を SAS URL に設定します。 次の例では、Azure CLI を使用してそれを行っています。

```
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_RUN_FROM_PACKAGE="<your-SAS-URL>"
```

このアプリケーション設定を追加すると、Web アプリが再起動します。 アプリが再起動したら、そのアプリを参照し、デプロイ パッケージを使用して正しく起動したことを確認します。 アプリケーションが正しく起動しなかった場合は、[パッケージからの実行のトラブルシューティング ガイド](deploy-run-package.md#troubleshooting)に関する記事を参照してください。

### <a name="encrypt-the-application-setting-using-key-vault-references"></a>Key Vault 参照を使用してアプリケーション設定を暗号化する

`WEBSITE_RUN_FROM_PACKAGE` アプリケーション設定の値を、SAS でエンコードされた URL への Key Vault 参照に置き換えることができるようになりました。 これにより、SAS URL が Key Vault で暗号化され、追加のセキュリティ層が用意されます。

1. 次の [`az keyvault create`](/cli/azure/keyvault#az-keyvault-create) コマンドを使用して Key Vault インスタンスを作成します。       

    ```azurecli    
    az keyvault create --name "Contoso-Vault" --resource-group <group-name> --location eastus    
    ```    

1. [こちらの手順](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)に従い、キー コンテナーへのアクセス権をアプリに付与します。

1. 次の [`az keyvault secret set`](/cli/azure/keyvault/secret#az-keyvault-secret-set) コマンドを使用して、キー コンテナーにシークレットとして外部 URL を追加します。   

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ```    

1.  次の [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) コマンドを使用して、`WEBSITE_RUN_FROM_PACKAGE` アプリケーション設定を作成し、値を外部 URL への Key Vault 参照として指定します。

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>` は、前の `az keyvault secret set` コマンドの出力にあります。

このアプリケーション設定を更新すると、Web アプリが再起動します。 アプリが再起動したら、そのアプリを参照し、Key Vault 参照を使用して正しく起動したことを確認します。

## <a name="how-to-rotate-the-access-token"></a>アクセス トークンをローテーションする方法

ストレージ アカウントの SAS キーは定期的にローテーションすることをお勧めします。 Web アプリが誤ってアクセス権を失わないようにするために、Key Vault で SAS URL も更新する必要があります。

1. Azure portal でストレージ アカウントに移動して SAS キーをローテーションします。 **[設定]**  >  **[アクセス キー]** で、SAS キーをローテーションするためのアイコンをクリックします。

1. 新しい SAS URL をコピーし、次のコマンドを使用して、Key Vault に更新された SAS URL を設定します。

    ```azurecli    
    az keyvault secret set --vault-name "Contoso-Vault" --name "external-url" --value "<SAS-URL>"    
    ``` 

1. アプリケーション設定の Key Vault 参照を新しいシークレット バージョンに更新します。

    ```azurecli    
    az webapp config appsettings set --settings WEBSITE_RUN_FROM_PACKAGE="@Microsoft.KeyVault(SecretUri=https://Contoso-Vault.vault.azure.net/secrets/external-url/<secret-version>"    
    ```

    `<secret-version>` は、前の `az keyvault secret set` コマンドの出力にあります。

## <a name="how-to-revoke-the-web-apps-data-access"></a>Web アプリのデータ アクセスを取り消す方法

ストレージ アカウントへの Web アプリのアクセス権を取り消す方法は 2 つあります。 

### <a name="rotate-the-sas-key-for-the-azure-storage-account"></a>Azure Storage アカウントの SAS キーをローテーションする

ストレージ アカウントの SAS キーがローテーションされると、Web アプリはストレージ アカウントへのアクセス権を失いますが、パッケージ ファイルの最終ダウンロード バージョンを使用して引き続き実行されます。 Web アプリを再起動すると、最終ダウンロード バージョンがクリアされます。

### <a name="remove-the-web-apps-access-to-key-vault"></a>Key Vault への Web アプリのアクセス権を削除する

Key Vault への Web アプリのアクセス権を無効にすることで、サイト データへの Web アプリのアクセス権を取り消すことができます。 これを行うには、Web アプリの ID のアクセス ポリシーを削除します。 これは、Key Vault 参照を構成するときに作成した同じ ID です。

## <a name="summary"></a>まとめ

これで、アプリケーション ファイルがストレージ アカウントで保存時に暗号化されるようになりました。 Web アプリは、起動時に Key Vault から SAS URL を取得します。 最後に、Web アプリは、ストレージ アカウントからアプリケーション ファイルを読み込みます。 

ストレージ アカウントへの Web アプリのアクセス権を取り消す必要がある場合は、キー コンテナーへのアクセス権を取り消すか、ストレージ アカウント キーをローテーションします。これにより、SAS URL が無効になります。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="is-there-any-additional-charge-for-running-my-web-app-from-the-deployment-package"></a>デプロイ パッケージから Web アプリを実行する場合、追加料金は発生しますか。

Azure Storage アカウントに関連するコストと、該当するエグレス料金のみです。

### <a name="how-does-running-from-the-deployment-package-affect-my-web-app"></a>デプロイ パッケージからの実行は、Web アプリにどのように影響しますか。

- デプロイ パッケージからアプリを実行すると、`wwwroot/` が読み取り専用になります。 アプリでこのディレクトリへの書き込みを試行すると、エラーを受け取ります。
- TAR および GZIP 形式はサポートされていません。
- この機能は、ローカル キャッシュと互換性がありません。

## <a name="next-steps"></a>次のステップ

- [App Service の Key Vault 参照](app-service-key-vault-references.md)
- [保存データに対する Azure Storage 暗号化](../storage/common/storage-service-encryption.md)