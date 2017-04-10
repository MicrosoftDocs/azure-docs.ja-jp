---
title: "Azure Multi-Factor Authentication についてよく寄せられる質問 (FAQ) | Microsoft Docs"
description: "Azure Multi-Factor Authentication に関してよく寄せられる質問と回答。 Azure Multi-Factor Authentication は、本人確認において、ユーザー名とパスワードに加えて、その他の要素を取り入れる手法です。 ユーザーのサインインとトランザクションに多層構造のセキュリティを確保することができます。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 20afeb3ba290ddf728d2b52c076c7a57fadc77c6
ms.openlocfilehash: dfbb5e2358cd857c2a16ded5d4d82c1f246cbe20
ms.lasthandoff: 02/28/2017

---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication についてよく寄せられる質問
この FAQ では、Azure Multi-Factor Authentication と Multi-Factor Authentication サービスの利用について、課金モデルや使いやすさに関する疑問を含め、よく寄せられる質問に答えます。

## <a name="general"></a>全般
**Q: Azure Multi-Factor Authentication Server ではどのようにユーザー データが処理されますか。**

Multi-Factor Authentication Server では、ユーザーのデータはオンプレミス サーバーのみに格納されます。 永続的なユーザー データはクラウドに格納されません。 ユーザーが&2; 段階認証を実行すると、Multi-Factor Authentication Server から Azure Multi-Factor Authentication クラウド サービスにデータが送信され、認証が要求されます。 Multi-Factor Authentication Server と Multi-Factor Authentication クラウド サービス間の通信には、送信方向のポート 443 経由で Secure Sockets Layer (SSL) またはトランスポート層セキュリティ (TLS) が使用されます。

認証要求がクラウド サービスに送信されると、認証レポートと使用状況レポート用のデータが収集されます。 2 段階認証のログに含まれるデータ フィールドは次のとおりです。

* **一意の ID** (ユーザー名またはオンプレミスの Multi-Factor Authentication Server ID のいずれか)
* **姓と名** (省略可能)
* **電子メール アドレス** (省略可能)
* **電話番号** (音声通話または SMS 認証を使用する場合)
* **デバイス トークン** (モバイル アプリ認証を使用する場合)
* **認証モード**
* **認証の結果**
* **Multi-Factor Authentication のサーバー名**
* **Multi-Factor Authentication のサーバー IP**
* **クライアント IP** (使用可能な場合)

オプション フィールドを Multi-Factor Authentication Server で構成できます。

認証結果 (成功または拒否) と、拒否された場合の拒否理由も、認証データと共に格納され、認証レポートと使用状況レポートで使用できます。

## <a name="billing"></a>課金
課金に関する質問の大半は、「[Multi-Factor Authentication の価格](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)」を参照することで答えを得ることができます。

**Q: ユーザーを認証するために使用される通話やテキスト メッセージの料金は組織に請求されますか。**

Azure Multi-Factor Authentication 経由でユーザーに対して行われる通話や送信されるテキスト メッセージの料金が組織に請求されることはありません。 電話の所有者が受け取る通話やテキスト メッセージの料金は、所有者が契約している電話サービスに従って請求される場合があります。

**Q: 「ユーザーごと」の課金モデルは、Multi-Factor Authentication を使用できるように構成されているユーザー数に基づきますか、それとも確認を実行するユーザー数に基づいて課金されるのでしょうか。**

請求は、Multi-Factor Authentication を使用できるように構成されているユーザーの数に基づいて行われます。

**Q: Multi-Factor Authentication の請求はどのように行われますか。**

"ユーザーごと" または"認証ごと" のモデルを使用している場合、Azure MFA は使用量ベースのリソースです。 課金は、仮想マシンや Web サイトなどと同じように、組織の Azure サブスクリプションに対して行われます。

ライセンス モデルを使用する場合は、Azure Multi-Factor Authentication ライセンスを購入してユーザーに割り当てます。これは、Office 365 や他のサブスクリプション製品と同様です。

オプションについて詳しくは、「[Azure Multi-Factor Authentication のしくみ](multi-factor-authentication-how-it-works.md)」をご覧ください

