---
title: Azure Multi-Factor Authentication FAQ - Azure Active Directory
description: Azure Multi-Factor Authentication に関してよく寄せられる質問と回答。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/18/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86cce0ee2a4966e2c2c8d74ad63a50976c199eb6
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967304"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication についてよく寄せられる質問

この FAQ では、Azure Multi-Factor Authentication と Multi-Factor Authentication サービスの利用について、よく寄せられる質問に回答します。 FAQ の内容は、サービス全般、課金モデル、ユーザー エクスペリエンス、トラブルシューティングに分けてまとめられています。

## <a name="general"></a>全般

> [!IMPORTANT]
> 2019 年 7 月 1 日より、Microsoft では新しいデプロイに対して MFA Server が提供されなくなります。 ユーザーからの多要素認証が必要な新しいお客様は、クラウドベースの Azure Multi-Factor Authentication を使用していただく必要があります。 7 月 1 日より前に MFA Server をアクティブ化した既存のお客様は、最新バージョンの今後の更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成することができます。
> [!NOTE]
> Azure Multi-Factor Authentication Server に関して、以下で共有される情報は、MFAサーバーを既に実行しているユーザーにのみ適用されます。


**Q:Azure Multi-Factor Authentication Server ではどのようにユーザー データが処理されますか。**

Multi-Factor Authentication Server では、ユーザーのデータはオンプレミス サーバーのみに格納されます。 永続的なユーザー データはクラウドに格納されません。 ユーザーが 2 段階認証を実行すると、Multi-Factor Authentication Server から Azure Multi-Factor Authentication クラウド サービスにデータが送信され、認証が要求されます。 Multi-Factor Authentication Server と Multi-Factor Authentication クラウド サービス間の通信には、送信方向のポート 443 経由で Secure Sockets Layer (SSL) またはトランスポート層セキュリティ (TLS) が使用されます。

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

認証データと共に、認証結果 (成功または拒否) と、拒否された場合はその理由が保存されます。 このデータは、認証と使用状況のレポートで確認できます。

**Q:ユーザーに SMS メッセージを送る際には、どの SMS ショート コードが使用されますか。**

米国では、Microsoft は次の SMS ショート コードを使用します。

   * 97671
   * 69829
   * 51789
   * 99399

カナダでは、Microsoft は次の SMS ショート コードを使用します。

   * 759731 
   * 673801

Microsoft は、一貫した SMS や音声ベース Multi-Factor Authentication のプロンプトが同じ番号で配信されることを保証しません。 ユーザーのために、Microsoft は、ルートを調整して SMS の配信率を向上させる際に任意のタイミングでショート コードを追加または削除する場合があります。 Microsoft は、米国とカナダ以外の国/地域ではショート コードをサポートしていません。

## <a name="billing"></a>課金

課金に関するほとんどの疑問は、「[Multi-Factor Authentication の価格](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)」ページか「[Azure Multi-Factor Authentication の入手方法](concept-mfa-licensing.md)」のドキュメントを参照することで解決します。

**Q:認証用の電話呼び出しやテキスト メッセージの送信について、自分の組織に料金が請求されることはありますか。**

いいえ。Azure Multi-Factor Authentication 経由でユーザーに対して行われる電話呼び出しや送信されるテキスト メッセージの料金が個別に請求されることはありません。 認証ごとの MFA プロバイダーを使用している場合は認証ごとに料金が請求されますが、認証の手段に対する請求は行われません。

ユーザーが受ける電話呼び出しやテキスト メッセージの料金は、個人で契約している電話サービスに従って請求されます。

**Q:ユーザーごとの課金モデルで、課金の対象となるのは有効化されているすべてのユーザーですか、それとも 2 段階認証を実行したユーザーのみですか。**

その月に 2 段階認証を実行したかどうかに関係なく、Multi-Factor Authentication を使用するように構成されたユーザーの数に基づいて課金されます。

**Q:Multi-Factor Authentication の請求はどのように行われますか。**

ユーザーごとまたは認証ごとの MFA プロバイダーを作成すると、組織の Azure サブスクリプションは毎月使用量に基づいて課金されます。 この課金モデルは、仮想マシンと Web サイトの使用量に対する Azure の課金方法に似ています。

