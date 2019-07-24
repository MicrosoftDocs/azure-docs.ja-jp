---
title: カスタム アプリ用の Azure MFA ソフトウェア開発キット - Azure Active Directory
description: この記事では、Azure MFA SDK をダウンロードし、それを使用して、カスタム アプリの 2 段階検証を有効にする方法を示します。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b6f5def70dcb2564e92c04e53b5d2ef5f0631fd
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58310865"
---
# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>カスタム アプリに Multi-Factor Authentication を構築する (SDK)

> [!IMPORTANT]
> Azure Multi-Factor Authentication ソフトウェア開発キット (SDK) の廃止が発表されました。 新規のお客様については、この機能はサポートされなくなります。 現在のお客様は、2018 年 11 月 14 日まで、SDK の使用を継続できます。 その後は、SDK への呼び出しは失敗します。 

Azure Multi-factor Authentication ソフトウェア開発キット (SDK) を使用して、Azure AD テナントのアプリケーションで行われるサインインまたはトランザクション プロセスに 2 段階認証を直接組み込むことができます。

Multi-factor Authentication SDK では、C#、Visual Basic (.NET)、Java、Perl、PHP、および Ruby を使用できます。 SDK は、2 段階認証を囲む薄いラッパーを提供します。 これには、コメント付きのソース コード ファイル、サンプル ファイル、詳細な ReadMe ファイルなど、コードを記述するために必要なすべてのものが含まれます。 また、各 SDK には、Multi-Factor Authentication プロバイダーに固有のトランザクションを暗号化するための証明書と秘密キーが含まれます。 プロバイダーがある限り、さまざまな言語と形式の SDK を必要な分だけダウンロードできます。

Multi-factor Authentication SDK に含まれる API の構造は単純です。 API に対する関数呼び出しを、多要素認証パラメーター (検証モードなど) とユーザー データ (通話するための電話番号や検証するための PIN 番号など) を指定して実行します。 API は関数呼び出しをクラウド ベースの Azure Multi-Factor Authentication サービスに対する Web サービス要求に変換します。 すべての呼び出しには、各 SDK に含まれるプライベート証明書への参照が含まれている必要があります。

API は Azure Active Directory に登録されたユーザーにアクセスできないため、ファイルまたはデータベースでユーザー情報を提供する必要があります。 また、API には登録機能やユーザー管理機能が用意されていないため、これらのプロセスをアプリケーションに作成する必要があります。

> [!IMPORTANT]
> SDK をダウンロードするには、Azure MFA、AAD Premium、または EMS ライセンスを所有している場合でも、Azure多要素認証プロバイダーを作成する必要があります。 この目的のために Azure 多要素認証プロバイダーを作成し、ライセンスを既に所有している場合は、プロバイダーが**有効化されたユーザーごと**のモデルで作成されていることを確認します。 プロバイダーを作成したら、Azure MFA、Azure AD Premium、または EMS のライセンスが保存されているディレクトリにリンクします。 この構成により、所有しているライセンス数よりも SDK を使用する一意のユーザーの数が多い場合でも、適切な課金が行われます。


## <a name="download-the-sdk"></a>SDK のダウンロード
Azure Multi-Factor SDK のダウンロードには、 [Azure Multi-Factor Auth プロバイダー](concept-mfa-authprovider.md)が必要です。  これには、Azure MFA、Azure AD Premium、または Enterprise Mobility Suite のライセンスを所有している場合でも、完全な Azure サブスクリプションが必要です。 SDK は非推奨となったため、公開されていた SDK のダウンロード方法は使用できなくなりました。 SDK をダウンロードする必要がある場合は、Microsoft のサポート ケースを開く必要があります。 この SDK は既に SDK を使用しているお客様にのみ提供されます。 新規のお客様には提供されません。

## <a name="whats-in-the-sdk"></a>SDK の内容
SDK には、以下のアイテムが含まれています。

