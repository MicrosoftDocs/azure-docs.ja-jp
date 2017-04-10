---
title: "詳細情報: Azure Active Directory のパスワード管理 | Microsoft Docs"
description: "パスワード ライトバックのしくみ、パスワード ライトバックのセキュリティ、パスワード リセット ポータルのしくみ、パスワードのリセットで使用されるデータなど、Azure AD のパスワード管理に関する高度なトピック。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: d3be2912-76c8-40a0-9507-b7b1a7ce2f8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: dca6f5189693fc98cec4f92eac81b6985e691889
ms.lasthandoff: 03/28/2017


---
# <a name="learn-more-about-password-management"></a>パスワード管理の詳細情報
> [!IMPORTANT]
> **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)。
>
>

既にパスワード管理をデプロイしている場合、またはデプロイする前に技術的な動作方法についてさらにしく知りたい場合は、サービスの背景技術の概念について、このセクションで概要を説明します。 次の内容を説明します。

* [**パスワード リセット ポータルのしくみ**](#how-does-the-password-reset-portal-work)
* [**パスワード ライトバックの概要**](#password-writeback-overview)
 * [パスワード ライトバックのしくみ](#how-password-writeback-works)
* [**パスワード ライトバックでサポートされるシナリオ**](#scenarios-supported-for-password-writeback)
 * [サポートされる Azure AD Connect、Azure AD Sync、および DirSync クライアント](#supported-clients)
 * [パスワード ライトバックで必要なライセンス](#licenses-required-for-password-writeback)
 * [パスワード ライトバックでサポートされるオンプレミスの認証モード](#on-premises-authentication-modes-supported-for-password-writeback)
 * [パスワード ライトバックでサポートされるユーザーと管理者の操作](#user-and-admin-operations-supported-for-password-writeback)
 * [パスワード ライトバックでサポートされないユーザーと管理者の操作](#user-and-admin-operations-not-supported-for-password-writeback)
* [**パスワード ライトバックのセキュリティ モデル**](#password-writeback-security-model)
 * [パスワード ライトバックの暗号化の詳細](#password-writeback-encryption-details)
 * [パスワード ライトバックの帯域幅の使用](#password-writeback-bandwidth-usage)
* [**ユーザーのパスワード リセット データのデプロイ、管理、およびアクセス**](#deploying-managing-and-accessing-password-reset-data-for-your-users)
 * [パスワードのリセットで使用されるデータ](#what-data-is-used-by-password-reset)
 * [エンド ユーザーによる登録を必要としないパスワード リセットのデプロイ](#deploying-password-reset-without-requiring-end-user-registration)
 * [パスワードをリセットするためにユーザーの登録時に発生すること](#what-happens-when-a-user-registers)
 * [ユーザーのパスワード リセット データにアクセスする](#how-to-access-password-reset-data-for-your-users)
 * [PowerShell を使用したパスワード リセット データの設定](#setting-password-reset-data-with-powershell)
 * [PowerShell を使用したパスワード リセット データの読み取り](#reading-password-reset-data-with-powershell)
* [**B2B ユーザーに対するパスワード リセットの動作**](#how-does-password-reset-work-for-b2b-users)

## <a name="how-does-the-password-reset-portal-work"></a>パスワード リセット ポータルのしくみ
ユーザーがパスワード リセット ポータルに移動すると、そのユーザー アカウントは有効か、そのユーザーはどの組織に属しているか、そのユーザーのパスワードはどこに管理されているか、ユーザーは機能を使用するライセンスが付与されているかを判断するためのワークフローが開始されます。  パスワード リセット ページの背後にあるロジックの詳細については、次の手順をお読みください。

1. ユーザーは、"あなたのアカウントにアクセスできません" のリンクをクリックするか、直接 [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com)に移動します。
2. ユーザーは、ユーザー ID を入力し、CAPTCHA を渡します。
3. Azure AD は、次の手順を行うことで、ユーザーがこの機能を使用できるかどうかを確認します。
   * ユーザーがこの機能を有効にしていて、Azure AD ライセンスが割り当てられていることを確認します。
     * ユーザーがこの機能を有効にしていない、またはライセンスが割り当てられていない場合、そのユーザーは管理者に連絡してパスワードをリセットするように求められます。
   * ユーザーは、管理者ポリシーに従って、自分のアカウントに正しいチャレンジ データが定義されていることを確認します。
     * ポリシーが 1 つのチャレンジのみを要求する場合は、管理者ポリシーで有効になっている 1 つ以上のチャレンジに対して、ユーザーが適切なデータを定義していることが確認されます。
       * ユーザーが構成されていない場合、そのユーザーは管理者に連絡してパスワードをリセットするように求められます。
     * ポリシーが 2 つのチャレンジを要求する場合は、管理者ポリシーで有効になっている 2 つ以上のチャレンジに対して、ユーザーが適切なデータを定義していることが確認されます。
       * ユーザーが構成されていない場合、そのユーザーは管理者に連絡してパスワードをリセットするように求められます。
   * ユーザーのパスワード (フェデレーションまたはパスワード ハッシュ同期された) がオンプレミスで管理されているかどうかを確認します。
     * ライトバックがデプロイされていて、ユーザーのパスワードがオンプレミスで管理されている場合、ユーザーは自分のパスワードを認証してリセットできます。
     * ライトバックがデプロイされておらず、ユーザーのパスワードがオンプレミスで管理されている場合、ユーザーは管理者に連絡してパスワードをリセットするように求められます。
4. ユーザーが自分のパスワードを正常にリセットできると判断された場合は、リセット プロセスの説明がユーザーに示されます。

パスワード ライトバックのデプロイ方法については、「[概要: Azure AD でのパスワード管理](active-directory-passwords-getting-started.md)」をご覧ください。

## <a name="password-writeback-overview"></a>パスワード ライトバックの概要
パスワード ライトバックは [Azure Active Directory Connect](connect/active-directory-aadconnect.md) コンポーネントです。Azure Active Directory Premium の現在のサブスクライバーによって有効にされ、使用されます。 詳細については、 [Azure Active Directory のエディション](active-directory-editions.md)を参照してください。

パスワード ライトバックを使用すると、オンプレミスの Active Directory にパスワードを書き戻すようにクラウド テナントを構成できます。  これにより、複雑なオンプレミスのセルフサービス パスワード リセット ソリューションを設定して管理する必要がなくなります。ユーザーはどこにいても、便利なクラウドベースの方法で自分のオンプレミスのパスワードをリセットできます。  次に、パスワード ライトバックの主な機能を示します。

* **ゼロ遅延フィードバック。**  パスワード ライトバックは同期操作です。  ユーザーのパスワードがポリシーに合わなかった場合や、何らかの理由でリセットまたは変更できなかった場合は、すぐにユーザーに通知します。
* **AD FS または他のフェデレーション技術を使ってユーザーのパスワードをリセットできます。**  パスワード ライトバック使うと、フェデレーション ユーザー アカウントと Azure AD テナントが同期している限り、クラウドからオンプレミスの AD パスワードを管理できます。
* **パスワード ハッシュ同期を使ってユーザーのパスワードをリセットできます。** パスワード リセット サービスは、同期されているユーザー アカウントでパスワード ハッシュ同期が有効になっていることを検出すると、このアカウントのオンプレミスのパスワードとクラウドのパスワードの両方を同時にリセットします。
* **アクセス パネルと Office 365 からのパスワードの変更をサポートします。**  フェデレーション ユーザーかパスワード同期されたユーザーが有効期限切れまたは有効期限切れでないパスワードを変更すると、これらのパスワードはローカル AD 環境に書き戻されます。
* **管理者が** [**Microsoft Azure 管理ポータル**](https://manage.windowsazure.com)でパスワードをリセットする場合のパスワードのライトバックをサポートします。  管理者が [Microsoft Azure 管理ポータル](https://manage.windowsazure.com)でユーザーのパスワードをリセットするときに、そのユーザーがフェデレーションまたはパスワード同期されている場合は、ローカル AD で管理者が選択するパスワードも設定されます。  現時点で、これは Office の管理ポータルではサポートされていません。
* **オンプレミスの AD パスワード ポリシーを強制します。**  ユーザーが自分のパスワードをリセットする場合、パスワードがオンプレミスの AD ポリシーに準拠していることを確認してから、そのディレクトリにコミットします。  このポリシーには、履歴、複雑さ、年齢、パスワード フィルター、ローカル AD で定義された他のパスワード制限が含まれます。
* **受信のファイアウォール規則を要求しません。**  パスワード ライトバックは、基礎となる通信チャネルとして Azure Service Bus Relay を使用します。つまり、この機能を使用するためにファイアウォールで受信ポートを開く必要はありません、
* **オンプレミスの Active Directory の保護グループ内に存在するユーザー アカウントではサポートされません。** 保護グループの詳細については、「 [Protected Accounts and Groups in Active Directory (Active Directory の保護アカウントとグループ)](https://technet.microsoft.com/library/dn535499.aspx)。

### <a name="how-password-writeback-works"></a>パスワード ライトバックのしくみ
パスワード ライトバックには次の 3 つの主要コンポーネントがあります。

* パスワード リセットのクラウド サービス (これは Azure AD のパスワード変更ページにも統合されています)
* テナント固有の Azure Service Bus Relay
* オンプレミスのパスワード リセット エンドポイント

これらは、以下の図で説明するように統合されています。

  ![][001]

フェデレーション ユーザーまたはパスワード ハッシュ同期されたユーザーがクラウドで自分のパスワードをリセットまたは変更する場合は、次の処理が行われます。

1. ユーザーのパスワードの種類が確認されます。  パスワードがオンプレミスで管理されていることが確認されると、ライトバック サービスが起動し、実行します。  オンプレミスで管理されている場合は続行しますが、管理されていない場合は、パスワードをリセットできないことがユーザーに通知されます。
2. 次に、ユーザーが適切な認証ゲートを通過すると、パスワードのリセット画面が表示されます。
3. ユーザーは新しいパスワードを選択して確認します。
4. 送信をクリックすると、プレーンテキスト パスワードがライトバックのセットアップ プロセス時に作成された対称キーを使用して暗号化されます。
5. パスワードは暗号化された後、ペイロードに含められ、HTTPS チャネル経由でテナント固有の Service Bus Relay (ライトバックのセットアップ中にも設定される) に送信されます。  このリレーは、オンプレミスのインストールのみを認識するランダムに生成されたパスワードによって保護されます。
6. メッセージが Service Bus に到達すると、パスワード リセット エンドポイントが自動的にアクティブになり、保留中のリセット要求があるかどうかを確認します。
7. サービスは、クラウドのアンカー属性を使用して該当するユーザーを探します。  この検索が正常に動作するには、ユーザー オブジェクトが AD コネクタ スペースに存在し、これが対応する MV オブジェクトにリンクし、さらにこれが対応する AAD コネクタ オブジェクトにリンクする必要があります。 最後に、同期によってこのユーザー アカウントを検索するには、AD コネクタ オブジェクトから MV へのリンクに `Microsoft.InfromADUserAccountEnabled.xxx` 同期ルールが含まれている必要があります。  これが必要なのは、クラウドから呼び出される場合に、同期エンジンは cloudAnchor 属性を使用して AAD コネクタ スペース オブジェクトを検索し、MV オブジェクトのリンクに戻り、次に AD オブジェクトのリンクに戻るためです。 同じユーザーに対して複数の AD オブジェクト (マルチ フォレスト) がある可能性があるため、同期エンジンは `Microsoft.InfromADUserAccountEnabled.xxx` のリンクに依存して正しいユーザー アカウントを選択します。 このロジックの結果として、パスワード ライトバックが機能するには、Azure AD Connect をプライマリ ドメイン コントローラーに接続する必要があります。  これを行う必要がある場合は、プライマリ ドメイン コントローラー エミュレーターを使用するよう Azure AD Connect を構成できます。そのためには、Active Directory 同期コネクタの **[プロパティ]** を右クリックして **[configure directory partitions (ディレクトリ パーティションの構成)]** を選択します。 そこで **[domain controller connection settings (ドメイン コントローラーの接続の設定)]** セクションを探して、**[only use preferred domain controllers (優先ドメイン コントローラーのみを使用する)]** というチェック ボックスをオンにします。 注: 優先 DC が PDC エミュレーターではない場合、Azure AD Connect はパスワード ライトバックのため引き続き PDC にアクセスします。
8. ユーザー アカウントが見つかると、適切な AD フォレスト内で直接パスワードのリセットを試みます。
9. パスワードの設定操作に成功すると、ユーザーにパスワードが変更されたことが通知され、そのまま続行されます。
10. パスワードの設定操作に失敗した場合は、エラーが返され、やり直す必要があります。  サービスがダウンした、選択したパスワードが組織のポリシーを満たしていない、ローカル AD でユーザーが見つからないなど、さまざまな原因で操作に失敗する可能性があります。  多くの場合、特定のメッセージが表示され、問題解決の手段がユーザーに通知されます。

## <a name="scenarios-supported-for-password-writeback"></a>パスワード ライトバックでサポートされるシナリオ
次のセクションは、同期機能のバージョンでサポートされるシナリオについて説明します。  通常、パスワード ライトバックを使用する場合は、Azure AD Connect の自動更新機能を使用するか、最新バージョンの [Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect) をインストールすることを常にお勧めしています。

* [**サポートされる Azure AD Connect、Azure AD Sync、および DirSync クライアント**](#supported-clients)
* [**パスワード ライトバックで必要なライセンス**](#licenses-required-for-password-writeback)
* [**パスワード ライトバックでサポートされるオンプレミスの認証モード**](#on-premises-authentication-modes-supported-for-password-writeback)
* [**パスワード ライトバックでサポートされるユーザーと管理者の操作**](#user-and-admin-operations-supported-for-password-writeback)
* [**パスワード ライトバックでサポートされないユーザーと管理者の操作**](#user-and-admin-operations-not-supported-for-password-writeback)

### <a name="supported-clients"></a>サポートされるクライアント
パスワード ライトバックを使用する場合は、Azure AD Connect の自動更新機能を使用するか、最新バージョンの [Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect) をインストールすることを常にお勧めしています。

* **DirSync (任意のバージョン > 1.0.6862)** - "_未サポート_" - 基本的なライトバック機能のみをサポートします。製品グループによるサポートは終了しています。
* **Azure AD Sync** - "_非推奨_" - 基本的なライトバック機能のみをサポートします。アカウントのロック解除機能、豊富なログ記録、および Azure AD Connect で行われた信頼性の改善はありません。 そのため、アップグレードすることを**強く**お勧めします。
* **Azure AD Connect** - "_完全サポート_" - すべてのライトバック機能をサポートします。最善の新機能と最高の安定性/信頼性を取得するには、最新バージョンにアップグレードしてください。

### <a name="licenses-required-for-password-writeback"></a>パスワード ライトバックで必要なライセンス
パスワード ライトバックを使用するのには、次のライセンスのいずれかがテナントに割り当てられている必要があります。

* **Azure AD Premium P1**: パスワード ライトバックの使用に制限なし
* **Azure AD Premium P2**: パスワード ライトバックの使用に制限なし
* **Enterprise Moblity Suite**: パスワード ライトバックの使用に制限なし
* **Enterprise Cloud Suite**: パスワード ライトバックの使用に制限なし

試用または有料にかかわらず、Office 365 のライセンス プランではパスワード ライトバックを使用することはできません。 この機能を使用するためには、上記のプランのいずれかにアップグレードする必要があります。

Office 365 SKU でパスワード ライトバックを有効にする予定はありません。

### <a name="on-premises-authentication-modes-supported-for-password-writeback"></a>パスワード ライトバックでサポートされるオンプレミスの認証モード
パスワード ライトバックは、次のユーザー パスワードの種類に対して機能します。

* **クラウド専用ユーザー**: オンプレミスのパスワードがないため、この状況ではパスワード ライトバックは適用されません。
* **パスワード同期ユーザー**: パスワード ライトバックがサポートされます。
* **フェデレーション ユーザー**: パスワード ライトバックがサポートされます。
* **パススルー認証ユーザー**: パスワード ライトバックがサポートされます。

### <a name="user-and-admin-operations-supported-for-password-writeback"></a>パスワード ライトバックでサポートされるユーザーと管理者の操作
パスワードのライト バックは、次のすべての状況で実行されます。

* **サポートされるエンドユーザーの操作**
 * エンド ユーザーによる自発的なパスワード変更
 * エンド ユーザーによる強制的なパスワード変更 (パスワードの期限切れなど)
 * [パスワード リセット ポータル](https://passwordreset.microsoftonline.com)から実行されたエンド ユーザーによるパスワードのリセット
* **サポートされる管理者の操作**
 * 管理者による自発的なパスワード変更
 * 管理者による強制的なパスワード変更 (パスワードの期限切れなど)
 * [パスワード リセット ポータル](https://passwordreset.microsoftonline.com)から実行された管理者によるパスワードのリセット
 * [従来の Microsoft Azure 管理ポータル](https://manage.windowsazure.com)から管理者が開始したエンドユーザーのパスワードのリセット
 * [Azure Portal](https://portal.azure.com) から管理者が開始したエンドユーザーのパスワードのリセット

### <a name="user-and-admin-operations-not-supported-for-password-writeback"></a>パスワード ライトバックでサポートされないユーザーと管理者の操作
パスワードのライト バックは、次の状況では実行されません。

* **サポートされないエンドユーザーの操作**
 * PowerShell v1、v2、または Azure AD Graph API を使用した、エンド ユーザーによるパスワードのリセット
* **サポートされない管理者の操作**
 * [Microsoft Office 管理ポータル](https://portal.office.com)から管理者が開始したエンドユーザーのパスワードのリセット
 * PowerShell v1、v2、または Azure AD Graph API から管理者が開始したエンド ユーザーのパスワードのリセット

マイクロソフトでは、これらの制限を取り除くための作業を続けていますが、共有できる予定はまだありません。

## <a name="password-writeback-security-model"></a>パスワード ライトバックのセキュリティ モデル
パスワード ライトバックは、安全性と信頼性の高いサービスです。  ユーザーの情報を確実に保護するために、以下で説明する 4 層のセキュリティ モデルを実現しています。

* **テナント固有の Service Bus Relay** – サービスを設定すると、マイクロソフトでもアクセスできない、ランダムに生成された強力なパスワードで保護されたテナント固有の Service Bus Relay が設定されます。
* **ロックダウンされ、暗号強度の高いパスワード暗号化キー** – Service Bus Relay が作成されると、強力な非対称キー ペアが作成され、ネットワーク経由でパスワードが渡されるときに暗号化に使用されます。  このキーは、クラウド内の会社のシークレット ストアのみに存在し、厳重にロックダウンされ、ディレクトリ内のパスワードと同様に監査されます。
* **業界標準の TLS** – クラウド内でパスワードのリセットや変更操作が行われる場合は、プレーン テキスト パスワードが取得され、公開キーを使用して暗号化されます。  次に、これを HTTPS メッセージにして、マイクロソフトの SSL 証明書を使用して暗号化されたチャネルを介して Service Bus Relay に送信されます。  このメッセージが Service Bus に到着すると、オンプレミスのエージェントがアクティブになり、前に生成された強力なパスワードを使用して Service Bus に認証され、暗号化されたメッセージを取得し、生成された秘密キーを使用して復号化され、AD DS SetPassword API を使用してパスワードの設定を試みます。  この手順に従うと、クラウドで AD オンプレミスのパスワード ポリシー (複雑さ、年齢、履歴、フィルターなど) を適用できます。
* **メッセージの有効期限ポリシー** - 最後に、何らかの理由でオンプレミス サービスが停止しているために Service Bus でメッセージが表示される場合は、セキュリティをさらに強化するために数分後にタイムアウトになり、削除されます。

### <a name="password-writeback-encryption-details"></a>パスワード ライトバックの暗号化の詳細
次に示すのは、ユーザーがパスワード リセット要求を送信した後、その要求がオンプレミス環境に到着するまでに実行される暗号化の手順です。これらの手順によって、最大限のサービスの信頼性とセキュリティが確保されます。

* **手順 1 - 2048 ビット RSA キーによるパスワードの暗号化**: ユーザーがオンプレミスにライトバックするためにパスワードを送信すると、送信されたパスワードそのものが 2048 ビット RSA キーを使用して暗号化されます。

* **手順 2 - AES-GCM によるパッケージ レベルの暗号化**: 次に、AES-GCM を使用して、パッケージ全体 (パスワード + 必要なメタデータ) が暗号化されます。 これにより、ServiceBus チャネルに直接アクセスできる人物による内容の表示/改ざんを防止します。

* **手順 3 - すべての通信は TLS / SSL 経由で行われる**: さらに、ServiceBus でのすべての通信は、SSL/TLS チャネルで実行されます。 これにより、権限がないサード パーティからの保護が保証されます。

* **ステップ 4 - 半年ごとの自動キー ロールオーバー**: 最後に、半年ごとに自動的に、または Azure AD Connect でパスワード ライトバックが無効/再度有効になるたびに、すべてのキーがロールオーバーされます。これにより、最大限のサービスのセキュリティと安全性が確保されます。

### <a name="password-writeback-bandwidth-usage"></a>パスワード ライトバックの帯域幅の使用

パスワード ライトバックは、次の状況でのみ、要求をオンプレミスのエージェントに戻す、きわめて低い帯域幅のサービスです。

1. Azure AD Connect を通じて機能を有効または無効にしたときに 2 つのメッセージが送信されます。
2. サービスのハートビートとして 5 分おきに 1 回 1 つのメッセージが、サービスを実行している間中、送信されます。
3. 新しいパスワードが送信されるたびに 2 つのメッセージが送信されます。1 つは要求として操作を実行するメッセージです。後続のメッセージには、操作の結果が含まれます。 これらのメッセージは、次の状況で送信されます。
4. ユーザーのセルフサービスのパスワードのリセット時に新しいパスワードが送信された場合
5. ユーザーのパスワード変更操作時に新しいパスワードが送信された場合
6. 管理者によるユーザー パスワードのリセット時に新しいパスワードが送信された場合 (Azure 管理ポータルからのみ)

#### <a name="message-size-and-bandwidth-considerations"></a>メッセージ サイズと帯域幅に関する考慮事項

上記で説明したメッセージの各サイズは、通常 1 kb 未満です。つまり、負荷が大きい場合でも、パスワード ライトバック サービス自体で 1 秒間に最大でも数キロビットの帯域幅しか消費されないことを意味します。 各メッセージは、パスワードの更新操作で必要になった場合にのみリアルタイムで送信されるため、またメッセージのサイズが非常に小さいため、ライトバック機能の帯域幅は実質的に非常に小さく、実際に測定可能な影響を及ぼすには至りません。

## <a name="deploying-managing-and-accessing-password-reset-data-for-your-users"></a>ユーザーのパスワード リセット データのデプロイ、管理、およびアクセス
Azure AD Connect、PowerShell、Graph、またはマイクロソフトの登録エクスペリエンスを通して、ユーザーのパスワード リセット データの管理とアクセスを実行できます。  次に示すオプションを活用することで、ユーザーによる登録を必要とせずに、組織全体にパスワードのリセットをデプロイすることもできます。

  * [パスワードのリセットで使用されるデータ](#what-data-is-used-by-password-reset)
  * [エンド ユーザーによる登録を必要としないパスワード リセットのデプロイ](#deploying-password-reset-without-requiring-end-user-registration)
  * [パスワードをリセットするためにユーザーの登録時に発生すること](#what-happens-when-a-user-registers)
  * [ユーザーのパスワード リセット データにアクセスする](#how-to-access-password-reset-data-for-your-users)
  * [PowerShell を使用したパスワード リセット データの設定](#setting-password-reset-data-with-powershell)
  * [PowerShell を使用したパスワード リセット データの読み取り](#reading-password-reset-data-with-powershell)

### <a name="what-data-is-used-by-password-reset"></a>パスワードのリセットで使用されるデータ
次の表は、このデータがパスワードのリセット時に使用される場所と方法を示し、組織に適した認証オプションを決定する際に役立つように作られています。 この表では、このデータを検証しない入力パスからユーザーに代わってデータを指定する場合の形式の要件も示しています。

> [!NOTE]
> 会社電話は、現在ユーザーはディレクトリでこのプロパティを編集できないため、登録ポータルに表示されません。 この値は、管理者のみが設定できます。
>
>

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>連絡方法</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Active Directory のデータ要素</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Azure Active Directory のデータ要素</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用される場所 / 設定可能な場所</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>形式の要件</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>会社電話</p>
            </td>
            <td>
              <p>telephoneNumber</p>
              <p>このプロパティは、Azure Active Directory の PhoneNumber 属性に同期でき、ユーザーによる登録を必要とせずに、パスワード リセットですぐに使用できます。</p>
            </td>
            <td>
              <p>PhoneNumber</p>
              <p>例: Set-MsolUser -UserPrincipalName JWarner@contoso.com -PhoneNumber "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>使用される場所: </p>
              <p>パスワード リセット ポータル</p>
              <p>設定可能な場所: </p>
              <p>PhoneNumber は、PowerShell、DirSync、Microsoft Azure 管理ポータル、Office の管理ポータルから設定可能です</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz (例: +1 1234567890)</p>
              <ul>
                <li class="unordered">
国コードを提供する必要があります<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
市外局番を指定する必要があります (該当する場合)<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
国コードの前に + 記号を指定する必要があります<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
国コードと残りの番号の間にスペースを入れる必要があります<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
内線番号はサポートされません。内線番号が指定されている場合は、通話をディスパッチする前に電話番号から内線番号が削除されます。<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>携帯電話</p>
            </td>
            <td>
              <p>モバイル</p>
              <p>このプロパティは、Azure Active Directory の MobilePhone 属性に同期でき、ユーザーによる登録を必要とせずに、パスワード リセットですぐに使用できます。</p>
              <p>現時点では、このプロパティを AuthenticationPhone に同期することはできません。</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>または</p>
              <p>MobilePhone</p>
              <p>(認証用電話はデータが存在する場合に使用されます。それ以外の場合は、携帯電話フィールドが使用されます)。</p>
              <p>例: Set-MsolUser -UserPrincipalName JWarner@contoso.com -MobilePhone "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>使用される場所: </p>
              <p>パスワード リセット ポータル</p>
              <p>登録ポータル</p>
              <p>設定可能な場所:  </p>
              <p>AuthenticationPhone は、パスワード リセット登録ポータルまたは MFA 登録ポータルから設定可能です。</p>
              <p>MobilePhone は、PowerShell、Azure AD Connect、Microsoft Azure 管理ポータル、および Microsoft Office 管理ポータルから設定できます。</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz (例: +1 1234567890)</p>
              <ul>
                <li class="unordered">
国コードを提供する必要があります。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
市外局番を指定する必要があります (該当する場合)。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
国コードの前に + 記号を指定する必要があります。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
国コードと残りの番号の間にスペースを入れる必要があります。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
内線番号はサポートされません。内線番号が指定されている場合は、通話をディスパッチするときに無視されます。<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>連絡用メール アドレス</p>
            </td>
            <td>
              <p>使用できません。</p>
              <p>現時点では、Active Directory の値を AuthenticationEmail プロパティまたは AlternateEmailAddresses [0] プロパティに同期することはできません。 </p>
              <p>PowerShell を使用して、AlternateEmailAddresses [0] を設定できます。 これを行うための手順については、この表のすぐ後のセクションに説明があります。</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>または</p>
              <p>AlternateEmailAddresses [0] </p>
              <p>(認証用電子メールはデータが存在する場合に使用されます。それ以外の場合は、連絡用メール アドレス フィールドが使用されます)</p>
              <p>注: 連絡用メール アドレス フィールドは、ディレクトリ内の文字列の配列として指定されます。  この配列内の最初のエントリが使用されます。</p>
              <p>例: Set-MsolUser -UserPrincipalName JWarner@contoso.com -AlternateEmailAddresses "email@live.com"</p>
            </td>
            <td>
              <p>使用される場所: </p>
              <p>パスワード リセット ポータル</p>
              <p>登録ポータル</p>
              <p>設定可能な場所:  </p>
              <p>AuthenticationEmail は、パスワード リセット登録ポータルまたは MFA 登録ポータルから設定可能です。</p>
              <p>AlternateEmail は、PowerShell、Microsoft Azure 管理ポータル、Office の管理ポータルから設定可能です。</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a> または甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
電子メールは標準的な形式に従う必要があります。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Unicode の電子メールがサポートされています。<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>セキュリティの質問と回答</p>
            </td>
            <td>
              <p>使用できません。</p>
              <p>現時点では、Active Directory のセキュリティの質問または回答を同期することはできません。</p>
            </td>
            <td>
              <p>ディレクトリ内で直接変更することはできません。</p>
              <p>パスワード リセットのエンド ユーザー登録プロセス中にのみ設定できます。</p>
            </td>
            <td>
              <p>使用される場所: </p>
              <p>パスワード リセット ポータル</p>
              <p>登録ポータル </p>
              <p>設定可能な場所:  </p>
              <p>セキュリティの質問は、Microsoft Azure 管理ポータルからのみ設定可能です。</p>
              <p>特定のユーザーからのセキュリティに関する質問への回答は、登録ポータルからのみ設定可能です。</p>
            </td>
            <td>
              <p>セキュリティの質問は、最小 3 文字 ～ 最大 200 文字で指定します。</p>
              <p>回答は、最小 3 文字 ～ 最大 40 文字で指定します。</p>
            </td>
          </tr>
        </tbody></table>


### <a name="deploying-password-reset-without-requiring-end-user-registration"></a>エンド ユーザーによる登録を必要としないパスワード リセットのデプロイ
ユーザーによる登録を必要とせずにパスワードのリセットをデプロイする場合は、次の 2 つのオプションのいずれかを使用して簡単に実行できます。 これは、SSPR を使用できるように多数のユーザーをブロック解除すると同時に、ユーザーが登録プロセス中にこの情報を確認できるようにする便利な方法として利用できます。

今日、大口のお客様の多くは、これらのオプションを使用して、パスワードのリセットを非常に迅速に行っています。

#### <a name="synchronize-phone-numbers-with-azure-ad-connect"></a>電話番号を Azure AD Connect と同期する
データを次のフィールドのいずれかまたは両方に同期した場合、ユーザーによる登録を必要とせずに、パスワードのリセットですぐに使用できます。

* 携帯電話
* 会社電話

オンプレミスで更新する必要があるプロパティについては、前述の「[パスワードのリセットで使用されるデータ](#what-data-is-used-by-password-reset)」セクションを参照してください。  

システムで正常に動作するように、電話番号の形式が "+1 1234567890" になっていることを確認してください。

#### <a name="set-phone-numbers-or-emails-with-powershell"></a>PowerShell を使用して電話番号または電子メールを設定する
次のフィールドを 1 つ以上設定した場合、ユーザーによる登録を必要とせずに、パスワードのリセットですぐに使用できます。

* 携帯電話
* 会社電話
* 連絡用メール アドレス

PowerShell を使用してこれらのプロパティを設定する方法については、「[PowerShell を使用したパスワード リセット データの設定](#setting-password-reset-data-with-powershell)」セクションを参照してください。

システムで正常に動作するように、電話番号の形式が "+1 1234567890" になっていることを確認してください。

### <a name="what-happens-when-a-user-registers"></a>ユーザーの登録時に発生すること
ユーザーが登録するとき、登録ページには **常に** 次のフィールドが設定されます。

* 認証用電話
* 認証用メール
* セキュリティの質問と回答

**[携帯電話]** または **[連絡用メール アドレス]** に値が指定されている場合、ユーザーはそれらを使用してすぐにパスワードをリセットすることができます。これはユーザーがサービスに登録していない場合でも実行できます。  さらに、これらの値は、ユーザーが初めて登録するときに表示され、ユーザーは必要に応じてそれらを変更することができます。  ただし、ユーザーが正常に登録された後、これらの値は、それぞれ **[認証用電話]** フィールドと **[認証用メール]** フィールドの固定値になります。

### <a name="how-to-access-password-reset-data-for-your-users"></a>ユーザーのパスワード リセット データにアクセスする方法
#### <a name="data-settable-via-synchronization"></a>同期によって設定できるデータ
次のフィールドは、オンプレミスから同期することができます。

* 携帯電話
* 会社電話

#### <a name="data-settable-with-azure-ad-powershell--azure-ad-graph"></a>Azure AD PowerShell と Azure AD Graph を使用して設定できるデータ
次のフィールドは、Azure AD PowerShell と Azure AD Graph API を使用して設定できます。

* 連絡用メール アドレス
* 携帯電話
* 会社電話

#### <a name="data-settable-with-registration-ui-only"></a>登録 UI でのみ設定できるデータ
次のフィールドは、SSPR 登録 UI でのみアクセスできます (https://aka.ms/ssprsetup):

* セキュリティの質問と回答

#### <a name="data-readable-with-azure-ad-powershell--azure-ad-graph"></a>Azure AD PowerShell と Azure AD Graph を使用して読み取ることができるデータ
次のフィールドは、Azure AD PowerShell と Azure AD Graph API を使用してアクセスすることができます。

* 連絡用メール アドレス
* 携帯電話
* 会社電話
* 認証用電話
* 認証用メール

### <a name="setting-password-reset-data-with-powershell"></a>PowerShell を使用したパスワード リセット データの設定
次のフィールドの値は、Azure AD PowerShell を使用して設定することができます。

* 連絡用メール アドレス
* 携帯電話
* 会社電話

**_PowerShell V1_**

操作を開始する前に、 [Azure AD PowerShell モジュールをダウンロードしてインストールする](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)必要があります。  インストールした後、次の手順に従って各フィールドを構成できます。

**_PowerShell V2_**

操作を開始する前に、[Azure AD V2 PowerShell モジュールをダウンロードしてインストールする](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure%20AD%20Cmdlets/AzureAD/index.md)必要があります。 インストールした後、次の手順に従って各フィールドを構成できます。

Install-Module をサポートする最新バージョンの PowerShell を簡単にインストールには、次のコマンドを実行します (最初の行は、既にインストールされているかどうかをチェックするだけです)。

```
Get-Module AzureADPreview
Install-Module AzureADPreview
Connect-AzureAD
```

#### <a name="alternate-email---how-to-set-alternate-email-with-powershell"></a>連絡用メール アドレス - PowerShell を使用して連絡用メール アドレスを設定する方法
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -OtherMails @("email@domain.com")
```

#### <a name="mobile-phone---how-to-set-mobile-phone-with-powershell"></a>携帯電話 - PowerShell を使用して携帯電話を設定する方法
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -Mobile "+1 1234567890"
```

#### <a name="office-phone---how-to-set-office-phone-with-powershell"></a>会社電話 - PowerShell を使用して会社電話を設定する方法
**_PowerShell V1_**

```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

**_PowerShell V2_**

```
Connect-AzureAD
Set-AzureADUser -ObjectId user@domain.com -TelephoneNumber "+1 1234567890"
```

### <a name="reading-password-reset-data-with-powershell"></a>PowerShell を使用したパスワード リセット データの読み取り
次のフィールドの値は、Azure AD PowerShell を使用して読み取ることができます。

* 連絡用メール アドレス
* 携帯電話
* 会社電話
* 認証用電話
* 認証用メール

#### <a name="alternate-email---how-to-read-alternate-email-with-powershell"></a>連絡用メール アドレス - PowerShell を使用して連絡用メール アドレスを読み取る方法
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

**_PowerShell V2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select otherMails
```

#### <a name="mobile-phone---how-to-read-mobile-phone-with-powershell"></a>携帯電話 - PowerShell を使用して携帯電話を読み取る方法
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

**_PowerShell v2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select Mobile
```

#### <a name="office-phone---how-to-read-office-phone-with-powershell"></a>会社電話 - PowerShell を使用して会社電話を読み取る方法
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

**_PowerShell V2_**

```
Connect-AzureAD
Get-AzureADUser -ObjectID user@domain.com | select TelephoneNumber
```

#### <a name="authentication-phone---how-to-read-authentication-phone-with-powershell"></a>認証用電話 - PowerShell を使用して認証用電話を読み取る方法
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

**_PowerShell V2_**

```
Not possible in PowerShell V2
```

#### <a name="authentication-email---how-to-read-authentication-email-with-powershell"></a>認証用メール - PowerShell を使用して認証用メールを読み取る方法
**_PowerShell V1_**

```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

**_PowerShell V2_**

```
Not possible in PowerShell V2
```
## <a name="how-does-password-reset-work-for-b2b-users"></a>B2B ユーザーに対するパスワード リセットの動作
パスワードのリセットと変更は、B2B のすべての構成で完全にサポートされています。  パスワードのリセットでサポートされる 3 つの明示的な B2B の事例を次に示します。

1. **既存の Azure AD テナントがあるパートナー組織のユーザー**: パートナーを組んでいる組織に既存の Azure AD テナントがある場合は、**そのテナントで有効になっているパスワード リセット ポリシーが常に尊重されます**。 パスワード リセットを機能させるためにパートナー組織が実行する必要があるのは、Azure AD SSPR が有効であることを確認するだけです。有効にしても O365 顧客に追加料金は発生しません。「[パスワード管理の概要](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords)」ガイドの手順に従って有効にすることができます。
2. **[セルフ サービス サインアップ](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-signup)を使用してサインアップしたユーザー**: パートナーを組んでいる組織が[セルフ サービス サインアップ](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-signup)機能を使用してテナントに参加している場合は、登録された電子メールを使用して、追加設定なしでパスワードをリセットできます。
3. **B2B ユーザー**: 新しい [Azure AD B2B 機能](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)を使用して作成された B2B ユーザーも、招待プロセス中に登録した電子メールを使用して、追加設定なしで自分のパスワードをリセットできます。

これらをテストするには、いずれかのパートナー ユーザーとして http://passwordreset.microsoftonline.com に移動します。  連絡用電子メールまたは認証用電子メールが定義されている限り、パスワードのリセットは予想どおりに機能します。  ここで SSPR によって使用されるデータの詳細については、「[パスワードのリセットで使用されるデータ](https://azure.microsoft.com/en-us/documentation/articles/active-directory-passwords-learn-more/#what-data-is-used-by-password-reset)」を参照してください。

## <a name="next-steps"></a>次のステップ
Azure AD のパスワードのリセットに関するすべてのドキュメント ページへのリンクを以下に示します。

* **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md#reset-your-password)にお進みください。
* [**しくみ**](active-directory-passwords-how-it-works.md) - サービスの 6 つの異なるコンポーネントとそれぞれの機能について説明します。
* [**概要**](active-directory-passwords-getting-started.md) - ユーザーによるクラウドまたはオンプレミスのパスワードのリセットと変更を許可する方法について説明します。
* [**カスタマイズ**](active-directory-passwords-customize.md) - 組織のニーズに合わせてサービスの外観と動作をカスタマイズする方法について説明します。
* [**ベスト プラクティス**](active-directory-passwords-best-practices.md) - 組織内でのパスワードの迅速なデプロイと効果的な管理方法について説明します。
* [**洞察を得る**](active-directory-passwords-get-insights.md) - 統合レポート機能について説明します。
* [**FAQ**](active-directory-passwords-faq.md) -よく寄せられる質問の回答を得ます。
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - サービスに関する問題を迅速にトラブルシューティングする方法について説明します。

[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"