**Q: 管理者用の Azure Multi-Factor Authentication の無料版はありますか。**

場合によります。 Multi-Factor Authentication for Azure Administrators は、Azure MFA の機能の一部を無料で提供します。 このプランは、使用量ベースの Azure Multi-Factor Authentication プロバイダーにリンクされていない Azure Active Directory インスタンスの Azure グローバル管理者グループのメンバーに適用されます。 Multi-Factor Authentication プロバイダーを使用すると、Multi-Factor Authentication を使用できるように構成されているディレクトリ内のすべての管理者とユーザーが、Azure Multi-Factor Authentication のフル バージョンにアップグレードされます。

**Q: Office 365 ユーザー用の Azure Multi-Factor Authentication の無料版はありますか。**

場合によります。 Multi-Factor Authentication for Office 365 は、Azure MFA の機能の一部を無料で提供します。 このプランは、使用量ベースの Azure Multi-Factor Authentication プロバイダーが対応する Azure Active Directory インスタンスにリンクされていない場合に、Office 365 ライセンスが割り当てられているユーザーに適用されます。 Multi-Factor Authentication プロバイダーを使用すると、Multi-Factor Authentication を使用できるように構成されているディレクトリ内のすべての管理者とユーザーが、Azure Multi-Factor Authentication のフル バージョンにアップグレードされます。

**Q: ユーザーごとと認証ごとの使用量課金モデルは、組織でいつでも切り替えることができますか。**

組織でリソースを作成するときに課金モデルを選択します。 リソースをプロビジョニングした後に課金モデルを変更することはできません。 ただし、別の Multi-Factor Authentication リソースを作成して元のリソースを置き換えることは可能です。 ユーザー設定と構成オプションを新しいリソースに転送することはできません。

**Q: 使用量課金とライセンス モデルは、組織でいつでも切り替えることができますか。**

既にユーザーごとの Azure Multi-Factor Authentication プロバイダーを持っているディレクトリにライセンスが追加されると、所有ライセンス数分が使用量ベース課金から差し引かれます。 Multi-Factor Authentication を使用できるように構成されているすべてのユーザーにライセンスが割り当てられている場合、管理者は Azure Multi-Factor Authentication プロバイダーを削除できます。

認証ごとの使用量課金とライセンス モデルを混在させることはできません。 認証ごとの Multi-Factor Authentication プロバイダーがディレクトリにリンクされている場合、組織は所有しているライセンスに関係なく、すべての Multi-Factor Authentication 確認要求に対して課金されます。

**Q: 組織が Azure Multi-Factor Authentication を使用するには、ID の使用と同期が必要ですか。**

組織で使用量ベースの課金モデルを使用する場合、Azure Active Directory は必要ありません。 ディレクトリへの Multi-Factor Authentication プロバイダーのリンクはオプションです。 組織がディレクトリにリンクされていない場合は、オンプレミスの Azure Multi-Factor Authentication Server または Azure Multi-factor Authentication SDK をデプロイできます。

ライセンス モデルでは Azure Active Directory が必要です。これは、ライセンスが購入されてディレクトリ内のユーザーに割り当てられると、ライセンスがディレクトリに追加されるためです。

## <a name="usability"></a>使いやすさ
**Q: ユーザーの携帯電話に応答が届かない場合、またはユーザーが携帯電話を利用できない場合はどうすればよいですか。**

ユーザーが予備の電話番号を構成している場合は、サインインをもう一度実行し、サインイン ページでその電話番号を選択します。 ユーザーが別の方法を構成していない場合は、組織の管理者がユーザーのメインの電話に割り当てられている番号を更新できます。

**Q: アクセスできなくなったアカウントについてユーザーから問い合わせがあった場合、管理者はどうすればよいですか。**

管理者は、ユーザーに登録プロセスを再度実行するように依頼することで、ユーザーのアカウントをリセットできます。 詳細については、 [クラウドでの Azure Multi-Factor Authentication によるユーザーおよびデバイスの設定の管理](multi-factor-authentication-manage-users-and-devices.md)に関するページを参照してください。

