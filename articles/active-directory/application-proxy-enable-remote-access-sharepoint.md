---
title: "Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスの有効化 | Microsoft Docs"
description: "オンプレミスの SharePoint サーバーを Azure AD アプリケーション プロキシと統合する方法の基礎について説明します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 12e8ff02c72d90513868d43bc34c564f1da1ae43
ms.lasthandoff: 03/08/2017


---

# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスの有効化

この記事では、オンプレミスの SharePoint サーバーと Azure Active Directory (Azure AD) アプリケーション プロキシを統合する方法について説明します。

Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスを有効にするには、この記事の手順に従います。

## <a name="prerequisites"></a>前提条件

この記事では、SharePoint 2013 以降が既にセットアップされ、環境内で実行されていることを前提にしています。 さらに、次の前提条件を検討してください。

* アプリケーション プロキシ機能は、Azure Active Directory の Premium または Basic エディションにアップグレードしている場合にのみ利用できます。 詳細については、「 [Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。

* SharePoint では、ネイティブの Kerberos がサポートされています。 そのため、Azure AD アプリケーション プロキシを経由して内部サイトにリモートでアクセスするユーザーは、シームレスなシングル サインオン (SSO) エクスペリエンスを想定できます。

* SharePoint サーバーに、いくつかの構成変更を加える必要があります。 ステージング環境を使用することをお勧めします。 これにより、ステージング サーバーを最初に更新し、実稼働に移行する前のテスト サイクルを容易にすることができます。

* SharePoint では公開 URL で SSL が必要なため、SharePoint 用の SSL を既に設定していると想定しています。 内部のサイトで SSL を有効にして、リンクの送信およびマッピングが適切であることを確認する必要があります。 SSL を構成していない場合、手順については「[Configure SSL for SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013)」(SharePoint 2013 用の SSL の構成) をご覧ください。 また、コネクタ コンピューターがユーザー発行の証明書を信頼することを確認します  (証明書は、パブリックに発行されたものである必要はありません)。

## <a name="step-1-set-up-single-sign-on-to-sharepoint"></a>手順 1: SharePoint へのシングル サインオンの設定

お客様は、バックエンド アプリケーション (この場合は SharePoint サーバー) での最良の SSO エクスペリエンスを求めています。 この一般的な Azure AD シナリオでは、ユーザーは&1; 回のみ認証され、認証をもう一度求められることはありません。

Windows 認証を必要とする、または使用するオンプレミス アプリケーションの場合は、Kerberos 認証プロトコルと、Kerberos の制約付き委任 (KCD) と呼ばれる機能を使用して SSO を実現できます。 KCD を構成すると、ユーザーが Windows に直接サインインしていない場合でも、アプリケーション プロキシ コネクタは、そのユーザーの Windows チケット/トークンを取得できます。 KCD の詳細については、「[Kerberos の制約付き委任の概要](https://technet.microsoft.com/en-us/library/jj553400.aspx)」をご覧ください。

SharePoint サーバーの KCD を設定するには、以下のセクションの手順を順番に実行します。

### <a name="ensure-that-sharepoint-is-running-under-a-service-account"></a>SharePoint がサービス アカウントで実行されていることを確認する

まず、SharePoint がローカル システム、ローカル サービス、ネットワーク サービスのいずれでもなく、定義済みのサービス アカウントで実行されていることを確認します。 これを行って、有効なアカウントにサービス プリンシパル名 (SPN) をアタッチできるようにする必要があります。 SPN は、Kerberos プロトコルがさまざまなサービスを特定する方法です。 そして、KCD を構成するために後でアカウントが必要になります。

サイトが定義済みのサービス アカウントで実行されていることを確認するには、以下の手順に従います。

1. **SharePoint 2013 の全体管理**サイトを開きます。
2. **[セキュリティ]** に移動し、**[サービス アカウントの構成]** を選択します。
3. **[Web アプリケーション プール - SharePoint - 80]** を選択します。 Web プールの名前に応じて、オプションが若干異なることがあります。Web プールが既定で SSL を使用している場合も同様です。

  ![サービス アカウントを構成するための選択肢](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-web-application.png)

4. **[このコンポーネントのアカウントの選択]** が **[ローカル サービス]** または **[ネットワーク サービス]** の場合は、アカウントを作成する必要があります。 アカウントがそのどちらでもない場合は完了しているため、次のセクションに進むことができます。
5. **[新しい管理アカウントの登録]** を選択します。 アカウントを作成したら、アカウントを使用する前に **Web アプリケーション プール**を設定する必要があります。

> [!NOTE]
サービス用に事前に作成した Azure AD アカウントが必要です。 パスワードの自動変更を許可することをお勧めします。 問題のトラブルシューティングの完全な手順について詳しくは、「[SharePoint 2013 でのパスワードの自動変更の構成](https://technet.microsoft.com/EN-US/library/ff724280.aspx)」をご覧ください。

### <a name="configure-sharepoint-for-kerberos"></a>Kerberos 用に SharePoint を構成する

KCD を使用して、SharePoint サーバーへのシングル サインオンを実行します。これは、Kerberos でのみ動作します。

Kerberos 認証用 SharePoint サイトを構成するには

1. **SharePoint 2013 の全体管理**サイトを開きます。
2. **[アプリケーション構成の管理]** に移動し、**[Web アプリケーションの管理]** を選択して、SharePoint サイトを選択します。 この例では、**[SharePoint - 80]** です。

  ![SharePoint サイトの選択](./media/application-proxy-remote-sharepoint/remote-sharepoint-manage-web-applications.png)

3. ツール バーの **[認証プロバイダー]** をクリックします。
4. **[認証プロバイダー]** ボックスで、**[既定のゾーン]** をクリックして設定を表示します。
5. **[認証の編集]** ダイアログ ボックスで、**[クレーム認証の種類]** が表示されるまで下へスクロールし、**[Windows 認証の有効化]** と **[統合 Windows 認証]** がオンになっていることを確認します。
6. ドロップダウン ボックスで、**[ネゴシエート (Kerberos)]** が選択されていることを確認します。

  ![[認証の編集] ダイアログ ボックス](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-edit-authentication.png)

7. **[認証の編集]** ダイアログ ボックスの下部で、**[保存]** をクリックします。

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>SharePoint サービス アカウントのサービス プリンシパル名の設定

KCD を構成する前に、構成した、サービス アカウントとして実行されている SharePoint サービスを特定する必要があります。 これは SPN を設定して行います。 詳細については、「[Service Principal Names (サービス プリンシパル名)](https://technet.microsoft.com/en-us/library/cc961723.aspx)」をご覧ください。

SPN の形式は以下のとおりです。

```
<service class>/<host>:<port>
```

SPN 形式で:

* _service class_ は、サービスの一意の名前です。 SharePoint では、**HTTP** を使用します。

* _host_ は、サービスが実行されているホストの完全修飾ドメイン名または NetBIOS 名です。 SharePoint サイトの場合、使用している IIS のバージョンによっては、サイトの URL であることが必要となる可能性があります。

* _port_ は省略可能です。

SharePoint サーバーの FQDN が以下である場合、

```
sharepoint.demo.o365identity.us
```

SPN は以下のようになります。

```
HTTP/ sharepoint.demo.o365identity.us demo
```

サーバー上の特定のサイトの SPN も設定することが必要な場合があります。 詳しくは、[Kerberos 認証の構成](https://technet.microsoft.com/en-us/library/cc263449(v=office.12).aspx)に関する記事をご覧ください。 「Kerberos 認証を使用して Web アプリケーションのサービス プリンシパル名を作成する」セクションに特に注意を払ってください。

SPN を設定する最も簡単な方法は、サイトに既に存在する可能性がある SPN の形式に従うことです。 その SPN をコピーし、サービス アカウントに対して SPN を登録します。 これを行うには、次の手順を実行します。

1. 別のコンピューターから、SPN を使用してサイトを参照します。
 これを行うと、関連する一連の Kerberos チケットがコンピューターにキャッシュされます。 これらのチケットには、参照した対象サイトの SPN が含まれます。

2. [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html) と呼ばれるツールを使用して、そのサイトの SPN を取り出すことができます。 ブラウザーでサイトにアクセスしたユーザーと同じコンテキストで実行されているコマンド ウィンドウで、次のコマンドを実行します。
```
Klist
```
Klist は、ターゲット SPN のセットを返します。 この例では、強調表示されている値が、必要な SPN です。

  ![Klist の結果の例](./media/application-proxy-remote-sharepoint/remote-sharepoint-target-service.png)

4. これで SPN がわかったため、先ほど Web アプリケーション用に設定したサービス アカウントで SPN が正しく構成されていることを確認する必要があります。 ドメインの管理者として、コマンド プロンプトから次のコマンドを実行します。

 ```
 setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
 ```

 このコマンドにより、_demo\sp_svc_ として実行されている SharePoint サービス アカウントの SPN が設定されます。

 _http/sharepoint.demo.o365identity.us_ をサーバーの SPN に、_demo\sp_svc_ を環境でのサービス アカウントに置き換えます。 Setspn コマンドでは、追加前にその SPN が検索されます。 この場合、**重複している SPN 値**のエラーが表示されることがあります。 このエラーが表示された場合、SPN 値がサービス アカウントに関連付けられていることを確認してください。

-l オプションを使用して Setspn コマンドを実行すると、SPN が追加されたことを確認できます。 このコマンドについて詳しくは、「[Setspn](https://technet.microsoft.com/en-us/library/cc731241.aspx)」をご覧ください。

### <a name="ensure-that-the-connector-is-set-as-a-trusted-delegate-to-sharepoint"></a>コネクタが信頼され、SharePoint に委任するように設定されていることを確認する

KCD を構成して、Azure AD アプリケーション プロキシ サービスがユーザー ID を SharePoint サービスに委任できるようにします。 これを行うには、アプリケーション プロキシ コネクタを有効にして、Azure AD で認証されているユーザーの Kerberos チケットを取得します。 その後、そのサーバーは、コンテキストを対象アプリケーション (この場合は SharePoint) に渡します。

KCD を構成するには、コネクタ コンピューターごとに以下の手順を繰り返します。

1. ドメイン管理者として DC にログインし、**[Active Directory ユーザーとコンピューター]** を開きます。
2. コネクタが実行されているコンピューターを見つけます。 この例では、同じ SharePoint サーバーです。
3. そのコンピューターをダブルクリックし、**[委任]** タブをクリックします。
4. 委任設定が **[指定されたサービスへの委任でのみこのコンピューターを信頼する]** に設定されていることを確認し、**[任意の認証プロトコルを使う]** を選択します。

  ![Delegation settings](./media/application-proxy-remote-sharepoint/remote-sharepoint-delegation-box.png)

5. **[追加]** ボタンをクリックし、**[ユーザーまたはコンピューター]** をクリックして、サービス アカウントを特定します。

  ![サービス アカウントの SPN の追加](./media/application-proxy-remote-sharepoint/remote-sharepoint-users-computers.png)

6. SPN の一覧で、先ほどサービス アカウント用に作成した SPN を選びます。
7. **[OK]**をクリックします。 もう一度 **[OK]** をクリックして変更を保存します。

## <a name="step-2-enable-remote-access-to-sharepoint"></a>手順 2: SharePoint へのリモート アクセスの有効化

Kerberos 用の SharePoint を有効にし、KCD を構成したことから、これで SharePoint へのシングル サインオンを設定する準備ができました。 そして、Azure AD アプリケーション プロキシ経由のリモート アクセスのため、コネクタから SharePoint ファームを公開できます。

以下の手順を実行するには、組織の Azure Active Directory アカウント内のグローバル管理者ロールのメンバーである必要があります。

1. [Azure Portal](https://manage.windowsazure.com) にサインインし、Azure AD テナントを探します。
2. **[アプリケーション]** をクリックし、**[追加]** をクリックします。
3. **[ネットワーク外部からアクセスできるアプリケーションを発行します]**を選択します。 このオプションが表示されない場合は、テナントで Azure AD Basic または Azure AD Premium が設定されていることを確認します。
4. 各オプションを次のように入力します。
 * **名前**: 必要な任意の値を使用します (例: **SharePoint**)。
 * **内部 URL**: SharePoint サイトの内部的な URL (例: **https://SharePoint/**)。 この例では、必ず **https** を使用します。
 * **事前認証方法**: **[Azure Active Directory]** を選択します。

  ![アプリケーションの追加のオプション](./media/application-proxy-remote-sharepoint/remote-sharepoint-add-application.png)

5. アプリケーションを発行したら、**[構成]** タブをクリックします。
6. オプション **[ヘッダーの URL を変換する]** まで下にスクロールします。 既定値は **[はい]** です。 これを **[いいえ]** に変更します。

 SharePoint では、_[ホスト ヘッダー]_ 値を使用してサイトを検索します。 この値に基づいてリンクも生成されます。 実際の効果は、SharePoint で生成されたリンクはどれも公開 URL であり、外部 URL を使用するように適切に設定されていることを確実にするというものです。 この値を **[はい]** に設定すると、コネクタが要求をバックエンド アプリケーションに転送できるようにもなります。 一方、値を **[いいえ]** に設定することは、コネクタが内部のホスト名を送信しないことを意味します。 代わりに、コネクタは、ホスト ヘッダーをバックエンド アプリケーションに公開 URL として送信します。

 また、SharePoint がこの URL を受け入れるようにするには、SharePoint サーバーでもう&1; つの構成を完了する必要があります。 これは、次のセクションで行います。

7. **[内部認証方法]** を **[統合 Windows 認証]** に設定します。 Azure AD テナントが、オンプレミス の UPN とは異なる UPN をクラウドで使用している場合は、**[委任されたログイン ID]** も忘れずに更新します。
8. **[内部アプリケーション SPN]** を先ほど設定した値に設定します。 たとえば、**http/sharepoint.demo.o365identity.us** を使用します。
9. アプリケーションを対象ユーザーに割り当てます。

アプリケーションの表示は次のようになります。

  ![完成したアプリケーション](./media/application-proxy-remote-sharepoint/remote-sharepoint-internal-application-spn.png)

## <a name="step-3-ensure-that-sharepoint-knows-about-the-external-url"></a>手順 3: SharePoint による外部 URL の把握

最終手順は、SharePoint が外部 URL に基づいてサイトを見つけることができるようにして、その外部 URL に基づいたリンクが表示されるようにすることです。 これを行うには、SharePoint サイトの代替アクセス マッピングを構成します。

1. **SharePoint 2013 の全体管理**サイトを開きます。
2. **[システム設定]** で、**[代替アクセス マッピングの構成]** を選択します。

 これにより、**[代替アクセス マッピング]** ボックスが開きます。

  ![[代替アクセス マッピング] ボックス](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access1.png)

3. **[代替アクセス マッピング コレクション]** の横にあるドロップダウン リストで、**[Change Alternative Access Mapping Collection (代替アクセス マッピング コレクションの変更)]** を選択します。
4. サイト (例: **[SharePoint - 80]**) を選択します。

  ![サイトの選択](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access2.png)

5. 内部 URL またはパブリック URL のいずれかとして、公開 URL を追加できます。 この例では、パブリック URL をエクストラネットとして使用します。
6. **[エクストラネット]** パスで **[パブリック URL の編集]** をクリックし、前の手順のように、アプリケーションを発行したパスを入力します。 たとえば、「**https://sharepoint-iddemo.msappproxy.net**」と入力します。

  ![パスの入力](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access3.png)

7. **[保存]**をクリックします。

 これで、Azure AD アプリケーション プロキシ経由で外部から SharePoint サイトにアクセスできるようになりました。

## <a name="next-steps"></a>次のステップ

[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](active-directory-application-proxy-get-started.md)<br>
[Azure AD アプリケーション プロキシ コネクタについて](application-proxy-understand-connectors.md)<br>
[Publishing SharePoint 2016 and Office Online Server with Azure AD Application Proxy (Azure AD アプリケーション プロキシによる SharePoint 2016 および Office Online Server の公開)](https://blogs.technet.microsoft.com/dawiese/2016/06/09/publishing-sharepoint-2016-and-office-online-server-with-azure-ad-application-proxy/)

