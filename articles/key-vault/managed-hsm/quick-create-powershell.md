---
title: Azure Key Vault でマネージド キーの属性を作成し、取得する - Azure PowerShell
description: Azure PowerShell を使用して Azure Key Vault との間でマネージド キーの設定と取得を行う方法を紹介するクイックスタート
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/26/2021
ms.author: mbaldwin
ms.openlocfilehash: 943555e9f7a26530a075aee27dd310d96974e652
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99072790"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>クイックスタート: PowerShell を使用して Azure Key Vault との間でマネージド キーの設定と取得を行う

このクイックスタートでは、Azure PowerShell を使用して Azure Key Vault にキー コンテナーを作成します。 Azure Key Vault は、セキュリティで保護されたシークレット ストアとして機能するクラウド サービスです。 キー、パスワード、証明書、およびその他のシークレットを安全に保管することができます。 Key Vault の詳細については、[概要](../general/overview.md)に関するページを参照してください。 Azure PowerShell は、コマンドまたはスクリプトを使用して Azure リソースを作成および管理するために使用します。 この作業を完了したら、キーを格納します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell をローカルにインストールして使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 1.0.0 以降が必要になります。 バージョンを確認するには、「`$PSVersionTable.PSVersion`」と入力します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzAccount` を実行して Azure との接続を作成することも必要です。

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 Azure PowerShell の [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) コマンドレットを使用して、*myResourceGroup* という名前のリソース グループを *westus* に作成します。 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

プリンシパル ID は、"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" という形式で返されます。

## <a name="create-a-managed-hsm"></a>マネージド HSM を作成する

新しい Key Vault Managed HSM を作成するには、Azure PowerShell の [New-AzKeyVaultManagedHsm](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) コマンドレットを使用します。 いくつかの情報を指定する必要があります。

- マネージド HSM 名: 数字 (0-9)、文字 (a-z、A-Z)、ハイフン (-) のみを含んだ 3 から 24 文字の文字列

  > [!Important]
  > 各マネージド HSM には一意の名前が必要です。 次の例の <your-unique-managed-hsm-name> は、お使いのマネージド HSM の名前に置き換えてください。

- リソース グループ名: **myResourceGroup**
- 場所: **EastUS**。
- プリンシパル ID: 前セクションで取得した Azure Active Directory のプリンシパル ID を "Administrator" パラメーターに渡します。 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

このコマンドレットの出力では、新しく作成したマネージド HSM のプロパティが示されます。 次の 2 つのプロパティをメモしておきます。

- **マネージド HSM 名**: 上の --name パラメーターに指定した名前です。
- **Vault URI (コンテナー URI)** :この例では、これは https://&lt;your-unique-managed-hsm-name&gt;.vault.azure.net/ です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

この時点で、自分の Azure アカウントが唯一、この新しいコンテナーで任意の操作を実行することを許可されています。

## <a name="activate-your-managed-hsm"></a>マネージド HSM をアクティブにする

HSM をアクティブにするまでは、データ プレーンのコマンドはすべて無効です。 キーを作成することはできず、ロールを割り当てることもできません。 HSM をアクティブにできるのは、create コマンドの実行時に割り当てられた指定された管理者だけです。 HSM をアクティブにするには、[セキュリティ ドメイン](security-domain.md)をダウンロードする必要があります。

HSM をアクティブにするための要件は次のとおりです。
- 最低 3 つの RSA キーペア (最大 10 個)
- セキュリティ ドメインの暗号化を解除するために必要なキーの最小数 (クォーラム) の指定

HSM をアクティブにするには、少なくとも 3 つ (最大 10 個) の RSA 公開キーを HSM に送信する必要があります。 HSM は、それらのキーでセキュリティ ドメインを暗号化して返します。 このセキュリティ ドメインのダウンロードが正常に完了したら、HSM を使用する準備は完了です。 加えて、クォーラムの指定も必要となります。クォーラムは、セキュリティ ドメインの暗号化を解除するために必要な秘密キーの最小数です。

次の例は、`openssl` ([こちら](https://slproweb.com/products/Win32OpenSSL.html)から Windows 版を入手可能) を使用して 3 つの自己署名証明書を生成する方法を示しています。

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> この手順で生成した RSA キー ペアとセキュリティ ドメイン ファイルは、安全に作成、保存してください。

セキュリティ ドメインをダウンロードして、マネージド HSM をアクティブにするには、Azure PowerShell の [Export-AzKeyVaultSecurityDomain](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) コマンドレットを使用します。 以下の例では、3 つの RSA キー ペア (このコマンドで必要なのは公開キーのみ) を使用し、クォーラムは 2 に設定しています。

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

セキュリティ ドメイン ファイルと RSA キー ペアは安全に保存してください。 これらは、ディザスター リカバリーに必要となります。また、同じセキュリティ ドメインを共有するマネージド HSM を新たに作成し、キーを共有できるようにする際にも必要となります。

セキュリティ ドメインを正常にダウンロードすると、HSM がアクティブな状態となり、いつでも使用することができます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成して証明書を格納しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- [Azure PowerShell の Key Vault コマンドレット](/powershell/module/az.keyvault/)のリファレンスを参照する
- [Key Vault のセキュリティの概要](../general/security-overview.md)を確認する
