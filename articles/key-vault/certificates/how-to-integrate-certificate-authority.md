---
title: Key Vault と DigiCert 証明機関の統合
description: Key Vault と DigiCert 証明機関を統合する方法
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 06/02/2020
ms.author: sebansal
ms.openlocfilehash: 01383acad9f221e376f814ecf99794eb0431d0cd
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588927"
---
# <a name="integrating-key-vault-with-digicert-certificate-authority"></a>Key Vault と DigiCert 証明機関の統合

Azure Key Vault を使用すると、ネットワークのデジタル証明書のプロビジョニング、管理、およびデプロイを簡単に行うことができ、アプリケーションのセキュリティで保護された通信を実現できます。 デジタル証明書は、電子的トランザクションで身元の証明を確立するための電子資格情報です。 

Azure Key Vault ユーザーは、自分のキー コンテナーから DigiCert 証明書を直接生成できます。 Key Vault では、Key Vault と DigiCert 証明機関との信頼できるパートナーシップを通じて、DigiCert によって発行された証明書のエンドツーエンドの証明書ライフサイクル管理が保証されます。

証明書の一般的な情報については、[Azure Key Vault 証明書](/azure/key-vault/certificates/about-certificates)に関するページを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このガイドを完了するには、以下のリソースが必要です。
* キー コンテナー。 既存のキー コンテナーを使用することも、次のいずれかのクイックスタートの手順に従って新しいキー コンテナーを作成することもできます。
   - [Azure CLI を使用してキー コンテナーを作成する](../secrets/quick-create-cli.md)
   - [Azure PowerShell を使用してキー コンテナーを作成する](../secrets/quick-create-powershell.md)
   - [Azure portal を使用してキー コンテナーを作成する](../secrets/quick-create-portal.md)。
