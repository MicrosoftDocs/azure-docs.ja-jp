---
title: RADIUS 認証と Azure MFA Server - Azure Active Directory
description: RADIUS 認証と Azure Multi-Factor Authentication Server のデプロイ。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17e040492b1d986215aeb77ea14ebff53946f78e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056014"
---
# <a name="integrate-radius-authentication-with-azure-multi-factor-authentication-server"></a>RADIUS 認証と Azure Multi-Factor Authentication Server の統合

RADIUS は、認証要求を承認してそれらの要求を処理する標準のプロトコルです。 Azure Multi-Factor Authentication Server は RADIUS サーバーとして機能します。 これを RADIUS クライアント (VPN アプライアンス) と認証対象の間に置くことで 2 段階認証が追加されます。 この場合、認証ターゲットは、Active Directory や LDAP ディレクトリ、別の RADIUS サーバーなどになります。 Azure Multi-Factor Authentication (MFA) が機能するには、クライアント サーバーと認証ターゲットの両方と通信できるように Azure MFA Server を構成する必要があります。 Azure MFA Server は RADIUS クライアントから要求を受け取り、認証ターゲットに対して資格情報を検証し、Azure Multi-Factor Authentication を追加して、RADIUS クライアントに応答を返します。 認証要求が成功するのは、プライマリ認証と Azure Multi-Factor Authentication の両方が成功した場合だけです。

> [!IMPORTANT]
> 2019 年 7 月 1 日より、Microsoft では新しいデプロイに対して MFA Server が提供されなくなります。 ユーザーからの多要素認証が必要な新しいお客様は、クラウドベースの Azure Multi-Factor Authentication を使用していただく必要があります。 7 月 1 日より前に MFA Server をアクティブ化した既存のお客様は、最新バージョンの今後の更新プログラムをダウンロードし、アクティブ化資格情報を通常どおり生成することができます。

> [!NOTE]
> MFA サーバーは、PAP (パスワード認証プロトコル) と、RADIUS サーバーとして機能するときの MSCHAPv2 (Microsoft チャレンジ ハンドシェイク認証プロトコル ) RADIUS プロトコルのみをサポートします。  EAP (拡張認証プロトコル) などの他のプロトコルは、そのプロトコルをサポートする別の RADIUS サーバーの RADIUS プロキシとして MFA サーバーが機能する場合に使用できます。
>
> この構成では、MFA Server は他のプロトコルを使用して正常な RADIUS チャレンジ応答を開始できないため、一方向の SMS および OATH トークンは機能しません。

![MFA Server での Radius 認証](./media/howto-mfaserver-dir-radius/radius.png)

## <a name="add-a-radius-client"></a>RADIUS クライアントの追加

RADIUS 認証を構成するには、Azure Multi-Factor Authentication Server を Windows サーバーにインストールします。 Active Directory 環境を使用している場合は、サーバーをネットワーク内のドメインに参加させる必要があります。 次の手順に従って Azure Multi-Factor Authentication Server を構成します。

1. Azure Multi-Factor Authentication Server で、左側のメニューの [RADIUS 認証] アイコンをクリックします。
2. **[RADIUS 認証を有効にする]** チェック ボックスをオンにします。
3. Azure MFA RADIUS サービスが標準以外のポートで RADIUS 要求をリッスンする必要がある場合は、[クライアント] タブで認証ポートとアカウンティング ポートを変更します。
4. **[追加]** をクリックします。
5. Azure Multi-Factor Authentication Server に対して認証するアプライアンス/サーバーの IP アドレス、アプリケーション名 (省略可能)、共有シークレットを入力します。

   アプリケーション名はレポートに表示され、SMS またはモバイル アプリの認証メッセージにも表示される場合があります。

   共有シークレットは、Azure Multi-Factor Authentication Server とアプライアンス/サーバーの両方で同じである必要があります。

6. すべてのユーザーを Server にインポート済みであり、多要素認証の対象となる場合は、 **[Multi-Factor Authentication のユーザー照合が必要]** チェック ボックスをオンにします。 多数のユーザーがまだサーバーにインポートされていない、または 2 段階認証から除外される場合、ボックスはオフのままにします。
7. バックアップ手段として、モバイル認証アプリの OATH パスコードを使用する場合は、 **[代替の OATH トークンを有効にする]** チェック ボックスをオンにします。
8. Click **OK**.

手順 4. ～ 8. を繰り返して、RADIUS クライアントを必要な数だけ追加します。

## <a name="configure-your-radius-client"></a>RADIUS クライアントの構成

1. **[ターゲット]** タブをクリックします。
   * Azure MFA Server を Active Directory 環境のドメインに参加しているサーバーにインストールする場合は、 **[Windows ドメイン]** を選択します。
   * ユーザーを LDAP ディレクトリに対して認証する必要がある場合は、 **[LDAP バインド]** を選択します。
      [ディレクトリの統合] アイコンを選択し、[設定] タブで LDAP 構成を編集して、Server を指定のディレクトリにバインドできるようにします。 LDAP を構成する手順については、[LDAP プロキシの構成ガイド](howto-mfaserver-dir-ldap.md)をご覧ください。
   * 別の RADIUS サーバーに対してユーザーを認証する必要がある場合は、 **[RADIUS サーバー]** を選択します。
1. **[追加]** をクリックして、Azure MFA Server が RADIUS 要求をプロキシするサーバーを構成します。
1. [RADIUS サーバーの追加] ダイアログ ボックスで、RADIUS サーバーの IP アドレスと共有シークレットを入力します。

   共有シークレットは、Azure Multi-Factor Authentication Server と RADIUS サーバーの両方で同じである必要があります。 RADIUS サーバーで異なるポートが使用されている場合は、認証ポートとアカウンティング ポートを変更します。

1. Click **OK**.
1. Azure MFA Server から受け取ったアクセス要求を処理できるように、Azure MFA Server を RADIUS クライアントとして他の RADIUS サーバーに追加します。 Azure Multi-Factor Authentication Server で構成されている同じ共有シークレットを使用します。

さらに別の RADIUS サーバーを追加するには、上記の手順を繰り返します。 **[上へ移動]** ボタンと **[下へ移動]** ボタンを使用して Azure MFA Server が呼び出すサーバーの順序を構成します。

Azure Multi-Factor Authentication Server の構成は以上です。 Server が構成されたポートを使用して、構成されたクライアントからの RADIUS アクセス要求をリッスンするようになります。

## <a name="radius-client-configuration"></a>RADIUS クライアントの構成

RADIUS クライアントを構成するには、ガイドラインに従います。

* RADIUS 経由で Azure Multi-Factor Authentication Server の IP アドレスに対して認証するように機器/サーバーを構成します。これにより、RADIUS サーバーとして機能します。
* 以前に構成した同じ共有シークレットを使用します。
* ユーザーの資格情報を検証して、2 段階認証を実行し、応答を受信した後、RADIUS アクセス要求に応答する時間を確保するために、RADIUS のタイムアウトを 30 ～ 60 秒に構成します。

## <a name="next-steps"></a>次の手順

Azure Multi-Factor Authentication がクラウドにある場合は、[RADIUS 認証と統合](howto-mfa-nps-extension.md)する方法を参照してください。 
