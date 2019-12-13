---
title: SSPR のパスワード ライトバックを構成する - Azure Active Directory
description: Azure AD と Azure AD Connect を使って、オンプレミスのディレクトリにパスワードを書き戻します
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67737aed3bd9961a37dc761fddf608d9bcfe3ffe
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74847254"
---
# <a name="how-to-configure-password-writeback"></a>方法: パスワード ライトバックを構成する

以下の手順は、環境で[簡単](../hybrid/how-to-connect-install-express.md)設定または[カスタム](../hybrid/how-to-connect-install-custom.md)設定を使って、Azure AD Connect を既に構成してあることが前提になっています。

1. パスワード ライトバックを構成し、有効化するには、Azure AD Connect サーバーにサインインして **Azure AD Connect** 構成ウィザードを開始します。
2. **[ようこそ]** ページで **[構成]** を選びます。
3. **[追加のタスク]** ページで **[同期オプションのカスタマイズ]** を選んで、 **[次へ]** を選びます。
4. **[Azure AD に接続]** ページで全体管理者の資格情報を入力し、 **[次へ]** を選びます。
5. **[Connect ディレクトリ]** ページおよび **[ドメイン/OU のフィルタリング]** ページで、 **[次へ]** を選びます。
6. **[オプション機能]** ページで **[パスワード ライトバック]** の横にあるチェック ボックスをオンにし、 **[次へ]** を選びます。
   ![Azure AD Connect でパスワード ライトバックを有効にする][Writeback]
7. **[構成の準備完了]** ページで **[構成]** を選び、処理が完了するまで待ちます。
8. 構成の完了が表示されたら、 **[終了]** を選びます。

パスワード ライトバックに関連する一般的なトラブルシューティングのタスクについては、トラブルシューティング記事の「[パスワード ライトバックのトラブルシューティング](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback)」セクションをご覧ください。

> [!WARNING]
> [Azure Access Control Service (ACS) が 2018 年 11 月 7 日に廃止される](../develop/active-directory-acs-migration.md)と、パスワード ライトバックは Azure AD Connect バージョン 1.0.8641.0 以前を使用している顧客には機能しなくなります。 Azure AD Connect バージョン 1.0.8641.0 以前では、パスワード ライトバックは ACS に依存しているため、この時点でこの機能を許可することはなくなります。
>
> 稼働中に中断しないように、Azure AD Connect の前のバージョンからより新しいバージョンにアップグレードします。「[Azure AD Connect: 旧バージョンから最新バージョンにアップグレードする](../hybrid/how-to-upgrade-previous-version.md)」の記事を参照してください。
>

## <a name="licensing-requirements-for-password-writeback"></a>パスワード ライトバックに必要なライセンス

**オンプレミスの書き戻しによるセルフサービスのパスワードのリセット/変更/ロック解除は、Azure AD の Premium 機能です**。 ライセンスの詳細については、[Azure Active Directory の価格サイト](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

パスワード ライトバックを使用するには、次のいずれかのライセンスがご自分のテナントに割り当てられている必要があります。

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 または A3
* Enterprise Mobility + Security E5 または A5
* Microsoft 365 E3 または A3
* Microsoft 365 E5 または A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> スタンドアロンの Office 365 ライセンス プランでは、"*セルフサービスによるパスワードのリセット/変更/ロック解除 (オンプレミスの書き戻しが可能) をサポートしていません*"。この機能を動作させるには、上記プランのいずれかが必要になります。
>

## <a name="active-directory-permissions-and-on-premises-password-complexity-policies"></a>Active Directory のアクセス許可とオンプレミスのパスワードの複雑性ポリシー 

SSPR の範囲内に入るには、Azure AD Connect ユーティリティで指定されているアカウントに次の項目が設定されている必要があります。

* **パスワードのリセット** 
* **[パスワードの変更]** 
* `lockoutTime` に対する**書き込みアクセス許可**
* `pwdLastSet` に対する**書き込みアクセス許可**
* 次のいずれかに対する**拡張権限**:
   * そのフォレスト内の*各ドメイン*のルート オブジェクト
   * SSPR の範囲内にしたいユーザー組織単位 (OU)

説明されているアカウントがどのアカウントのことか明らかでない場合は、Azure Active Directory Connect の構成 UI を開き、 **[現在の構成を表示する]** オプションを選びます。 アクセス許可を追加する必要があるアカウントが、 **[同期されたディレクトリ]** の下に表示されます。

これらのアクセス許可を設定した場合、各フォレストの MA サービス アカウントが、そのフォレスト内のユーザー アカウントの代わりに、パスワードを管理できるようになります。 

> [!IMPORTANT]
> これらのアクセス許可を割り当てないと、ライトバックが正常に構成されているように思われる場合でも、クラウドからオンプレミスのパスワードを管理しようとするとエラーが発生します。
>

> [!NOTE]
> ディレクトリ内のすべてのオブジェクトに対してこれらのアクセス許可のレプリケーションを行うには、最大で 1 時間以上かかることがあります。
>

パスワード ライトバックを行うための適切なアクセス許可を設定するには、以下の手順を完了します。

1. 適切なドメインの管理アクセス許可を持つアカウントで [Active Directory ユーザーとコンピューター] を開きます。
2. **[表示]** メニューで、 **[高度な機能]** がオンになっていることを確認します。
3. 左側のパネルで、ドメインのルートを表すオブジェクトを右クリックし、 **[プロパティ]**  >  **[セキュリティ]**  >  **[詳細設定]** の順に選びます。
4. **[アクセス許可]** タブで **[追加]** を選びます。
5. (Azure AD Connect のセットアップから) アクセス許可を適用するアカウントを選びます。
6. **[適用対象]** ドロップダウン ボックスの一覧で、 **[ユーザーの子孫オブジェクト]** オブジェクトを選びます。
7. **[アクセス許可]** で次のオプションのボックスを選択します。
    * **[パスワードの変更]**
    * **パスワードのリセット**
8. **[プロパティ]** で次のオプションのボックスを選択します。
    * **Write lockoutTime**
    * **Write pwdLastSet**
9. **[適用]/[OK]** を選んで変更を適用し、開いているすべてのダイアログ ボックスを終了します。

権限のソースがオンプレミスにあるため、同じ接続済みデータ ソースからパスワードの複雑性ポリシーが適用されます。 [パスワードの変更禁止期間] の既存のグループ ポリシーを変更したことを確認します。 グループ ポリシーは 1 に設定しないでください。これは、パスワードを更新するには少なくとも 1 日経過している必要があることを意味します。 0 に設定されていることを確認する必要があります。 これらの設定は、 **[コンピューターの構成] > [ポリシー] > [Windows の設定] > [セキュリティの設定] > [アカウント ポリシー]** の下の `gpmc.msc` にあります。 `gpupdate /force` を実行して、確実に変更を有効にしてください。 

## <a name="next-steps"></a>次の手順

[パスワード ライトバックとは何ですか。](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Azure AD Connect でパスワード ライトバックを有効にする"
