---
title: チュートリアル - Azure portal を使用して Key Vault に証明書をインポートする | Microsoft Docs
description: Azure Key Vault に証明書をインポートする方法を示すチュートリアル
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.custom: mvc, devx-track-azurecli
ms.date: 04/16/2020
ms.author: sebansal
ms.openlocfilehash: b7b15c1c4952f6e0a606b1ed46f1588c536b4ae2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102204084"
---
# <a name="tutorial-import-a-certificate-in-azure-key-vault"></a>チュートリアル:Azure Key Vault に証明書をインポートする

Azure Key Vault は、シークレットのセキュリティで保護されたストアを提供するクラウド サービスです。 キー、パスワード、証明書、およびその他のシークレットを安全に保管することができます。 Azure Key Vault は、Azure Portal を使用して作成および管理できます。 このチュートリアルでは、キー コンテナーを作成し、それを使用して証明書をインポートします。 Key Vault の詳細については、[概要](../general/overview.md)に関する記事をご覧ください。

このチュートリアルでは、次の操作方法について説明します。

> [!div class="checklist"]
> * Key Vault を作成します。
> * ポータルを使用して Key Vault に証明書をインポートします。
> * CLI を使用して Key Vault に証明書をインポートします。
> * PowerShell を使用して Key Vault に証明書をインポートします。


始める前に、[Key Vault の基本的な概念](../general/basic-concepts.md)を確認してください。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-vault"></a>コンテナーの作成

1. Azure portal メニューまたは **[ホーム]** ページで、 **[リソースの作成]** を選択します。
2. 検索ボックスに「**Key Vault**」と入力します。
3. 結果の一覧の **[Key Vault]** を選択します。
4. Key Vault のセクションで、 **[作成]** を選択します。
5. **[キー コンテナーの作成]** セクションで、次の情報を入力します。
    - **Name**:一意の名前が必要です。 このクイックスタートでは、**Example-Vault** を使用します。 
    - **サブスクリプション**:サブスクリプションを選択します。
    - **[リソース グループ]** で、 **[新規作成]** を選択し、リソース グループ名を入力します。
    - **[場所]** プルダウン メニューで場所を選択します。
    - 他のオプションは既定値のままにしておきます。
6. 上記の情報を指定したら、 **[作成]** を選択します。

次の 2 つのプロパティをメモしておきます。

* **Vault Name**:この例では、これは **Example-Vault** です。 この名前は他の手順で使用します。
* **Vault URI (コンテナー URI)** :この例では、これは https://example-vault.vault.azure.net/ です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

この時点で、使用している Azure アカウントが、この新しいコンテナーで操作を実行することを許可されている唯一のアカウントになります。

![Key Vault の作成が完了した後の出力](../media/certificates/tutorial-import-cert/vault-properties.png)

## <a name="import-a-certificate-to-key-vault"></a>証明書を Key Vault にインポートする

証明書をコンテナーにインポートするには、PEM または PFX 証明書ファイルをディスク上に配置する必要があります。 この例では **ExampleCertificate** という名前の証明書をインポートします。

> [!IMPORTANT]
> Azure Key Vault でサポートされている証明書の形式は PFX と PEM です。 
> - .pem ファイル形式には、1 つまたは複数の X509 証明書ファイルが含まれています。
> - .pfx ファイル形式は、サーバー証明書 (ドメイン用に発行) や一致する秘密キーなどの複数の暗号化オブジェクトを 1 つのファイルに格納するためのアーカイブ ファイル形式で、必要に応じて中間 CA を含めることができます。  

1. Key Vault のプロパティ ページで、 **[証明書]** を選択します。
2. **[Generate/Import]\(生成/インポート\)** をクリックします。
3. **[証明書の作成]** 画面で、次の値を選択します。
    - **[証明書の作成方法]** :インポート。
    - **[証明書名]** :ExampleCertificate。
    - **[証明書ファイルのアップロード]** : ディスクから証明書ファイルを選択します
    - **[パスワード]** : パスワードで保護された証明書ファイルをアップロードする場合は、ここにパスワードを指定します。 それ以外の場合は空白のまま残します。 証明書ファイルが正常にインポートされると、このパスワードはキー コンテナーによって削除されます。
4. **Create** をクリックしてください。

![証明書のプロパティ](../media/certificates/tutorial-import-cert/cert-import.png)

**[インポート]** を使用して証明書を追加することにより、証明書のパラメーター (有効期間、発行者名、アクティブ化した日など) は、Azure Key Vault によって自動的に設定されます。

証明書が正常にインポートされたことを示すメッセージが表示されたら、一覧でその証明書をクリックすることで、対応するプロパティを表示できます。 

![証明書のプロパティを表示する場所を示すスクリーンショット。](../media/certificates/tutorial-import-cert/current-version-hidden.png)

## <a name="import-a-certificate-using-azure-cli"></a>Azure CLI を使用して証明書をインポートする

指定されたキー コンテナーに証明書をインポートします。 秘密キーを含む既存の有効な証明書を Azure Key Vault にインポートするには、インポートするファイルには、PFX 形式または PEM 形式のいずれかを使用できます。 証明書が PEM 形式の場合、PEM ファイルには x509 証明書だけでなく、キーが含まれている必要があります。 この操作には、証明書/インポートのアクセス許可が必要です。

```azurecli
az keyvault certificate import --file
                               --name
                               --vault-name
                               [--disabled {false, true}]
                               [--only-show-errors]
                               [--password]
                               [--policy]
                               [--subscription]
                               [--tags]
```

[パラメーター](/cli/azure/keyvault/certificate#az-keyvault-certificate-import)の詳細を参照してください。

証明書のインポート後は、[certificate show](/cli/azure/keyvault/certificate#az-keyvault-certificate-show) を使用して証明書を表示できます。


```azurecli
az keyvault certificate show [--id]
                             [--name]
                             [--only-show-errors]
                             [--subscription]
                             [--vault-name]
                             [--version]
```

以上で終了です。キー コンテナーを作成して、証明書をインポートし、証明書のプロパティを確認しました。

## <a name="import-a-certificate-using-azure-powershell"></a>Azure PowerShell を使用して証明書をインポートする

```
Import-AzureKeyVaultCertificate
      [-VaultName] <String>
      [-Name] <String>
      -FilePath <String>
      [-Password <SecureString>]
      [-Tag <Hashtable>]
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
```

[パラメーター](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0)の詳細を参照してください。


## <a name="clean-up-resources"></a>リソースをクリーンアップする

Key Vault に関する他のクイック スタートとチュートリアルは、このクイック スタートに基づいています。 後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことをお勧めします。
不要になったら、リソース グループを削除します。これにより、Key Vault と関連リソースが削除されます。 ポータルを使用してリソース グループを削除するには:

1. ポータル上部にある検索ボックスにリソース グループの名前を入力します。 このクイック スタートで使用されているリソース グループが検索結果に表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[リソース グループ名を入力してください:]** ボックスにリソース グループの名前を入力し、 **[削除]** を選択します。


## <a name="next-steps"></a>次のステップ

このチュートリアルでは、キー コンテナーを作成して証明書を内部にインポートしました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault での証明書作成の管理](./create-certificate-scenarios.md)の詳細を確認する
- [REST API を使用した証明書のインポート](/rest/api/keyvault/importcertificate/importcertificate)の例を参照する
- [Key Vault のセキュリティの概要](../general/security-overview.md)を確認する