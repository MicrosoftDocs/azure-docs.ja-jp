---
title: Azure AD SSPR のパスワード ライトバックを構成する方法
description: Azure AD と Azure AD Connect を使って、オンプレミスのディレクトリにパスワードを書き戻します
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: e613ff742096077fe1765d4b855b6c7d409cc228
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158949"
---
# <a name="how-to-configure-password-writeback"></a>方法: パスワード ライトバックを構成する

Microsoft では、パスワード ライトバックの使用時に [Azure AD Connect](./../connect/active-directory-aadconnect-get-started-express.md) の自動更新機能を使用することをお勧めしています。

以下の手順は、環境で[簡単](./../connect/active-directory-aadconnect-get-started-express.md)設定または[カスタム](./../connect/active-directory-aadconnect-get-started-custom.md)設定を使って、Azure AD Connect を既に構成してあることが前提になっています。

1. パスワード ライトバックを構成し、有効化するには、Azure AD Connect サーバーにサインインして **Azure AD Connect** 構成ウィザードを開始します。
2. **[ようこそ]** ページで **[構成]** を選びます。
3. **[追加のタスク]** ページで **[同期オプションのカスタマイズ]** を選んで、**[次へ]** を選びます。
4. **[Azure AD に接続]** ページで全体管理者の資格情報を入力し、**[次へ]** を選びます。
5. **[Connect ディレクトリ]** ページおよび **[ドメイン/OU のフィルタリング]** ページで、**[次へ]** を選びます。
6. **[オプション機能]** ページで **[パスワード ライトバック]** の横にあるチェック ボックスをオンにし、**[次へ]** を選びます。
   ![Azure AD Connect でパスワード ライトバックを有効にする][Writeback]
7. **[構成の準備完了]** ページで **[構成]** を選び、処理が完了するまで待ちます。
8. 構成の完了が表示されたら、**[終了]** を選びます。

パスワード ライトバックに関連する一般的なトラブルシューティングのタスクについては、トラブルシューティング記事の「[パスワード ライトバックのトラブルシューティング](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback)」セクションをご覧ください。

## <a name="active-directory-permissions"></a>Active Directory のアクセス許可

SSPR の範囲内に入るには、Azure AD Connect ユーティリティで指定されているアカウントに次の項目が設定されている必要があります。

* **パスワードのリセット** 
* **[パスワードの変更]** 
* `lockoutTime` に対する**書き込みアクセス許可**
* `pwdLastSet` に対する**書き込みアクセス許可**
* 次のいずれかに対する**拡張権限**:
   * そのフォレスト内の*各ドメイン*のルート オブジェクト
   * SSPR の範囲内にしたいユーザー組織単位 (OU)

説明されているアカウントがどのアカウントのことか明らかでない場合は、Azure Active Directory Connect の構成 UI を開き、**[現在の構成を表示する]** オプションを選びます。 アクセス許可を追加する必要があるアカウントが、**[同期されたディレクトリ]** の下に表示されます。

これらのアクセス許可を設定した場合、各フォレストの MA サービス アカウントが、そのフォレスト内のユーザー アカウントの代わりに、パスワードを管理できるようになります。 

> [!IMPORTANT]
> これらのアクセス許可を割り当てないと、ライトバックが正常に構成されているように思われる場合でも、クラウドからオンプレミスのパスワードを管理しようとするとエラーが発生します。
>

> [!NOTE]
> ディレクトリ内のすべてのオブジェクトにこれらのアクセス許可をレプリケートするには、最大 1 時間かそれ以上かかることがあります。
>

パスワード ライトバックを行うための適切なアクセス許可を設定するには、以下の手順を完了します。

1. 適切なドメインの管理アクセス許可を持つアカウントで [Active Directory ユーザーとコンピューター] を開きます。
2. **[表示]** メニューで、**[拡張機能]** がオンになっていることを確認します。
3. 左側のパネルで、ドメインのルートを表すオブジェクトを右クリックし、**[プロパティ]** > **[セキュリティ]** > **[詳細設定]** の順に選びます。
4. **[アクセス許可]** タブで **[追加]** を選びます。
5. (Azure AD Connect のセットアップから) アクセス許可を適用するアカウントを選びます。
6. **[適用対象]** ドロップダウン ボックスの一覧で、**[Descendent user]\(下位ユーザー\)** オブジェクトを選びます。
7. **[アクセス許可]** で次のチェック ボックスをオンにします。
    * **パスワードのリセット**
    * **[パスワードの変更]**
    * **Write lockoutTime**
    * **Write pwdLastSet**
8. **[適用]/[OK]** を選んで変更を適用し、開いているすべてのダイアログ ボックスを終了します。

## <a name="next-steps"></a>次の手順

[パスワード ライトバックとは何ですか。](concept-sspr-writeback.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Azure AD Connect でパスワード ライトバックを有効にする"
