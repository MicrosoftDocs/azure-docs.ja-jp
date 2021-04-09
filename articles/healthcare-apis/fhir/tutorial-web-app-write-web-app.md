---
title: Web アプリ チュートリアル - Web アプリケーションを記述する
description: このチュートリアルでは、単純な Web アプリケーションをデプロイする例について説明します。 チュートリアルのこのセクションでは、Web アプリケーションを記述する手順について説明します。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 01/03/2020
ms.openlocfilehash: bb9c206a17a11f0cf710ac4ee3ac7ccae0fa9eb9
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019481"
---
# <a name="write-azure-web-application-to-read-fhir-data"></a>FHIR データを読み取るための Azure Web アプリケーションを記述する
FHIR サーバーに接続してデータを投稿できるようになったので、FHIR データを読み取る Web アプリケーションを記述する準備ができました。 このチュートリアルの最後の手順では、Web アプリケーションの記述とアクセスの手順について説明します。

## <a name="create-web-application"></a>Web アプリケーションを作成する
Azure で **[リソースの作成]** を選択し、 **[Web アプリ]** を選択します。 クライアント アプリケーションのリダイレクト URI で指定した名前を Web アプリケーションに付けてください。または、リダイレクト URI の設定に戻り、新しい名前で更新してください。 

![Web アプリケーションを作成する](media/tutorial-web-app/create-web-app.png)

Web アプリケーションが使用可能になったら、 **[リソースに移動]** を選択します。 右側で開発ツールの下の **[App Service Editor (プレビュー)]** を選択し、 **[移動]** を選択します。 [移動] を選択すると、App Service Editor が開きます。 *[Explore]\(探索\)* の下にある灰色の領域を右クリックし、**index.html** という名前の新しいファイルを作成します。

以下に、**index.html** に入力するコードを示します。 以下の項目を更新する必要があります。
* **clientId** - クライアント アプリケーション ID で更新します。 この ID は、トークンを取得するときに受け取ったのと同じ ID になります
* **authority** - Azure AD テナント ID で更新します
* **FHIRendpoint** - FHIRendpoint が FHIR サービス名を持つように更新します
* **scopes** - 対象ユーザーの完全な URL を反映するように更新します

``` HTML

<!DOCTYPE html>
<html>

<head>
    <title>FHIR Patient browser sample app</title>
    <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
</head>

<body>
    <div class="leftContainer">
        <p id="WelcomeMessage">Welcome to the FHIR Patient browsing sample Application</p>
        <button id="SignIn" onclick="signIn()">Sign In</button>
    </div>

    <div id="patientTable">
    </div>

    <script>
        var msalConfig = {
            auth: {
                clientId: '<CLIENT-ID>',
                authority: "https://login.microsoftonline.com/<AZURE-AD-TENANT-ID>"
            },
            cache: {
                cacheLocation: "localStorage",
                storeAuthStateInCookie: true
            }
        }

        var FHIRConfig = {
            FHIRendpoint: "https://<FHIR-SERVER-NAME>.azurehealthcareapis.com"
        }
        var requestObj = {
            scopes: ["https://<FHIR-SERVER-NAME>.azurehealthcareapis.com/user_impersonation"]
        }

        function authRedirectCallBack(error, response) {
            if (error) {
                console.log(error);
            } else {
                if (response.tokenType === "access_token") {
                    callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, response.accessToken, FHIRCallback);
                }
            }
        }

        var myMSALObj = new Msal.UserAgentApplication(msalConfig);
        myMSALObj.handleRedirectCallback(authRedirectCallBack);

        function signIn() {
            myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
                showWelcomeMessage();
                acquireTokenPopupAndCallFHIRServer();
            }).catch(function (error) {
                console.log(error);
            })
        }

        function showWelcomeMessage() {
            var divWelcome = document.getElementById('WelcomeMessage');
            divWelcome.innerHTML = "Welcome " + myMSALObj.getAccount().userName + " to FHIR Patient Browsing App";
            var loginbutton = document.getElementById('SignIn');
            loginbutton.innerHTML = 'Sign Out';
            loginbutton.setAttribute('onclick', 'signOut()')
        }

        function signOut() {
            myMSALObj.logout();
        }

        function acquireTokenPopupAndCallFHIRServer() {
            myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
                callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
            }).catch(function (error) {
                console.log(error);
                if (requiresInteraction(error.errorCode)) {
                    myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                        callFHIRServer(FHIRConfig.FHIRendpoint + '/Patient', 'GET', null, tokenResponse.accessToken, FHIRCallback);
                    }).catch(function (error) {
                        console.log(error);
                    })
                }
            });
        }

        function callFHIRServer(theUrl, method, message, accessToken, callBack) {
            var xmlHttp = new XMLHttpRequest();
            xmlHttp.onreadystatechange = function () {
                if (this.readyState == 4 && this.status == 200)
                    callBack(JSON.parse(this.responseText));
            }
            xmlHttp.open(method, theUrl, true);
            xmlHttp.setRequestHeader("Content-Type", "application/json;charset=UTF-8");
            xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
            xmlHttp.send(message);
        }

        function FHIRCallback(data) {
            patientListHtml = '<ol>';
            data.entry.forEach(function(e) {
                patientListHtml += '<li>' + e.resource.name[0].family + ', ' + e.resource.name[0].given + ' (' + e.resource.id + ')';
            });
            patientListHtml += '</ol>';
            document.getElementById("patientTable").innerHTML = patientListHtml;
        }
    </script>
</body>

</html>
```

ここから Web アプリケーションのリソースに戻り、[概要] ページで見つけた URL を開くことができます。 ログインして、以前に作成した患者の James Tiberious Kirk を表示します。

## <a name="next-steps"></a>次の手順
これで、Azure API for FHIR のデプロイ、パブリック クライアント アプリケーションの登録、アクセスのテスト、および小規模な Web アプリケーションの作成が正常に完了しました。 次の手順として、サポートされている Azure API for FHIR の機能を確認します。

>[!div class="nextstepaction"]
>[サポートされている機能](fhir-features-supported.md)





