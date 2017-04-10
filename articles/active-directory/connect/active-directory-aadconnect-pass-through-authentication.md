---
title: "Azure AD Connect: パススルー認証 | Microsoft Docs"
description: "このトピックでは、Azure AD パススルー認証をオンプレミス Active Directory (AD) で使用して、Azure Active Directory (Azure AD) と接続済みサービスへのアクセス権限を提供する方法を説明します。"
services: active-directory
keywords: "Azure AD Connect とは, Active Directory のインストール, Azure AD に必要なコンポーネント, SSO, シングル サインオン"
documentationcenter: 
author: billmath
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 67b832253619afe789a4dfdb95893e8c0ae62bee
ms.openlocfilehash: 17890fddf948ddc0e89a9107ac5fe65223cd05e1
ms.lasthandoff: 01/30/2017

---

# <a name="what-is-azure-ad-pass-through-authentication"></a>Azure AD パススルー認証とは
会社のリソースとクラウド ベースのサービスへのアクセスに同じ資格情報 (ユーザー名とパスワード) を使用すると、ユーザーはさまざまな資格情報を覚えておく必要がなくなります。 これにより、ユーザーがサインインの方法を忘れる可能性が低減され、パスワードをリセットする際のヘルプ デスクの関与が減るというメリットもあります。

多くの組織が、Azure AD の[パスワード同期](active-directory-aadconnectsync-implement-password-synchronization.md)を使用して、ユーザーが 1 つの資格情報だけでオンプレミスとクラウドのサービスにアクセスできるようにしていますが、各部署が担当するサービスごとにパスワード (場合によってはハッシュ形式のパスワード) を必要とする組織もあります。

Azure AD パススルー認証は、このようなお客様にシンプルなソリューションを提供します。 パススルー認証では、オンプレミス Active Directory に対して Azure AD サービスのパスワード検証を実行できます。 複雑なネットワーク インフラストラクチャを導入したり、クラウドに何らかの形でオンプレミス パスワードが存在したりする必要なく、パスワードを検証できます。

[シングル サインオン](active-directory-aadconnect-sso.md)と組み合わせると、ユーザーは Azure AD や他のクラウド サービスにサインインする際にパスワードを入力する必要がなくなります。 この機能により、会社のコンピューターで真に統合されたエクスペリエンスを実現できます。

![パススルー認証](./media/active-directory-aadconnect-pass-through-authentication/pta1.png)

パススルー認証は Azure AD Connect を使用して構成することができ、パスワードの検証要求をリッスンするシンプルなオンプレミス エージェントを使用します。 エージェントは複数のコンピューターに簡単にデプロイでき、高可用性と負荷分散を実現できます。 すべての通信は送信のみであるため、DMZ を使用したり、DMZ にコネクタをインストールする必要がありません。 コネクタを使用する際のコンピューターの要件は次のとおりです。

- Windows Server 2012 R2 以降
- ユーザーの検証が行われるフォレスト内のドメインに参加済みであること

>[!NOTE]
>フォレスト間に信頼関係があり、名前サフィックス ルーティングが正しく構成されていれば、複数フォレスト環境をサポートできます。

## <a name="supported-clients-in-the-preview"></a>プレビューでサポートされているクライアント

