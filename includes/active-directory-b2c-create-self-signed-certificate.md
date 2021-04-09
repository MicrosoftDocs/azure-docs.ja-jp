---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 41d9962657aa81dbe34a52302d1b68ec655f2893
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095291"
---
証明書をまだ持っていない場合は、自己署名証明書を使用できます。 自己署名証明書は、証明機関 (CA) によって署名されていないセキュリティ証明書であり、CA によって署名された証明書のセキュリティ保証を提供するものではありません。 

# <a name="windows"></a>[Windows](#tab/windows)

Windows では、PowerShell の [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) コマンドレットを使用して証明書を生成します。

1. この PowerShell コマンドを実行して、自己署名証明書を生成します。 アプリケーションと Azure AD B2C のテナント名に合わせて `-Subject` 引数を変更します。 また、証明書に別の有効期限を指定するように `-NotAfter` 日付を調整することもできます。

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. **[ユーザー証明書の管理]**  >  **[現在のユーザー]**  >  **[個人用]**  >  **[証明書]**  > *yourappname.yourtenant.onmicrosoft.com* を開きます。
1. 証明書を選択し、 **[アクション]**  >  **[すべてのタスク]**  >  **[エクスポート]** の順に選択します。
1. **[はい]**  >  **[次へ]**  >  **[はい、秘密キーをエクスポートします]**  >  **[次へ]** を選択します。
1. **[エクスポート ファイルの形式]** の既定値を受け入れます。
1. 証明書のパスワードを指定します。

Azure AD B2C で .pfx ファイルのパスワードを受け入れるには、Windows 証明書ストアのエクスポート ユーティリティで、AES256-SHA256 ではなく、TripleDES-SHA1 オプションを使用してパスワードを暗号化する必要があります。

# <a name="macos"></a>[macOS](#tab/macos)

macOS では、キーチェーン アクセスで[証明書アシスタント](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0)を使用して証明書を生成します。

1. [Mac 上でキーチェーン アクセスで自己署名証明書を作成する](https://support.apple.com/guide/keychain-access/kyca8916/mac)方法の手順に従ってください。
1. Mac 上のキーチェーン アクセス アプリで、作成した証明書を選択します。
1. **[ファイル]**  >  **[項目のエクスポート]** を選択します。
1. 証明書を保存するファイル名を選択します。 たとえば、**self-signed-certificate.p12** など。
1. **[ファイル形式]** については、**PEM (p12)** を選択します。
1. **[保存]** を選択します。
1. **パスワード** を入力し、パスワードを **確認** します。
1. ファイル拡張子を `.pfx` に置き換えます。 たとえば、**self-signed-certificate.pfx** など。

---