Azure Multi-factor Authentication のサブスクリプションを購入すると、組織はユーザーごとに年間ライセンス費用のみを支払います。 MFA ライセンスと Office 365、Azure AD Premium または Microsoft Enterprise Mobility + Security バンドルは、この方法で課金されます。 

料金オプションの詳細については、「[Azure Multi-Factor Authentication の入手方法](concept-mfa-licensing.md)」を参照してください。

**Q:Azure Multi-Factor Authentication の無料版はありますか。**

場合によります。

Azure 管理者向けの Multi-Factor Authentication では、一部の Azure MFA 機能を無料で利用し、[Azure portal](https://portal.azure.com) と [Microsoft 365 管理センター](https://admin.microsoft.com)を含む Microsoft オンライン サービスにアクセスできます。 このプランは、Azure Active Directory インスタンスのグローバル管理者のうち、MFA ライセンス、バンドル、またはスタンドアロンの使用量ベースのプロバイダーを通じて提供される、通常版の Azure MFA を所有していない管理者のみに適用されます。 管理者が、無料版を使用していて、その後、完全バージョンの Azure MFA を購入すると、すべてのグローバル管理者が有償版に自動的に昇格されます。

Office 365 ユーザー向けの Multi-Factor Authentication では、一部の Azure MFA 機能を無料で利用し、Exchange Online と SharePoint Online を含む Office 365 サービスにアクセスできます。 このプランは、Office 365 ライセンスに対応する Azure Active Directory インスタンスに、MFA ライセンス、バンドル、またはスタンドアロンの使用量ベースのプロバイダーを通じて提供される通常版の Azure MFA がない場合、その Office 365 ライセンスが割り当てられているユーザーに適用されます。

**Q:ユーザーごとと認証ごとの使用量課金モデルは、組織でいつでも切り替えることができますか。**

使用量ベースの課金方法により、スタンドアロン サービスとして組織で MFA を購入する場合は、MFA プロバイダーを作成する際に課金モデルを選択します。 MFA プロバイダーの作成後に課金モデルを変更することはできません。 

MFA プロバイダーが Azure AD テナントにリンクされて*いない*場合、または新しい MFA プロバイダーを別の Azure AD テナントにリンクする場合、ユーザー設定と構成オプションは転送されません。 また、既存の Azure MFA サーバーは、新しい MFA プロバイダーによって生成されるアクティブ化資格情報を使用して再アクティブ化する必要があります。 MFA Server を新しい MFA プロバイダーにリンクするために再アクティブ化しても、電話呼び出しやテキスト メッセージによる認証には影響ありませんが、モバイル アプリ通知は、各ユーザーがモバイル アプリを再アクティブ化するまで機能しなくなります。

MFA プロバイダーの詳細については、「[Azure Multi-Factor Auth プロバイダーの概要](concept-mfa-authprovider.md)」を参照してください。

**Q:使用量ベースの課金とサブスクリプション (ライセンスベースのモデル) は、組織でいつでも切り替えることができますか。**

場合によります。

ディレクトリに "*ユーザーごと*" の Azure Multi-Factor Authentication プロバイダーがある場合は、MFA ライセンスを追加できます。 ライセンスを所有しているユーザーは、ユーザーごとの使用量ベースの課金にはカウントされません。 ライセンスのないユーザーに対しては、MFA プロバイダーを通じて MFA を有効化できます。 Multi-Factor Authentication を使用するように構成されているすべてのユーザーにライセンスを購入して割り当てた場合は、Azure Multi-Factor Authentication プロバイダーを削除できます。 将来、ユーザー数がライセンス数を上回るような場合には、ユーザーごとの MFA プロバイダーをいつでも別に作成できます。

ディレクトリに "*認証ごと*" の Azure Multi-Factor Authentication プロバイダーがある場合は、その MFA プロバイダーがサブスクリプションにリンクされている限り、認証ごとに常に料金が請求されます。 MFA ライセンスをユーザーに割り当てることはできますが、2 段階認証の要求が行われると、それが MFA ライセンスの割り当てられたユーザーによる要求かどうかに関係なく、毎回料金が請求されます。

**Q:組織が Azure Multi-Factor Authentication を使用するには、ID の使用と同期が必要ですか。**

組織で使用量ベースの課金モデルを使用する場合、Azure Active Directory の利用は任意であり、必須ではありません。 MFA プロバイダーが Azure AD テナントにリンクされていない場合は、Azure Multi-Factor Authentication Server をオンプレミスにのみデプロイできます。

ライセンス モデルでは Azure Active Directory が必要です。これは、ライセンスを購入してディレクトリ内のユーザーに割り当てると、そのライセンスが Azure AD テナントに追加されるためです。

## <a name="manage-and-support-user-accounts"></a>ユーザー アカウントの管理とサポート

**Q:ユーザーの携帯電話に応答が届かない場合にはどのように対処するようユーザーに伝えればよいですか。**

認証用の電話または SMS を受け取るための操作を、5 分間に 5 回を上限として試行するようユーザーに伝えてください。 Microsoft では、発信および SMS メッセージを配信するためのプロバイダーを複数使用しています。 それでもうまく行かない場合は、さらなるトラブルシューティングを行うために、Microsoft のサポート ケースを開いてください。

これらの手順でうまく行かない場合の解決策は、すべてのユーザーが複数の認証方法を構成していることが前提となります。 再度サインインを試すようユーザーに伝えますが、その際にサインイン ページで別の認証方法を選択してもらいます。

[エンドユーザー向けトラブルシューティング ガイド](../user-help/multi-factor-authentication-end-user-troubleshoot.md)を参照用としてユーザーに示すこともできます。

**Q:アカウントに入れないユーザーがいる場合はどうすればよいですか。**

ユーザーに登録プロセスを再度実行してもらうことで、ユーザーのアカウントをリセットできます。 詳細については、 [クラウドでの Azure Multi-Factor Authentication によるユーザーおよびデバイスの設定の管理](howto-mfa-userdevicesettings.md)に関するページを参照してください。

**Q:アプリ パスワードを使用している携帯電話を紛失したユーザーがいる場合は、どうすればよいですか。**

未承認のアクセスを防ぐには、そのユーザーのアプリ パスワードをすべて削除します。 ユーザーは、代わりのデバイスを入手した後に、パスワードを再作成できます。 詳細については、 [クラウドでの Azure Multi-Factor Authentication によるユーザーおよびデバイスの設定の管理](howto-mfa-userdevicesettings.md)に関するページを参照してください。

**Q:ユーザーがブラウザー以外のアプリにサインインできない場合はどうすればよいですか。**

組織でまだ従来型クライアントを使用しており、かつ[アプリ パスワードの使用を許可している](howto-mfa-mfasettings.md#app-passwords)場合、ユーザーがこのような従来型のクライアントにユーザー名とパスワードでサインインすることはできません。 代わりに、ユーザーは[アプリ パスワードを設定する](../user-help/multi-factor-authentication-end-user-app-passwords.md)必要があります。 ユーザーはサインイン情報をクリア (削除) してアプリを再起動し、ユーザー名と、通常のパスワードではなく "*アプリ パスワード*" を使用してサインインしなければなりません。

組織で従来型クライアントを使用していない場合は、アプリ パスワードの作成をユーザーに許可しないようにしてください。

> [!NOTE]
> Office 2013 クライアントのための最新の認証
>
> アプリ パスワードは、先進認証をサポートしていないアプリにのみ必要になります。 Office 2013 クライアントでは、先進認証プロトコルがサポートされますが、構成が必要です。 Office 2013 の 2015 年 3 月以降の更新を実行しているすべてのお客様は、最新の認証を利用できるようになりました。 詳細については、「[Updated Office 365 modern authentication (Office 365 の先進認証の更新)](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/)」を参照してください。

**Q:テキスト メッセージが届かない場合や、認証がタイムアウトになる場合があると、ユーザーが訴えています。**

サービスの信頼性に影響する可能性がある制御不能な要因があるため、SMS メッセージの配信は保証されません。 これらの要因には、相手国/地域、携帯電話会社、信号の強さなどがあります。

テキスト メッセージがユーザーに確実に届かない問題が頻発する場合は、代わりにモバイル アプリか電話呼び出しによる認証方法を使用するようユーザーに指示してください。 モバイル アプリは、携帯電話と Wi-fi 接続の両方で通知を受け取ることができます。 さらに、デバイスに信号がまったくない場合でも、モバイル アプリは検証コードを生成できます。 Microsoft Authenticator アプリは、[Android](https://go.microsoft.com/fwlink/?Linkid=825072)、[IOS](https://go.microsoft.com/fwlink/?Linkid=825073)、[Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) で利用できます。

**Q:ユーザーが認証コードを入力しなければならない、テキスト メッセージを受信してからシステムがタイムアウトになるまでの制限時間は変更できますか。**

場合によりますが、変更できます。 

Azure MFA サーバー v7.0 以降の単方向 SMS の場合、レジストリ キーをを設定することでタイムアウト設定を構成できます。 MFA クラウド サービスがテキスト メッセージを送信すると、確認コード (またはワンタイム パスコード) が MFA サーバーに返されます。 MFA サーバーは、既定ではコードをメモリ内に 300 秒間保存します。 300 秒が経過する前にユーザーがコードを入力しないと、認証は拒否されます。 既定のタイムアウト設定を変更するには、次の手順を実行します。

1. HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor に移動します。
2. **pfsvc_pendingSmsTimeoutSeconds** という名前の DWORD レジストリ キーを作成し、Azure MFA Server でワンタイム パスコードを保管する時間を秒単位で設定します。

>[!TIP] 
>MFA サーバーが複数ある場合、ユーザーに送信された確認コードを認識しているのは、元の認証要求を処理した 1 つだけです。 ユーザーがコードを入力する際に、そのコードを検証するための認証要求を同じサーバーに送信する必要があります。 コードの検証を別のサーバーに送信すると、認証が拒否されます。 

ユーザーが定義されたタイムアウト期間内に SMS に応答しない場合、認証は拒否されます。 

クラウドの Azure MFA の単方向 SMS (AD FS アダプターおよびネットワーク ポリシー サーバー拡張機能など) の場合、タイムアウト設定を構成することはできません。 Azure AD は、確認コードを 180 秒間保存します。 

**Q:Azure Multi-Factor Authentication Server でハードウェア トークンを使用できますか。**

Azure Multi-Factor Authentication Server を使用している場合、サード パーティにによるオープン認証 (OATH) の時間ベースのワンタイム パスワード (TOTP) トークンをインポートして 2 段階認証で使用できます。

CSV ファイル内に秘密キーを配置して Azure Multi-Factor Authentication Server にインポートすると、OATH TOTP トークンである ActiveIdentity トークンを使用できます。 クライアント システムでユーザー入力を受け入れられる限り、OATH トークンは、Active Directory フェデレーション サービス (ADFS)、インターネット インフォメーション サーバー (IIS) のフォームベース認証、リモート認証ダイヤルイン ユーザー サービス (RADIUS) で使用できます。

以下の形式のサードパーティによる OATH TOTP トークンをインポートできます。  

- 移植可能な対称キー コンテナー (PSKC)  
- CSV。シリアル番号、Base 32 形式の秘密キー、および時間間隔がファイルに含まれている場合  

**Q:Azure Multi-Factor Authentication Server を使用してターミナル サービスをセキュリティで保護することはできますか。**

はい。ただし、Windows Server 2012 R2 以降を使用している場合は、リモート デスクトップ ゲートウェイ (RD ゲートウェイ) の使用によってのみターミナル サービスをセキュリティで保護できます。

Windows Server 2012 R2 におけるセキュリティの変更により、Azure Multi-Factor Authentication Server から Windows Server 2012 以前のバージョンのローカル セキュリティ機関 (LSA) のセキュリティ パッケージに接続する方法が変わりました。 Windows 2012 以前のターミナル サービスのバージョンでは、[Windows 認証でアプリケーションをセキュリティで保護する](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure)ことができます。 Windows Server 2012 R2 を使用している場合は、RD ゲートウェイが必要です。

**Q:MFA Server で発信者 ID を構成しましたが、ユーザーがまだ匿名の発信者から Multi-Factor Authentication の呼び出しを受けます。**

公衆電話網経由で Multi-Factor Authentication の呼び出しが行われた場合、発信者 ID をサポートしていない通信事業者を通じて呼び出しがルーティングされることがあります。 このため、Multi-Factor Authentication システムが常に発信者 ID を送信していても、発信者 ID は保証されません。

**Q:セキュリティ情報の登録を求めるメッセージがユーザーに表示されるのはなぜでしょうか。**
セキュリティ情報の登録を求めるメッセージがユーザーに表示される場合、以下のようないくつかの理由が考えられます。

- そのユーザーは Azure AD の管理者によって MFA が有効化されているが、まだアカウントにセキュリティ情報を登録していない。
- そのユーザーに対して、Azure AD でのセルフサービスによるパスワードのリセットが有効化されている。 セキュリティ情報は、将来パスワードを忘れた場合に、それをリセットするために役立ちます。
- そのユーザーは事前に MFA への登録を行わずに、MFA を要求する条件付きアクセス ポリシーのあるアプリケーションにアクセスした。
- そのユーザーは Azure AD (Azure AD Join を含む) にデバイスを登録しようとしており、ユーザーの組織ではデバイスの登録に MFA を要求しているが、ユーザーは事前に MFA への登録を行っていない。
- そのユーザーは Windows 10 で Windows Hello for Business を生成 (この操作に MFA が必要) しようとしているが、事前に MFA への登録を行っていない。
- 組織で作成および有効化されている MFA 登録ポリシーが、そのユーザーに適用されている。
- そのユーザーは事前に MFA への登録を行っているが、選択した認証方法が、その後管理者によって無効化されている。 このため、ユーザーはもう一度 MFA 登録を行い、新しい既定の認証方法を選択する必要があります。

## <a name="errors"></a>Errors

**Q:モバイル アプリ通知を使用して認証を行うときに、"認証しようとしているアカウントはアクティブ化されていません" というエラー メッセージが表示された場合、ユーザーはどうすればよいでしょうか。**

次の手順でモバイル アプリからアカウントを削除した後、アカウントをもう一度追加するようにユーザーに伝えてください。

1. [Azure ポータル プロファイル](https://account.activedirectory.windowsazure.com/profile/) に移動して、組織のアカウントでサインインします。
2. **[追加のセキュリティ確認]** を選択します。
3. モバイル アプリから既存のアカウントを削除します。
4. **[構成]** をクリックし、モバイル アプリを再構成するための手順を実行します。

**Q:ブラウザー以外のアプリケーションでサインインしたときに 0x800434D4L エラー メッセージが表示された場合、ユーザーはどうすればよいでしょうか。**

0x800434D4L エラーは、2 段階認証を要求するアカウントでは機能しない、ローカル コンピューターにインストールされているブラウザー以外のアプリケーションにサインインしようとすると発生します。

このエラーを回避するには、管理関連の操作用と管理以外の操作用に異なるユーザー アカウントを使用します。 非管理アカウントを使用して Outlook にサインインできるように、後ほど、管理アカウントと非管理アカウントのメールボックスにリンクを作成することができます。 このソリューションの詳細については、「[管理者がユーザーのメールボックスの内容を開いたり表示したりできるようにする](https://help.outlook.com/141/gg709759.aspx?sl=1)」を参照してください。

## <a name="next-steps"></a>次の手順

このページで質問の答えが見つからない場合は、ページ下部の該当欄にコメントをご記入ください。 または、次の操作でヘルプを取得できます。

* [Microsoft サポート技術情報](https://support.microsoft.com)を検索して、一般的な技術上の問題の解決方法を探します。
* このコミュニティで技術的な質問と回答を検索して参照したり、 [Azure Active Directory フォーラム](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required)で独自の質問を投稿したりできます。
* 従来の PhoneFactor をご利用中で、パスワードのリセットについてご質問がある場合やサポートを必要とされている場合は、 [パスワード リセット](mailto:phonefactorsupport@microsoft.com) のリンクを使用してサポート ケースを開いてください。
* [Azure Multi-Factor Authentication Server (PhoneFactor) のサポート](https://support.microsoft.com/oas/default.aspx?prid=14947)を通して、サポートのプロに問い合わせます。 お問い合わせの際は、問題に関する情報をできるだけお知らせいただくと役に立ちます。 エラーが表示されたページ、具体的なエラー コード、具体的な ID、エラーが表示されたユーザーの ID などの情報をご提供ください。