パススルー認証は、Web ブラウザー ベースのクライアントと、[最新の認証](https://aka.ms/modernauthga)をサポートする Office クライアントでサポートされています。 従来の Office クライアントや Exchange Active Sync (モバイル デバイス上のネイティブ電子メール クライアント) など、サポートされていないクライアントの場合、同等の最新の認証手段を使用することをお勧めします。 そうすることで、これらのクライアントでパススルー認証が可能になるだけでなく、多要素認証などの条件付きアクセスを適用することもできます。

現在のところ、Azure AD に参加している Windows 10 を使用している場合は、パススルー認証がサポートされません。 ただし、レガシ クライアントに加え、Windows 10 でもパスワード同期を自動フォールバックとして利用できます。

>[!NOTE]
>プレビューでは、Azure AD Connect のサインイン オプションとしてパススルー認証を選択すると、パスワード同期が既定で有効になります。 この設定は、Azure AD Connect の [オプション] ページで無効にすることができます。

## <a name="how-azure-ad-pass-through-authentication-works"></a>Azure AD パススルー認証のしくみ
ユーザーが Azure AD サインイン ページでユーザー名とパスワードを入力すると、Azure AD はユーザー名とパスワードを検証用の適切なオンプレミス コネクタ キューに配置します。 その後、利用可能ないずれかのオンプレミス コネクタがユーザー名とパスワードを取得し、Active Directory に対して検証します。 検証は標準の Windows API を通じて行われます。これは、Active Directory フェデレーション サービスがパスワードを検証するのと同様の方法です。

次に、オンプレミス ドメイン コントローラーが要求を評価し、コネクタに応答を返します。その後、この応答はコネクタから Azure AD に返されます。 そうすると、Azure AD が応答を評価し、必要に応じてトークンを発行したり、多要素認証を要求したりしてユーザーに応答します。 次の図はさまざまな手順を示しています。

![パススルー認証](./media/active-directory-aadconnect-pass-through-authentication/pta2.png)

## <a name="azure-ad-pass-through-prerequisites"></a>Azure AD パススルーの前提条件
Azure AD のパススルー認証を有効にして使用するには、次の条件を満たす必要があります。

- Azure AD Connect
- 自分がグローバル管理者である Azure AD テナント。

>[!NOTE]
>オンプレミス サービスが失敗したり利用できなくなった場合にテナントの構成を管理できるように、アカウントはクラウド専用の管理者アカウントにすることをお勧めします。

- Azure AD Connect を実行するための、Windows Server 2012 R2 以降を実行しているサーバー。 このコンピューターは、検証対象のユーザーと同じフォレストのメンバーである必要があります。
- Azure AD で検証するユーザーを含むフォレストが複数ある場合は、フォレスト間で信頼関係を設定する必要があります。
- オンプレミスの UserPrincipalName を Azure AD ユーザー名として使用する必要があります。
- 高可用性と負荷分散のための 2 つ目のコネクタを実行するための、Windows Server 2012 R2 を実行する 2 つ目のサーバー。 このコネクタのデプロイ手順については後述します。
- コネクタと Azure AD の間にファイアウォールがある場合は、次の点をご確認ください。
    - URL フィルタリングが有効になっている場合は、コネクタが次の URL と通信できることをご確認ください。
        -  \*.msappproxy.net
        -  \*.servicebus.windows.net  
        -  また、コネクタは [Azure データ センターの IP 範囲](https://www.microsoft.com/en-us/download/details.aspx?id=41653)に対して直接 IP 接続で接続します。
    - コネクタがクライアント証明書を使用して Azure AD と通信する際に、ファイアウォールによって SSL 検査が実行されないことを確認します。
    - コネクタが、次のポートで Azure AD に HTTPS (TCP) 要求を実行できることを確認します。

|ポート番号|説明
| --- | ---
|80|SSL 証明書の失効リストなどのセキュリティ検証用の送信 HTTP トラフィックに使用されます。
|443|    Azure AD に対するユーザー認証に使用されます。
|8080/443|    コネクタのブートストラップ シーケンスのほか、コネクタの自動更新に使用されます。
|9090|    コネクタの登録に使用されます (コネクタ登録プロセスでのみ必要)。
|9091|    コネクタの信頼証明書の自動更新に使用されます。
|9352、5671|    コネクタと Azure AD サービスの間で受信要求の通信に使用されます。
|9350|    [省略可能] 受信要求のパフォーマンス向上に使用されます。
|10100–10120|    コネクタから Azure AD への応答に使用されます。

ご利用のファイアウォールが送信元ユーザーに応じてトラフィックを監視している場合は、Network Service として実行されている Windows サービスを送信元とするトラフィックに対してこれらのポートを開放します。 また、NT Authority\System に対しては必ずポート 8080 を有効にしてください。

## <a name="enabling-pass-through-authentication"></a>パススルー認証の有効化
Azure AD パススルー認証は Azure AD Connect を通じて有効にできます。 パススルー認証を有効にすると、Azure AD Connect と同じサーバーに 1 つ目のコネクタがデプロイされます。 Azure AD Connect をインストールするときに、カスタム インストールを選択し、サインインのオプション ページでパススルー認証を選択します。 詳細については、「[Azure AD Connect のカスタム インストール](active-directory-aadconnect-get-started-custom.md)」をご覧ください。

![シングル サインオン](./media/active-directory-aadconnect-sso/sso3.png)

既定では、1 つ目のパススルー認証コネクタは Azure AD Connect サーバーにインストールされます。 認証要求の高可用性と負荷分散を利用する場合は、別のコンピューターに 2 つ目のコネクタをデプロイする必要があります。

2 つ目のコネクタをデプロイするには、次の手順を実行します。

### <a name="step-1-install-the-connector-without-registration"></a>手順 1: 登録せずにコネクタをインストールする

1.    最新の[コネクタ](https://go.microsoft.com/fwlink/?linkid=837580)をダウンロードします。
2.    管理者としてコマンド プロンプトを開きます。
3.    次のコマンドを実行します。/q はサイレント インストールを意味します。つまり、インストールで使用許諾契約書への同意を求めるメッセージは表示されません。

```
AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
```

### <a name="step-2-register-the-connector-with-azure-ad-for-pass-through-authentication"></a>手順 2: パススルー認証用に Azure AD にコネクタを登録します。

1.    管理者として PowerShell ウィンドウを開きます。
2.    **C:\Program Files\Microsoft AAD App Proxy Connector** に移動し、次のスクリプトを実行します。  
`.\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Feature PassthroughAuthentication`
3.    メッセージが表示されたら、Azure AD テナント管理者アカウントの資格情報を入力します。

## <a name="troubleshooting-pass-through-authentication"></a>パススルー認証のトラブルシューティング
パススルー認証のトラブルシューティングにおいては、発生する問題をいくつかのカテゴリに分けることができます。 問題の種類に応じて、確認する場所が異なる場合があります。

コネクタに関するエラーの場合、コネクタ コンピューターの **Application and Service Logs\Microsoft\AadApplicationProxy\Connector\Admin** のイベント ログを確認します。 必要に応じて、分析とデバッグ ログを表示し、コネクタのセッション ログを有効にすることで、より詳細なログを使用できます。 このログを既定で有効にして実行することはお勧めしません。内容はログを無効にした後にのみ表示されます。

**C:\Programdata\Microsoft\Microsoft AAD Application Proxy Connector\Trace** にあるコネクタのトレース ログでも詳細を確認できます。 これらのログにも、個々のユーザーのパススルー認証の失敗の原因が記録されます。たとえば、次のエントリにはエラー コード 1328 が記録されています。

```
    ApplicationProxyConnectorService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

コマンド プロンプトを起動し、次のコマンドを実行すると、エラーの詳細を取得できます ("1328" を要求のエラー番号に置き換えてください)。

`Net helpmsg 1328`

結果は次の応答のようになります。

![パススルー認証](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

監査ログが有効になっている場合、ドメイン コントローラーのセキュリティ ログでも詳細を確認できます。 コネクタによる認証要求に対しては次のようにクエリを実行します。

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe')]]</Select>
    </Query>
    </QueryList>
```

Azure AD のサインイン画面に表示されるその他のエラーと適切な解決手順を次に示します。

|エラー|Description|解決策
| --- | --- | ---
|AADSTS80001|Unable to connect to Active Directory (Active Directory に接続できません)|Ensure that the connector machines are domain joined and are able to connect to Active Directory. (コネクタ コンピューターがドメインに参加しており、Active Directory に接続できることを確認してください)  
|AADSTS8002|A timeout occurred connecting to Active Directory (Active Directory への接続中にタイムアウトが発生しました)|Check to ensure that Active Directory is available and responding to requests from the connector. (Active Directory が使用可能で、コネクタからの要求に応答していることを確認してください)
|AADSTS80004|The username passed to the connector was not valid (コネクタに渡されたユーザー名が無効です)|サインインしようとしているユーザーのユーザー名が正しいことを確認してください。
|AADSTS80005|Validation encountered unpredictable WebException (検証で予測外の WebException が発生しました)|このエラーは一時的な問題です。 要求をやり直してください。 引き続き失敗する場合は、Microsoft サポートに連絡してください。
|AADSTS80007|An error occurred communicating with Active Directory (Active Directory との通信中にエラーが発生しました)|Check the connector logs for more information and verify that Active Directory is operating as expected. (コネクタ ログで詳細を確認し、Active Directory が期待通りに動作していることを確認してください)

