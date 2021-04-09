---
title: 証明書を使用した、AD Reporting API のチュートリアル |Microsoft Docs
description: このチュートリアルでは、Azure AD Reporting API と証明書の資格情報を使い、ユーザーの介入なしでディレクトリからデータを取得する方法について説明します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: c3443cb73e85fc69349e7293597a5f4a723959d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93130053"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>チュートリアル:Azure Active Directory Reporting API と証明書を使用したデータの取得

[Azure Active Directory (Azure AD) レポート API](concept-reporting-api.md) は、一連の REST ベースの API を使用してデータへのプログラムによるアクセスを提供します。 これらの API は、さまざまなプログラミング言語とツールから呼び出すことができます。 ユーザーの介入なしに Azure AD Reporting API にアクセスする場合は、証明書を使用するようにアクセスを構成する必要があります。

このチュートリアルでは、テスト証明書を使用してレポート用の MS Graph API にアクセスする方法について説明します。 このテスト証明書は、運用環境に使わないことをお勧めします。 

## <a name="prerequisites"></a>前提条件

1. サインイン データにアクセスするには、プレミアム (P1/P2) ライセンスを持つ Azure Active Directory テナントがあることを確認してください。 Azure Active Directory エディションにアップグレードするには、「[Azure Active Directory Premium の概要](../fundamentals/active-directory-get-started-premium.md)」を参照してください。 アップグレード前のアクティビティ データがない場合は、プレミアム ライセンスにアップグレードしてからレポートにデータが表示されるようになるまでに数日かかることに注意してください。 

2. テナントの **グローバル管理者**、**セキュリティ管理者**、**セキュリティ閲覧者**、または **レポート閲覧者** ロールに含まれているユーザー アカウントに切り替えるか、そのようなアカウントを新たに作成します。 

3. [Azure Active Directory Reporting API にアクセスするための前提条件](howto-configure-prerequisites-for-reporting-api.md)を満たします。 

4. [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md) をダウンロードしてインストールします。

5. [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/) をインストールします。 このモジュールには、いくつかのユーティリティ コマンドレットが用意されています。その例を次に示します。
    - 認証に必要な ADAL ライブラリ
    - ADAL を使用してユーザー キー、アプリケーション キー、証明書からアクセス トークンを取得
    - Graph API でページ単位の結果を処理

6. 初めてモジュールを使う場合は、**Install-MSCloudIdUtilsModule** を実行します。初めてではない場合は、**Import-Module** PowerShell コマンドを使ってモジュールをインポートします。 セッションは次のような画面になります。![Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. **New-SelfSignedCertificate** PowerShell コマンドレットを使用して、テスト証明書を作成します。

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. **Export-Certificate** コマンドレットを使用して、テスト証明書を証明書ファイルにエクスポートします。

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Azure Active Directory Reporting API と証明書を使用したデータの取得

1. [Azure portal](https://portal.azure.com) に移動し、 **[Azure Active Directory]** 、 **[アプリの登録]** の順に選択し、リストからアプリケーションを選択します。 

2. [アプリケーションの登録] ブレードの **[管理]** セクションで **[Certificates & secrets]\(証明書とシークレット\)** を選択し、 **[証明書のアップロード]** を選択します。

3. 前の手順の証明書ファイルを選択し、 **[追加]** を選択します。 

4. アプリケーション ID と、アプリケーションで登録した証明書のサムプリントを書き留めます。 サムプリントを調べるには、ポータルのアプリケーション ページから **[管理]** セクションの **[Certificates & secrets]\(証明書とシークレット\)** に移動します。 サムプリントは **[証明書]** 一覧の下にあります。

5. インライン マニフェスト エディターでアプリケーション マニフェストを開き、次に示すように *keyCredentials* プロパティが新しい証明書情報で更新されていることを確認します 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ``` 
6. これで、この証明書を使用して MS Graph API のアクセス トークンを取得できるようになりました。 MSCloudIdUtils PowerShell モジュールの **Get-MSCloudIdMSGraphAccessTokenFromCert** コマンドレットを使い、前の手順で取得したアプリケーション ID とサムプリントを渡します。 

   ![PowerShell ウィンドウを示すスクリーンショット。アクセス トークンを作成するコマンドが表示されています。](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. PowerShell スクリプトでアクセス トークンを使用して、Graph API のクエリを実行します。 MSCloudIDUtils の **Invoke-MSCloudIdMSGraphQuery** コマンドレットを使って SignIns と directoryAudits エンドポイントを列挙します。 複数のページにわたる結果を処理し、それらの結果を PowerShell パイプラインに送っています。

8. directoryAudits エンドポイントを照会して、監査ログを取得します。 

   ![PowerShell ウィンドウを示すスクリーンショット。前の手順で作成したアクセス トークンを使用して、directoryAudits エンドポイントにクエリを実行するコマンドが表示されています。](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. SignIns エンドポイントを照会して、サインイン ログを取得します。

    ![PowerShell ウィンドウを示すスクリーンショット。前の手順で作成したアクセス トークンを使用して、Signins エンドポイントにクエリを実行するコマンドが表示されています。](./media/tutorial-access-api-with-certificates/query-signins.png)

10. このデータを CSV にエクスポートし、SIEM システムに保存できるようになります。 また、スケジュールされたタスクにスクリプトをラップすれば、ソース コードにアプリケーション キーを保存せずに Azure AD データをテナントから定期的に取得することができます。 

## <a name="next-steps"></a>次のステップ

* [Reporting API の概要を把握します](concept-reporting-api.md)
* [監査 API リファレンス](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [サインイン アクティビティ レポート API リファレンス](/graph/api/resources/signin?view=graph-rest-beta)