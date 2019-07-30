---
title: LDAP 認証と Azure MFA Server - Azure Active Directory
description: LDAP 認証と Azure Multi-Factor Authentication Server のデプロイ。
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
ms.openlocfilehash: bc67fb216451bd19fe8cfb327c101aa989228b01
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536908"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP 認証と Azure Multi-Factor Authentication Server

既定では、Azure Multi-Factor Authentication Server は、Active Directory からユーザーをインポートするか同期するように構成されます。 ただし、ADAM ディレクトリや特定の Active Directory ドメイン コントローラーなどの別の LDAP ディレクトリにバインドするように構成できます。 LDAP 経由でディレクトリに接続する場合、Azure Multi-Factor Authentication Server は、認証を実行する LDAP プロキシとして機能できます。 RADIUS ターゲットとしての LDAP バインドの使用、IIS 認証使用時のユーザーの事前認証、Azure MFA ユーザー ポータルでのプライマリ認証も許可します。

Azure Multi-Factor Authentication を LDAP プロキシとして使用するには、LDAP クライアント (VPN アプライアンスやアプリケーションなど) と LDAP ディレクトリ サーバーの間に Azure Multi-Factor Authentication Server を挿入します。 クライアント サーバーと LDAP ディレクトリの両方と通信するように Azure Multi-Factor Authentication Server を構成する必要があります。 この構成の中で、Azure Multi-Factor Authentication Server は、クライアント サーバーとアプリケーションから LDAP 要求を受け取り、プライマリ資格情報を検証するためにターゲット LDAP ディレクトリ サーバーに要求を転送します。 LDAP ディレクトリでプライマリ資格情報を検証する場合、Azure Multi-Factor Authentication は 2 段階目の識別子認証を実行し、応答を LDAP クライアントに送信します。 LDAP サーバーの認証と 2 段階目の認証の両方が成功した場合のみ、認証全体が成功します。

> [!IMPORTANT]
> 2019 年 7 月 1 日より、Microsoft では新しいデプロイの MFA Server が提供されなくなります。 ユーザーからの多要素認証が必要な新しいお客様は、クラウドベースの Azure Multi-Factor Authentication を使用する必要があります。 7 月 1 日より前に MFA Server をアクティブ化した既存のお客様は、最新バージョンの今後の更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成することができます。

## <a name="configure-ldap-authentication"></a>LDAP 認証を構成する

LDAP 認証を構成するには、Azure Multi-Factor Authentication Server を Windows サーバーにインストールします。 次の手順に従います。

### <a name="add-an-ldap-client"></a>LDAP クライアントを追加する

