---
title: Azure Key Vault で CSR を作成、マージする
description: Azure Key Vault で CSR を作成、マージする方法について説明します。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: aa631f4c505200c2c8abc67d4e22ffbab23e015c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789029"
---
# <a name="create-and-merge-a-csr-in-key-vault"></a>Key Vault で CSR を作成、マージする

Azure Key Vault は、任意の証明機関 (CA) によって発行されたデジタル証明書の格納をサポートしています。 また、秘密キーと公開キーのペアを使用した証明書署名要求 (CSR) の作成をサポートしています。 CSR への署名は、任意の CA (社内のエンタープライズ CA または社外の公的 CA) が実行できます。 CSR は、デジタル証明書を要求する目的で CA に送信されるメッセージです。

証明書に関する一般的な情報については、[Azure Key Vault の証明書](./about-certificates.md)に関するページを参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>提携 CA によって発行された証明書を Key Vault に追加する

Key Vault は、証明書の作成を簡素化するために、次の証明機関と提携しています。

|プロバイダー|証明書の種類|構成のセットアップ  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault は、DigiCert による OV または EV SSL 証明書を提供します。| [統合ガイド](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault は、GlobalSign による OV または EV SSL 証明書を提供します。| [統合ガイド](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>提携外の CA によって発行された証明書を Key Vault に追加する

Key Vault と提携していない CA からの証明書を追加するには、次の手順に従います (たとえば GoDaddy は、信頼された Key Vault CA ではありません)。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. 証明書の追加先となるキー コンテナーに移動します。
1. プロパティ ページで **[証明書]** を選択します。
1. **[生成/インポート]** タブを選択します。
1. **[証明書の作成]** 画面で、次の値を選択します。
    - **[証明書の作成方法]** : [生成]。
    - **[証明書名]** :ContosoManualCSRCertificate。
    - **[証明機関 (CA) の種類]** : 統合されていない CA によって発行された証明書。
    - **[サブジェクト]** : `"CN=www.contosoHRApp.com"`。
     > [!NOTE]
     > 値にコンマ (,) が含まれた相対識別名 (RDN) を使用している場合は、特殊文字を含む値を二重引用符で囲みます。 
     >
     > たとえば、 **[サブジェクト]** のエントリが `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com` であるとします。
     >
     > この例では、RDN の `OU` には、名前にコンマを含む値が含まれています。 `OU` の結果の出力は、**Docs, Contoso** になります。
1. 必要に応じて他の値を選択し、 **[作成]** を選択して **[証明書]** の一覧に証明書を追加します。

    ![証明書のプロパティのスクリーンショット](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. **[証明書]** の一覧から新しい証明書を選択します。 現時点では、証明書はまだ CA によって発行されていないため、証明書の状態は **無効** です。
1. **[証明書の操作]** タブで **[CSR のダウンロード]** を選択します。

   ![[CSR のダウンロード] ボタンが強調表示されているスクリーンショット。](../media/certificates/create-csr-merge-csr/download-csr.png)

1. CSR (.csr) への署名を CA に依頼します。
1. 要求への署名が済んだら、 **[証明書の操作]** タブの **[署名された要求をマージ]** を選択して、署名証明書を Key Vault に追加します。

証明書要求が正常にマージされました。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. 証明書ポリシーを作成します。 このシナリオで選択した CA は提携外であるため、**IssuerName** は **Unknown** に設定されています。また、証明書が Key Vault によって登録されることはなく、更新されることもありません。

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > 値にコンマ (,) が含まれた相対識別名 (RDN) を使用している場合は、値全体 (値のセット) に単一引用符を使用し、特殊文字を含む値を二重引用符で囲みます。 
     >
     >たとえば、**SubjectName** のエントリが `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown` であるとします。 この例では、`OU` 値は **Docs, Contoso** になります。 この形式は、コンマを含むすべての値に使用できます。
     > 
     > この例では、RDN の `OU` には、名前にコンマを含む値が含まれています。 `OU` の結果の出力は、**Docs, Contoso** になります。

1. CSR を作成します。

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. CSR への署名を CA に依頼します。 `$csr.CertificateSigningRequest` は、証明書のベース エンコード CSR です。 この BLOB を発行者の証明書要求 Web サイトにダンプします。 この手順は CA ごとに異なります。 この手順を実行する方法については、CA のガイドラインを参照してください。 また、certreq や openssl などのツールを使用して、CSR に署名し、証明書を生成するプロセスを完了することもできます。

1. 署名済みの要求を Key Vault にマージします。 署名された証明書要求は、その後、Azure Key Vault に作成された最初の秘密キーと公開キーのペアにマージできます。

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

証明書要求が正常にマージされました。

---

## <a name="add-more-information-to-the-csr"></a>その他の情報を CSR に追加する

CSR の作成時にその他の情報を追加したい場合は、それを **SubjectName** 内に定義します。 たとえば、次のような情報を追加できます。
- 国
- 市区町村
- 都道府県
- Organization
- 組織単位

例

   ```azure-powershell
   SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
   ```

> [!NOTE]
> 追加情報を含むドメイン検証 (DV) 証明書を要求しても、CA が要求内の情報をすべて検証できなければ、要求が拒否されることがあります。 これらの情報は、組織検証 (OV) 証明書を要求する場合に追加した方がよいでしょう。

## <a name="faqs"></a>FAQ

- CSR を監視または管理するにはどうすればよいですか?

     「[証明書作成の監視と管理](./create-certificate-scenarios.md)」を参照してください。

- **エラーの種類 "The public key of the end-entity certificate in the specified X.509 certificate content does not match the public part of the specified private key. Please check if certificate is valid (指定された x.509 証明書の内容に含まれるエンド エンティティ証明書の公開キーが、指定された秘密キーの公開部分と一致しません。証明書が有効かどうかをご確認ください)"** が表示されました。どうすればよいですか?

     マージしようとしている署名済みの CSR が、最初に要求した CSR と異なる場合、このエラーが発生する可能性があります。 新たに作成する CSR には、それぞれ秘密キーがあります。署名済みの要求をマージする際は、その秘密キーが一致していなければなりません。

- CSR がマージされるとき、チェーン全体がマージされるのですか?

     はい。マージする .p7b ファイルをユーザーが戻しているなら、チェーン全体がマージされます。

- 発行された証明書が Azure portal で無効状態であった場合はどうなりますか?

     **[証明書の操作]** タブを見て、その証明書のエラー メッセージを確認してください。

- **エラーの種類 "The subject name provided is not a valid X500 name"(指定されたサブジェクト名は有効な X500 名ではありません)** が表示された場合、どうなりますか?

     **SubjectName** に特殊文字が含まれている場合、このエラーが発生することがあります。 Azure portal と PowerShell の手順の「注意」を参照してください。

---

## <a name="next-steps"></a>次のステップ

- [認証、要求、応答](../general/authentication-requests-and-responses.md)
- [Key Vault 開発者ガイド](../general/developers-guide.md)
- [Azure Key Vault REST API リファレンス](/rest/api/keyvault)
- [コンテナー - 作成または更新](/rest/api/keyvault/vaults/createorupdate)
- [コンテナー - アクセス ポリシーの更新](/rest/api/keyvault/vaults/updateaccesspolicy)