*   DigiCert CertCentral アカウントをアクティブにする必要があります。 CertCentral アカウントに[サインアップ](https://www.digicert.com/account/signup/)する。
*   対象のアカウントの管理者レベルのアクセス許可。


### <a name="before-you-begin"></a>開始する前に

DigiCert CertCentral アカウントから次の情報を取得していることを確認します。
-   CertCentral アカウント ID
-   組織 ID
-   API キー

## <a name="adding-certificate-authority-in-key-vault"></a>キー コンテナーへの証明機関の追加 
DigiCert CertCentral アカウントから上記の情報を収集した後、DigiCert をキー コンテナーの証明機関の一覧に追加できます。

### <a name="azure-portal"></a>Azure portal

1.  DigiCert 証明機関を追加するには、DigiCert を追加するキー コンテナーに移動します。 
2.  Key Vault のプロパティ ページで、 **[証明書]** を選択します。
3.  **[認証機関]** タブを選択します。![証明書のプロパティ](../media/certificates/how-to-integrate-certificate-authority/select-certificate-authorities.png)
4.  **[追加]** オプションを選択します。
 ![証明書のプロパティ](../media/certificates/how-to-integrate-certificate-authority/add-certificate-authority.png)
5.  **[Create a certificate Authority]\(証明機関の作成\)** 画面で、次の値を選択します。
    -   **Name**:識別可能な発行者名を追加します。 例: DigicertCA
    -   **[プロバイダー]** : メニューから DigiCert を選択します。
    -   **[アカウント ID]** : DigiCert CertCentral のアカウント ID を入力します
    -   **[Account Password]\(アカウントのパスワード\)** : DigiCert CertCentral アカウントで生成した API キーを入力します
    -   **[組織 ID]** : DigiCert CertCentral アカウントから収集した OrgID を入力します 
    -   **Create** をクリックしてください。
   
6.  DigicertCA が証明機関の一覧に追加されたことを確認できます。


### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell は、コマンドまたはスクリプトを使用して Azure リソースを作成および管理するために使用します。 Azure では、ブラウザー内の Azure portal を介して使用できる対話型のシェル環境である、Azure Cloud Shell がホストされています。

PowerShell をローカルにインストールして使用する場合、このチュートリアルでは Azure PowerShell モジュール バージョン 1.0.0 以降が必要になります。 バージョンを確認するには、「`$PSVersionTable.PSVersion`」と入力します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell をローカルで実行している場合、`Login-AzAccount` を実行して Azure との接続を作成することも必要です。

```azurepowershell-interactive
Login-AzAccount
```

1.  **リソース グループ**を作成します

[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用して Azure リソース グループを作成します。 リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

2. **キー コンテナー**を作成します

キー コンテナーには一意の名前を使用する必要があります。 ここで、"Contoso-Vaultname" は、このガイド全体で使用されるキー コンテナーの名前です。

- **コンテナー名**: Contoso-Vaultname。
- **リソース グループ名**: ContosoResourceGroup。
- **場所**: EastUS。

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vaultname' -ResourceGroupName 'ContosoResourceGroup' -Location 'EastUS'
```

3. DigiCert CertCentral アカウントから収集した情報の変数を定義します。

- **アカウント ID** 変数を定義します
- **組織 ID** 変数を定義します
- **API キー**変数を定義します
- **発行者名**変数を定義します

```azurepowershell-interactive
$accountId = "myDigiCertCertCentralAccountID"
$org = New-AzKeyVaultCertificateOrganizationDetails -Id OrganizationIDfromDigiCertAccount
$secureApiKey = ConvertTo-SecureString DigiCertCertCentralAPIKey -AsPlainText –Force
$issuerName = "DigiCertCA"
```

4. **発行者**を設定します。 これにより、Digicert が証明機関としてキー コンテナーに追加されます。
```azurepowershell-interactive
Set-AzureKeyVaultCertificateIssuer -VaultName $vaultName -IssuerName $issuerName -IssuerProvider DigiCert -AccountId $accountId -ApiKey $secureApiKey -OrganizationDetails $org
```

5. **キー コンテナー内の DigiCert から直接証明書のポリシーを設定し、証明書を発行します**。

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName DigiCertCA -ValidityInMonths 12 -RenewAtNumberOfDaysBeforeExpiry 60
Add-AzKeyVaultCertificate -VaultName "Contoso-Vaultname" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

この統合を通じて、指定したキー コンテナー内の Digicert CA によって証明書が正常に発行されました。

## <a name="troubleshoot"></a>トラブルシューティング

発行された証明書が Azure portal で "無効" 状態になっている場合は、 **[証明書の操作]** に進み、その証明書の DigiCert エラー メッセージを確認してください。

 ![証明書のプロパティ](../media/certificates/how-to-integrate-certificate-authority/certificate-operation-select.png)

詳しくは、[Key Vault REST API リファレンス内の証明書の操作](/rest/api/keyvault)の説明をご覧ください。 アクセス許可の設定については、「[Vaults - Create or Update](/rest/api/keyvault/vaults/createorupdate)」(コンテナー - 作成または更新) および「[Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy)」(コンテナー -アクセス ポリシーの更新) をご覧ください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

- KeyVault を使用して DigiCert ワイルドカード証明書を生成できますか。 
   はい。 これは、DigiCert アカウントをどのように構成したかによって異なります。
- EV 証明書を作成する場合、それを指定するにはどうすればよいですか。 
   証明書を作成するときに、[ポリシーの詳細構成] をクリックし、証明書の種類を指定します。 サポートされている値は、次のとおりです。OV-SSL、EV-SSL
- 統合を通じて DigiCert 証明書を作成する場合と、DigiCert を通じて証明書を直接取得する場合とで、時間的な差異は生じますか。
   いいえ。 証明書を作成するときに時間がかかることがあるのは検証のプロセスであり、その検証は DigiCert が従うプロセスに依存します。


## <a name="next-steps"></a>次のステップ

- [認証、要求、応答](../general/authentication-requests-and-responses.md)
- [Key Vault 開発者ガイド](../general/developers-guide.md)