1. Azure Multi-Factor Authentication Server で、左側のメニューの [LDAP 認証] アイコンをクリックします。
2. **[LDAP 認証を有効にする]** チェック ボックスをオンにします。

   ![MFA サーバーでの LDAP 認証](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. Azure Multi-Factor Authentication LDAP サービスを、LDAP 要求をリッスンする標準ポート以外のポートにバインドする必要がある場合は、[クライアント] タブで TCP ポートと SSL ポートを変更します。
4. クライアントから Azure Multi-Factor Authentication Server に対して LDAPS を使用する場合は、MFA サーバーと同じサーバー上に SSL 証明書をインストールする必要があります。 [SSL 証明書] ボックスの隣にある **[参照]** をクリックし、セキュリティで保護された接続で使用する証明書を選択します。
5. **[追加]** をクリックします。
6. [LDAP クライアントの追加] ダイアログ ボックスで、Server に対して認証するアプライアンス、サーバー、またはアプリケーションの IP アドレスを入力し、アプリケーション名 (省略可能) を入力します。 アプリケーション名は Azure Multi-Factor Authentication レポートに表示され、SMS またはモバイル アプリの認証メッセージにも表示される場合があります。
7. すべてのユーザーがサーバーにインポート済みであるかインポート予定であり、2 段階認証の対象となる場合は、 **[Require Azure Multi-Factor Authentication user match (Azure Multi-Factor Authentication のユーザー照合が必要)]** ボックスをオンにします。 多数のユーザーがまだサーバーにインポートされていない、または 2 段階認証から除外される場合、ボックスはオフのままにします。 この機能の追加情報については、MFA サーバーのヘルプ ファイルを参照してください。

さらに LDAP クライアントを追加するには、上記の手順を繰り返します。

### <a name="configure-the-ldap-directory-connection"></a>LDAP ディレクトリ接続を構成する

Azure Multi-Factor Authentication が LDAP 認証を受けるように構成するときは、これらの認証を LDAP ディレクトリに委任する必要があります。 したがって、[ターゲット] タブには LDAP ターゲットを使用する 1 つのオプションだけがグレー表示されます。

> [!NOTE]
> ディレクトリ統合では、Active Directory Domain Services 以外のディレクトリの操作は保証されません。

1. LDAP ディレクトリ接続を構成するには、 **[ディレクトリ統合]** アイコンをクリックします。
2. [設定] タブで、 **[特定の LDAP 構成を使用する]** ラジオ ボタンを選択します。
3. **[編集]** を選択します。
4. [LDAP 構成の編集] ダイアログ ボックスで、LDAP ディレクトリに接続するために必要な情報を各フィールドに入力します。 各フィールドの説明は、Azure Multi-Factor Authentication Server のヘルプ ファイルに含まれています。

    ![ディレクトリ統合 LDAP 構成](./media/howto-mfaserver-dir-ldap/ldap.png)

5. **[テスト]** ボタンをクリックして、LDAP 接続をテストします。
6. LDAP 接続テストが成功した場合、 **[OK]** ボタンをクリックします。
7. **[フィルター]** タブをクリックします。Server は、Active Directory からコンテナー、セキュリティ グループ、およびユーザーを読み込むように事前構成されています。 別の LDAP ディレクトリにバインドする場合は、表示されるフィルターの編集が必要になることがあります。 フィルターの詳細については、 **[ヘルプ]** リンクをクリックしてください。
8. **[属性]** タブをクリックします。Server は、Active Directory から属性をマッピングするように事前構成されています。
9. 別の LDAP ディレクトリにバインドしたり、事前構成されている属性のマッピングを変更する場合は、 **[編集]** をクリックします。
10. [属性の編集] ダイアログ ボックスで、ディレクトリの LDAP 属性マッピングを変更します。 属性名はキーボードから入力するか、各フィールドの横にある **[…]** ボタンをクリックして選択できます。 属性の詳細については、 **[ヘルプ]** リンクをクリックしてください。
11. **[OK]** ボタンをクリックします。
12. **[会社の設定]** アイコンをクリックし、 **[ユーザー名の解決]** タブを選択します。
13. ドメインに参加しているサーバーから Active Directory に接続する場合は、 **[Windows セキュリティ識別子 (SID) をユーザー名の照合に使用する]** を選択したままにします。 それ以外の場合は、 **[LDAP 一意識別子の属性をユーザー名の照合に使用する]** を選択します。

**[LDAP 一意識別子の属性をユーザー名の照合に使用する]** が選択されている場合、Azure Multi-Factor Authentication Server は、各ユーザーを LDAP ディレクトリ内の一意識別子に解決しようとします。 [ディレクトリ統合] の [属性] タブで定義されたユーザー名の各属性に対して LDAP 検索が実行されます。ユーザーが認証されると、ユーザー名は、LDAP ディレクトリ内の一意の識別子に解決されます。 この一意の識別子が、Azure Multi-factor Authentication データ ファイル内のユーザーと照合するために使用されます。 これにより、大文字と小文字を区別せず、ユーザー名の形式 (長短) を問わない比較が可能になります。

上記の手順を完了すると、MFA サーバーは構成されたクライアントからの LDAP アクセス要求のリッスンを構成されたポートで開始し、LDAP ディレクトリに対する認証要求のプロキシとして機能します。

## <a name="configure-ldap-client"></a>LDAP クライアントを構成する

LDAP クライアントを構成するには、次のガイドラインに従います。

* Azure Multi-Factor Authentication Server でのアプライアンス、サーバー、アプリケーションの LDAP 経由の認証を、あたかも LDAP ディレクトリで行われるかのように構成します。 サーバー名または IP アドレスを Azure Multi-Factor Authentication Server のサーバー名または IP アドレスにすること以外は、LDAP ディレクトリに直接接続するために通常使用するものと同じ設定を使用します。
* LDAP ディレクトリでユーザーの資格情報を検証し、2 段階目の認証を実行し、応答を受け取って LDAP アクセス要求に応答する時間があるように、LDAP タイムアウトは 30 ～ 60 秒に構成します。
* LDAPS を使用する場合、LDAP クエリを行うアプライアンスまたはサーバーは、Azure Multi-Factor Authentication Server にインストールされている SSL 証明書を信頼する必要があります。
