---
title: Web アプリ チュートリアル - Azure API for FHIR への接続をテストする
description: これらのチュートリアルでは、単純な Web アプリケーションをデプロイする例について説明します。 チュートリアルのこのセクションでは、Postman を使用して FHIR サーバーへの接続をテストする手順について説明します
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: 1c64468a2e420734ca51a5b9308bb52e13712c51
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018883"
---
# <a name="testing-the-fhir-api"></a>FHIR API をテストする
前の 2 つの手順では、Azure API for FHIR をデプロイし、クライアント アプリケーションを登録しました。 これで、Azure API for FHIR がクライアント アプリケーションでセットアップされていることをテストする準備ができました。 

## <a name="retrieve-capability-statement"></a>機能ステートメントを取得する
最初に、Azure API for FHIR の機能ステートメントを取得します。 
1. Postman を開きます
1. GET https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com/metadata を実行して、機能ステートメントを取得します。 次の図では、FHIR サーバー名が **fhirserver** になっています。

![機能ステートメント](media/tutorial-web-app/postman-capability-statement.png)

次に、患者の取得を試みます。 患者を取得するには、「GET https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com/Patient」と入力します。 "401 権限がありません" エラーを受け取ります。 このエラーは、患者データへのアクセス権を持っていることが証明されていないために発生します。

## <a name="get-patient-from-fhir-server"></a>FHIR サーバーから患者を取得する
![失敗した患者](media/tutorial-web-app/postman-patient-authorization-failed.png)

アクセスできるようにするには、アクセス トークンが必要です。
1. Postman で **[Authorization]\(認可\)** を選択し、種類を **OAuth2.0** に設定します
1. **[Get New Access Token]\(新しいアクセス トークンの取得\)** を選択します
1. 各フィールドに入力し、 **[Request Token]\(トークンの要求\)** を選択します。 以下に、このチュートリアルでの各フィールドの値を示します。

|フィールド                |値                                                               |
|---------------------|--------------------------------------------------------------------|
|Token Name (トークン名)           |トークンの名前                                               |
|付与タイプ           |Authorization Code (承認コード)                                                  |
|コールバック URL         |https://www.getpostman.com/oauth2/callback                          |
|認証 URL             |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID> /oauth2/?resource=https://\<FHIR-SERVER-NAME>.azurehealthcareapis.com|
|Access Token URL (アクセス トークン URL)     |https://login.microsoftonline.com/\<AZURE-AD-TENANT-ID> /oauth2/token|
|クライアント ID            |前の手順でコピーしたクライアント ID             |
|クライアント シークレット        |\<BLANK>                                                            |
|Scope                |\<BLANK>                                                            |
|State                |1234                                                                |
|クライアント認証|Send client credentials in body (本文でクライアントの資格情報を送信する)                                     |

4. 自分の資格情報でサインインし、 **[Accept]\(同意する\)** を選択します
1. 結果を下へスクロールし、 **[Use Token]\(トークンの使用\)** を選択します
1. 上部の **[Send]\(送信\)** をもう一度選択すると、今度は次の結果が得られるはずです![成功した患者](media/tutorial-web-app/postman-patient-authorization-success.png)

## <a name="post-patient-into-fhir-server"></a>FHIR サーバーに患者を投稿する
これで、アクセスできるようになりました。新しい患者を作成できます。 以下に、FHIR サーバーに追加できる簡単な患者のサンプルを示します。 次のコードを Postman の **[Body]\(本文\)** セクションに入力します。

``` json
    {
        "resourceType": "Patient",
        "active": true,
        "name": [
            {
                "use": "official",
                "family": "Kirk",
                "given": [
                    "James",
                    "Tiberious"
                ]
            },
            {
                "use": "usual",
                "given": [
                    "Jim"
                ]
            }
        ],
        "gender": "male",
        "birthDate": "1960-12-25"
    }
```
この POST では、"James Tiberious Kirk" という名前の新しい患者が FHIR サーバーに作成されます。
![患者を投稿する](media/tutorial-web-app/postman-post-patient.png)

上の GET の手順を実行して患者を再度取得すると、出力の一覧に James Tiberious Kirk が表示されます。

## <a name="troubleshooting-access-issues"></a>アクセスに関する問題をトラブルシューティングする
これらの手順の実行中に問題が発生した場合は、Azure Active Directory と Azure API for FHIR に関してまとめられたドキュメントを確認してください。 

* [Azure AD と Azure API for FHIR](azure-ad-hcapi.md) - このドキュメントでは、Azure Active Directory の基本的な原則の一部と、Azure Active Directory が Azure API for FHIR とどのように対話するかについて概説しています。
* [アクセス トークンの検証](azure-ad-hcapi-token-validation.md) - この攻略ガイドでは、アクセス トークンの検証について詳しく説明し、アクセスの問題を解決するための手順を示しています。

## <a name="next-steps"></a>次の手順
クライアント アプリケーションに正常に接続できるようになったので、Web アプリケーションを記述する準備ができました。

>[!div class="nextstepaction"]
>[Web アプリケーションを記述する](tutorial-web-app-write-web-app.md)