**Q: アプリ パスワードが使用されている電話をユーザーが紛失した場合や盗難された場合は、管理者はどうすればよいでしょうか。**

管理者は、そのユーザーのすべてのアプリ パスワードを削除して、承認されていないアクセスを防ぐことできます。 ユーザーは、代わりのデバイスを入手したら、パスワードを再作成できます。 詳細については、 [クラウドでの Azure Multi-Factor Authentication によるユーザーおよびデバイスの設定の管理](multi-factor-authentication-manage-users-and-devices.md)に関するページを参照してください。

**Q: ユーザーが非ブラウザー アプリにサインインできない場合はどうすればよいでしょうか。**

Multi-Factor Authentication を使用できるように構成されているユーザーが一部の非ブラウザー アプリにサインインするには、アプリ パスワードが必要になります。 ユーザーは、サインイン情報のクリア (削除)、アプリの再起動、ユーザー名とアプリ パスワードを使用したサインインを行う必要があります。

アプリ パスワードの作成や [アプリ パスワードに関するヘルプ](multi-factor-authentication-end-user-app-passwords.md)についての詳細を確認してください。

> [!NOTE]
> Office 2013 クライアントのための最新の認証
> 
> Office 2013 クライアント (Outlook など) では、新しい認証プロトコルがサポートされます。 Multi-Factor Authentication をサポートするように Office 2013 を構成することができます。 Office 2013 を構成すると、Office 2013 クライアントのアプリ パスワードが不要になります。 詳しくは、「 [Office 2013 modern authentication public preview announcement (Office 2013 の最新の認証のパブリック プレビューに関する発表)](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)」を参照してください。
> 
> 

**Q: テキスト メッセージを受信しない場合、または双方向のテキスト メッセージに返信したが確認がタイムアウトになる場合は、どうすればよいでしょうか。**

双方向 SMS でのテキスト メッセージの配信と応答の受信は保証されませんが、これはサービスの信頼性に影響しかねない制御不能な要因があるためです。 これらの要因には、相手国、携帯電話会社、信号の強さなどがあります。

