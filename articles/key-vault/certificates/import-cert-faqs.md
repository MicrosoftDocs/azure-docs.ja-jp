---
title: よく寄せられる質問 - Azure Key Vault 証明書のインポート
description: Azure Key Vault 証明書のインポートに関してよく寄せられる質問にお答えします。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 78ec0af0acbb74436af16abba75d64de061d5268
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202167"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Azure Key Vault 証明書のインポートに関する FAQ

この記事では、Azure Key Vault 証明書のインポートに関してよく寄せられる質問にお答えします。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Azure Key Vault に証明書をインポートするには、どうすればよいですか?

証明書インポート操作の場合、Azure Key Vault では次の 2 つの証明書ファイル形式が受け付けられます: PEM と PFX。 公開部分のみの PEM ファイルがありますが、Azure Key Vault では、秘密キーを含む PEM または PFX ファイルが必要であり、それだけが受け付けられます。 詳細については、「[証明書を Key Vault にインポートする](./tutorial-import-certificate.md#import-a-certificate-to-key-vault)」を参照してください。

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>パスワードで保護された証明書を Key Vault にインポートした後、それをダウンロードすると、それに関連付けられているパスワードが表示されないのはなぜですか?
    
証明書が Key Vault にインポートされて保護された後は、関連付けられているパスワードは保存されません。 パスワードは、インポート操作の間に 1 回だけ必要です。 これは設計によるものですが、いつでも証明書をシークレットとして取得し、[Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx) を使用してパスワードを追加することにより、Base64 から PFX に変換することができます。

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>"パラメーターが正しくありません" というエラーを解決するにはどうすればよいですか? Key Vault にインポートに対してサポートされている証明書の形式は何ですか?

証明書をインポートするときは、キーがファイルに含まれていることを確認する必要があります。 秘密キーが異なる形式で別に格納されている場合は、キーと証明書を組み合わせる必要があります。 証明機関 (CA) によっては、他の形式で証明書が提供される場合があります。 そのため、証明書をインポートする前に、それが PEM または PFX ファイル形式であること、およびキーで Rivest–Shamir–Adleman (RSA) または楕円曲線暗号 (ECC) のいずれかの暗号化が使用されていることを確認してください。 

詳細については、[証明書の要件](./certificate-scenarios.md#formats-of-import-we-support)および[証明書キーの要件](../keys/about-keys.md)に関する記事を参照してください。

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>ARM テンプレートを使用して証明書をインポートできますか?

いいえ。Azure Resource Manager (ARM) テンプレートを使用して証明書の操作を実行することはできません。 推奨される回避策は、Azure API、Azure CLI、または PowerShell での証明書インポート方法を使用することです。 既存の証明書がある場合は、それをシークレットとしてインポートできます。

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>Azure portal を使用して証明書をインポートすると、"問題が発生しました" というエラーが発生します。 詳しく調査するにはどうすればよいですか?
    
さらにわかりやすいエラーを表示するには、[Azure CLI](/cli/azure/keyvault/certificate#az-keyvault-certificate-import) または [PowerShell](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0) を使用して証明書ファイルをインポートします。

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>次のエラーはどうすれば解決できますか: "エラーの種類: アクセスが拒否されたか、ユーザーに証明書をインポートする権限がない" ?
    
インポート操作では、証明書をインポートするためのアクセス許可を、アクセス ポリシーでユーザーに付与する必要があります。 これを行うには、キーコンテナーに移動し、 **[アクセス ポリシー]**  >  **[アクセス ポリシーの追加]**  >  **[Select Certificate Permissions]\(証明書のアクセス許可の選択\)**  >  **[プリンシパル]** を選択して、ユーザーを検索し、ユーザーのメール アドレスを追加します。 

証明書関連のアクセス ポリシーの詳細については、「[Azure Key Vault の証明書について](./about-certificates.md#certificate-access-control)」を参照してください。


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>次のエラーはどうすれば解決できますか: "エラーの種類: 証明書の作成時に競合が発生する" ?
    
各証明書の名前は、一意でなければなりません。 同じ名前の証明書が、論理的に削除された状態になっている可能性があります。 また、[証明書の構成](./about-certificates.md#composition-of-a-certificate)に従うと、新しい証明書の作成時に、同じ名前を持つアドレス指定可能なシークレットが作成されます。このため、証明書に指定しようとしているのと同じ名前を持つ別のキーまたはシークレットがキー コンテナーに存在する場合は、証明書の作成が失敗し、そのキーまたはシークレットを削除するか、証明書に別の名前を使用する必要があります。 

詳細については、[削除された証明書の取得操作](/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)に関する記事を参照してください。

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>"エラーの種類: 文字の長さが長すぎる" が返されるのはなぜですか?
このエラーは次の 2 つの理由により発生する場合があります。    
* 証明書のサブジェクト名は、200 文字までに制限されています。
* 証明書のパスワードは、200 文字までに制限されています。


### <a name="error-the-specified-pem-x509-certificate-content-is-in-an-unexpected-format-please-check-if-certificate-is-in-valid-pem-format"></a>エラー "指定された PEM X.509 証明書の内容は、予期しない形式です。 証明書が有効な PEM 形式かどうかをご確認ください"。
PEM ファイルのコンテンツが UNIX スタイルの行区切り記号 `(\n)` を使用していることを確認してください。

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>有効期限が切れた証明書を Azure Key Vault にインポートできますか?
    
いいえ、期限切れの PFX 証明書を Key Vault にインポートすることはできません。

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>証明書を適切な形式に変換するには、どうすればよいですか?

CA に対して、必要な形式で証明書を提供するように依頼できます。 また、証明書を適切な形式に変換するのに役立つサードパーティ製のツールもあります。

### <a name="can-i-import-certificates-from-non-partner-cas"></a>パートナー以外の CA から証明書をインポートできますか?
はい、任意の CA から証明書をインポートできますが、キー コンテナーでそれらを自動的に更新することはできません。 証明書の期限切れが通知されるようにリマインダーを設定できます。

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>パートナーの CA から証明書をインポートした場合、自動更新機能は引き続き機能しますか?
はい。 証明書をアップロードした後、証明書の発行ポリシーで自動ローテーションを指定してください。 設定は、次のサイクルまたは証明書のバージョンがリリースされるまで有効です。

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Key Vault にインポートした App Service 証明書を表示できないのはなぜですか? 
証明書を正常にインポートした場合は、 **[シークレット]** ペインに移動して確認できます。


## <a name="next-steps"></a>次のステップ

- [Azure Key Vault 証明書](./about-certificates.md)