* **README**。 新規または既存のアプリケーションで、Multi-Factor Authentication API を使用する方法について説明します。
* Multi-Factor Authentication 用の**ソース ファイル**
* **クライアント証明書** 
* **秘密キー** 
* **呼び出し結果。**  呼び出し結果コードの一覧です。 このファイルを開くには、ワードパッドなど、テキストの書式設定があるアプリケーションを使用します。 呼び出し結果コードは、アプリケーションに対する Multi-Factor Authentication の実装のテストとトラブルシューティングに使用します。 これらは認証ステータス コードではありません。
* **例。**  Multi-Factor Authentication の基本的な実装作業のサンプル コードです。

> [!WARNING]
> クライアント証明書は、特定のユーザー向けに生成された一意のプライベート証明書です。 このファイルを共有しないよう、または紛失にはご注意ください。 これは、Multi-Factor Authentication サービスとの通信のセキュリティを確保するためのキーです。

## <a name="code-sample"></a>サンプル コード
このコード サンプルでは、Azure Multi-Factor Authentication SDK で API を使用して、アプリケーションに標準モードの音声通話検証を追加する方法を示します。 標準モードとは、ユーザーが # キーを押すことで応答する通話のことです。

このサンプルでは、C# のサーバー側ロジックがある基本的な ASP.NET アプリケーションで C# .NET 2.0 Multi-Factor Authentication SDK を使用していますが、他の言語でもプロセスはほぼ同じです。 SDK には実行可能ファイルの代わりにソース ファイルが含まれるため、ファイルを作成してそれらを参照することや、それらをアプリケーションに直接追加することもできます。

> [!NOTE]
> Multi-Factor Authentication を実装する際は、第 2 または第 3 の検証方法として通話またはテキスト メッセージを追加で使用することで、プライマリ (第 1 の) 認証方法 (ユーザー名とパスワード) を補完します。 これらの方法は、プライマリ認証方法として使用するようには設計されていません。

### <a name="code-sample-overview"></a>コード サンプルの概要
単純な Web デモ アプリケーション用のこのサンプル コードでは、通話と # キー応答を使用してユーザーの認証を確認します。 この通話要素は、Multi-Factor Authentication では標準モードと呼ばれます。

クライアント側のコードに Multi-Factor Authentication に固有の要素は含まれません。 追加の認証要素はプライマリ認証とは無関係であるため、既存のサインオン インターフェイスを変更することがなく追加することができます。 Multi-Factor SDK の API を使用するとユーザー エクスペリエンスをカスタマイズできますが、何も変更しなくても問題ありません。

サーバー側のコードでは、手順 2 で標準モード認証を追加します。 これにより、標準モード認証に必要なパラメーター (ユーザー名、電話番号、およびモード) を持つ PfAuthParams オブジェクトと、各呼び出しで必要なクライアント証明書へのパス (CertFilePath) が作成されます。 PfAuthParams 内のすべてのパラメーターのデモを表示するには、SDK のサンプル ファイルを参照してください。

次に、コードは PfAuthParams オブジェクトを pf_authenticate() 関数に渡します。 戻り値は認証の成否を示します。 out パラメーター (callStatus と errorID) には、呼び出し結果の追加情報が含まれています。 呼び出し結果コードは、SDK の呼び出し結果ファイルで説明しています。

この最も小さな実装は、数行で記述できます。 ただし、運用環境のコードではより高度なエラー処理、追加のデータベース コード、およびより優れたユーザー エクスペリエンスが含まれます。

### <a name="web-client-code"></a>Web クライアント コード
デモ ページの Web クライアント コードを次に示します。

    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="\_Default" %>

    <!DOCTYPE html>

    <html xmlns="https://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>サーバー側コード
次のサーバー側コードでは、手順 2 でMulti-Factor Authentication を構成して実行します。 標準モード (MODE_STANDARD) とは、ユーザーが # キーを押して応答する通話のことです。

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class \_Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "5555555555";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = "Multi-Factor Authentication failed.";
                }
            }

        }
    }