テキスト メッセージを確実に受信することが難しいユーザーは、モバイル アプリまたは電話による方法を選択する必要があります。 モバイル アプリは、携帯電話と Wi-fi 接続の両方で通知を受け取ることができます。 さらに、デバイスに信号がまったくない場合でも、モバイル アプリは検証コードを生成できます。 Microsoft Authenticator アプリは、[Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072)、[IOS](http://go.microsoft.com/fwlink/?Linkid=825073) で利用できます。

テキスト メッセージを使用する必要がある場合は、できるだけ双方向の SMS ではなく一方向の SMS を使用することをお勧めします。 一方向の SMS は、信頼性が高く、別の国から送信されたテキスト メッセージに応答して高額な国際 SMS 料金をユーザーが課金されるのを防ぐことができます。

**Azure Multi-Factor Authentication Server でハードウェア トークンを使用できますか。**

Azure Multi-Factor Authentication Server を使用している場合、サード パーティにによるオープン認証 (OATH) の時間ベースのワンタイム パスワード (TOTP) トークンをインポートして&2; 段階認証で使用できます。

CSV ファイルにシークレット キー ファイルを配置して Azure Multi-Factor Authentication Server にインポートしている場合は、OATH TOTP トークンである ActiveIdentity トークンを使用できます。 OATH トークンと Active Directory フェデレーション サービス (ADFS)、リモート認証ダイヤルイン ユーザー サービス (RADIUS) (クライアント システムがアクセス チャレンジ応答を処理できる場合)、インターネット インフォメーション サーバー (IIS) のフォーム ベース認証を使用できます。

次の形式のサードパーティによる OATH TOTP トークンをインポートできます。  

* 移植可能な対称キー コンテナー (PSKC)  
* CSV。シリアル番号、Base 32 形式の秘密キー、および時間間隔がファイルに含まれている場合  

**Q: Azure Multi-Factor Authentication Server を使用してターミナル サービスをセキュリティで保護することはできますか。**

はい。ただし、Windows Server 2012 R2 以降を使用している場合は、リモート デスクトップ ゲートウェイ (RD ゲートウェイ) を使用してのみ可能です。

Windows Server 2012 R2 におけるセキュリティの変更により、Azure Multi-Factor Authentication Server から、Windows Server 2012 以前のバージョンのローカル セキュリティ機関 (LSA) のセキュリティ パッケージに接続する方法が変更されました。 Windows 2012 以前のターミナル サービスのバージョンでは、[Windows 認証でアプリケーションをセキュリティで保護する](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)ことができます。 Windows Server 2012 R2 を使用している場合は、RD ゲートウェイが必要です。

**Q: 発信者 ID を設定した後に、ユーザーが Multi-Factor Authentication 呼び出しを匿名の発信者から受けるのはなぜですか。**

公衆電話網経由で Multi-Factor Authentication の呼び出しが行われた場合、発信者 ID をサポートしていない通信事業者を通じて呼び出しがルーティングされることがあります。 このため、Multi-Factor Authentication システムが常に発信者 ID を送信していても、発信者 ID は保証されません。

## <a name="errors"></a>エラー
**Q: モバイル アプリ通知を使用して認証を行うときに、"認証しようとしているアカウントはアクティブ化されていません" というエラー メッセージが表示された場合、ユーザーはどうすればよいでしょうか。**

次の手順でモバイル アプリからアカウントを削除した後、アカウントをもう一度追加するようにユーザーに伝えてください。

1. [Azure ポータル プロファイル](https://account.activedirectory.windowsazure.com/profile/) に移動して、組織のアカウントでサインインします。
2. **[追加のセキュリティ確認]** を選択します。
3. モバイル アプリから既存のアカウントを削除します。
4. **[構成]**をクリックし、モバイル アプリを再構成するための手順を実行します。

**Q: ブラウザー以外のアプリケーションでサインインしたときに 0x800434D4L エラー メッセージが表示された場合、ユーザーはどうすればよいでしょうか。**

現時点では、ユーザーは、ブラウザーからアクセスできるアプリケーションとサービスでのみ、追加のセキュリティ確認を使用することができます。 Windows PowerShell など、ローカル コンピューターにインストールされている非ブラウザー アプリケーション ("*リッチ クライアント アプリケーション*" とも呼ばれます) は、追加のセキュリティ確認を必要とするアカウントでは機能しません。 この場合、アプリケーションで 0x800434D4L エラーがユーザーに表示される可能性があります。

これを回避するには、管理関連の操作用と管理以外の操作用に異なるユーザー アカウントを使用します。 非管理アカウントを使用して Outlook にサインインできるように、後ほど、管理アカウントと非管理アカウントのメールボックスにリンクを作成することができます。 詳細については、「 [管理者がユーザーのメールボックスの内容を開いたり表示したりできるようにする](http://help.outlook.com/141/gg709759.aspx?sl=1)」を参照してください。

## <a name="next-steps"></a>次のステップ
このページで質問の答えが見つからない場合は、ページ下部の該当欄にコメントをご記入ください。 または、次の操作でヘルプを取得できます。

* [Microsoft サポート技術情報](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport)を検索して、一般的な技術上の問題の解決方法を探します。
* このコミュニティで技術的な質問と回答を検索して参照したり、 [Azure Active Directory フォーラム](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)で独自の質問を投稿したりできます。
* 従来の PhoneFactor をご利用中で、パスワードのリセットについてご質問がある場合やサポートを必要とされている場合は、 [パスワード リセット](mailto:phonefactorsupport@microsoft.com) のリンクを使用してサポート ケースを開いてください。
* [Azure Multi-Factor Authentication Server (PhoneFactor) のサポート](https://support.microsoft.com/oas/default.aspx?prid=14947)を通して、サポートのプロに問い合わせます。 お問い合わせの際は、問題に関する情報をできるだけお知らせいただくと役に立ちます。 エラーが表示されたページ、具体的なエラー コード、具体的な ID、エラーが表示されたユーザーの ID などの情報をご提供ください。


