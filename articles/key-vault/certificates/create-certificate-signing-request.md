---
title: Azure Key Vault での CSR の作成とマージ
description: Azure Key Vault での CSR の作成とマージ
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: 44d77c36b9aacb8a2f06fd7a0f167cffa06ae4eb
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88716114"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>Key Vault での CSR の作成とマージ

Azure Key Vault を使用すると、任意の証明機関によって発行されたデジタル証明書を、キー コンテナーに格納することができます。 公開キーと秘密キーのペアを使用して証明書署名要求を作成できます。また、証明書には任意の証明機関の署名を使用できます。 内部のエンタープライズ CA または外部の公開 CA を使用できます。 証明書署名要求 (CSR または証明書要求) は、デジタル証明書の発行を要求するためにユーザーから証明機関 (CA) に送信されるメッセージです。

証明書の一般的な情報については、[Azure Key Vault 証明書](/azure/key-vault/certificates/about-certificates)に関するページを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="adding-certificate-in-key-vault-issued-by-a-non-trusted-ca"></a>信頼されていない CA によって発行された証明書を Key Vault に追加する

次の手順を実行すると、Key Vault と提携していない証明機関から証明書を作成できます (たとえば、GoDaddy は信頼されたキー コンテナー CA ではありません)。 


### <a name="azure-powershell"></a>Azure PowerShell



1.  最初に、**証明書ポリシーを作成します**。 このシナリオで選択した CA はサポートされていないため、Key Vault はユーザーの代わりに発行者に対して証明書を登録または更新しません。そのため、発行者名は不明に設定されます。

    ```azurepowershell
    $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
    ```


2. **証明書署名要求**を作成します。

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. **CA によって署名された CSR 要求**を取得する。`$certificateOperation.CertificateSigningRequest` は、証明書の base4 でエンコードされた証明書署名要求です。 この BLOB を取得し、発行者の証明書要求 Web サイトにダンプできます。 この手順は CA によって異なりますが、この手順を実行する方法については、CA のガイドラインを参照することをお勧めします。 また、certreq や openssl などのツールを使用して、証明書要求に署名し、証明書を生成するプロセスを完了することもできます。


4. Key Vault で**署名済み要求をマージする**。証明書要求が発行者によって署名された後、署名された証明書を戻して、Azure Key Vault で作成された最初の公開キーと秘密キーのペアにマージできます。

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    証明書要求が正常にマージされました。

### <a name="azure-portal"></a>Azure portal

1.  任意の CA の CSR を生成するには、証明書を追加するキー コンテナーに移動します。
2.  Key Vault のプロパティ ページで、 **[証明書]** を選択します。
3.  **[生成/インポート]** タブを選択します。
4.  **[証明書の作成]** 画面で、次の値を選択します。
    - **[証明書の作成方法]:** 生成。
    - **[証明書名]:** ContosoManualCSRCertificate。
    - **[Type of Certificate Authority (CA)] (証明機関 (CA) の種類):** 統合されていない CA によって発行された証明書。
    - **[件名]:** `"CN=www.contosoHRApp.com"`
    - 必要に応じて、他の値を選択します。 **Create** をクリックしてください。

    ![証明書のプロパティ](../media/certificates/create-csr-merge-csr/create-certificate.png)
6.  証明書が [証明書] の一覧に追加されていることがわかります。 先ほど作成した新しい証明書を選択します。 証明書の現在の状態は、CA によってまだ発行されていないため、"無効" になります。
7. **[証明書の操作]** タブをクリックし、 **[CSR のダウンロード]** を選択します。
 ![証明書のプロパティ](../media/certificates/create-csr-merge-csr/download-csr.png)

8.  要求が署名されるように、.csr ファイルを CA に持っていきます。
9.  CA によって要求が署名されたら、証明書ファイルを戻し、同じ [証明書の操作] 画面で**署名された要求をマージ**します。

証明書要求が正常にマージされました。

## <a name="adding-more-information-to-csr"></a>CSR への詳細情報の追加

CSR を作成する際に、次のような詳細情報を追加する必要が生じることがあります: 
    - 国:
    - 市区町村、
    - 都道府県、
    - 組織、
    - 組織単位。これらの情報はすべて、CSR の作成時に subjectName で定義することにより追加できます。

例
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```

>[!Note]
>CSR でこれらの詳細情報がすべて含まれた DV 証明書を要求している場合、CA では要求内のすべての情報を検証できるとは限らないため、要求が拒否される可能性があります。 OV 証明書を要求している場合は、その情報すべてを CSR に追加する方が適切です。


## <a name="troubleshoot"></a>トラブルシューティング

- **エラーの種類「指定された X.509 証明書の内容のエンド エンティティ証明書の公開キーが、指定された秘密キーの公開部分と一致しません。証明書が有効かどうかをご確認ください」** 。このエラーは、CSR を、開始された同じ CSR 要求とマージしない場合に発生することがあります。 CSR が作成されるたびに、署名された要求をマージするときに一致する必要がある秘密キーが作成されます。
    
- 発行された証明書が Azure portal で "無効" 状態になっている場合は、 **[証明書の操作]** に進み、その証明書のエラー メッセージを確認してください。

詳しくは、[Key Vault REST API リファレンス内の証明書の操作](/rest/api/keyvault)の説明をご覧ください。 アクセス許可の設定については、「[Vaults - Create or Update](/rest/api/keyvault/vaults/createorupdate)」(コンテナー - 作成または更新) および「[Vaults - Update Access Policy](/rest/api/keyvault/vaults/updateaccesspolicy)」(コンテナー -アクセス ポリシーの更新) をご覧ください。

## <a name="next-steps"></a>次のステップ

- [認証、要求、応答](../general/authentication-requests-and-responses.md)
- [Key Vault 開発者ガイド](../general/developers-guide.md)
