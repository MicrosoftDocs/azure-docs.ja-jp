---
title: "RADIUS 認証と Azure MFA Server | Microsoft Docs"
description: "RADIUS 認証と Azure Multi-Factor Authentication Server をデプロイする際に役立つ Azure Multi-Factor Authentication のページです。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: f4ba0fb2-2be9-477e-9bea-04c7340c8bce
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/26/2017
ms.author: kgremban
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 20afeb3ba290ddf728d2b52c076c7a57fadc77c6
ms.openlocfilehash: e696b95c9db86b062440f0c4fd788bf97223317a
ms.lasthandoff: 02/28/2017

---
# <a name="integrate-radius-authentication-with-azure-multi-factor-authentication-server"></a>RADIUS 認証と Azure Multi-Factor Authentication Server の統合
Azure MFA Server の [RADIUS 認証] セクションを使用して、RADIUS 認証を有効にし、構成します。 RADIUS は、認証要求を承認してそれらの要求を処理する標準のプロトコルです。 Azure Multi-Factor Authentication Server は RADIUS サーバーとして機能します。 Azure Multi-Factor Authentication を追加するには、RADIUS クライアント (VPN アプライアンス) と認証ターゲット (Active Directory (AD)、LDAP ディレクトリ、または別の RADIUS サーバー) の間に Azure Multi-Factor Authentication Server を挿入します。 Azure Multi-Factor Authentication (MFA) が機能するには、クライアント サーバーと認証ターゲットの両方と通信できるように Azure MFA Server を構成する必要があります。 Azure MFA Server は RADIUS クライアントから要求を受け取り、認証ターゲットに対して資格情報を検証し、Azure Multi-Factor Authentication を追加して、RADIUS クライアントに応答を返します。 認証要求が成功するのは、プライマリ認証と Azure Multi-Factor Authentication の両方が成功した場合だけです。

> [!NOTE]
> MFA サーバーは、PAP (パスワード認証プロトコル) と、RADIUS サーバーとして機能するときの MSCHAPv2 (Microsoft チャレンジ ハンドシェイク認証プロトコル ) RADIUS プロトコルのみをサポートします。  EAP (拡張認証プロトコル) などの他のプロトコルは、そのプロトコルをサポートする別の RADIUS サーバーの RADIUS プロキシとして MFA サーバーが機能する場合に使用できます。
> 
> この構成では、MFA Server は他のプロトコルを使用して正常な RADIUS チャレンジ応答を開始できないため、一方向の SMS および OATH トークンは機能しません。

![RADIUS 認証](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="add-a-radius-client"></a>RADIUS クライアントの追加
RADIUS 認証を構成するには、Azure Multi-Factor Authentication Server を Windows サーバーにインストールします。 Active Directory 環境を使用している場合は、サーバーをネットワーク内のドメインに参加させる必要があります。 次の手順に従って Azure Multi-Factor Authentication Server を構成します。

1. Azure Multi-Factor Authentication Server で、左側のメニューの [RADIUS 認証] アイコンをクリックします。
2. **[RADIUS 認証を有効にする]** チェック ボックスをオンにします。
3. Azure MFA RADIUS サービスが標準以外のポートで RADIUS 要求をリッスンする必要がある場合は、[クライアント] タブで認証ポートとアカウンティング ポートを変更します。
4. **[追加]**をクリックします。
5. Azure Multi-Factor Authentication Server に対して認証するアプライアンス/サーバーの IP アドレス、アプリケーション名 (省略可能)、共有シークレットを入力します。 

  アプリケーション名は Azure Multi-Factor Authentication レポートに表示され、SMS またはモバイル アプリの認証メッセージにも表示される場合があります。

  共有シークレットは、Azure Multi-Factor Authentication Server とアプライアンス/サーバーの両方で同じである必要があります。 

6. すべてのユーザーを Server にインポート済みであるか、インポートする予定であり、多要素認証の対象となる場合は、**[Multi-Factor Authentication のユーザー照合が必要]** チェック ボックスをオンにします。 多数のユーザーがまだサーバーにインポートされていない、または&2; 段階認証から除外される場合、ボックスはオフのままにします。 
7. アウトオブバンドの電話、SMS、またはプッシュ通知に対するフォールバックとして、モバイル認証アプリの OATH パスコードを使用する場合は、**[代替の OATH トークンを有効にする]** チェック ボックスをオンにします。
8. **[OK]**をクリックします。

手順 4. ～ 8. を繰り返して、RADIUS クライアントを必要な数だけ追加します。

## <a name="configure-your-radius-client"></a>RADIUS クライアントの構成

1. **[ターゲット]** タブをクリックします。
2. Azure MFA Server を Active Directory 環境のドメインに参加しているサーバーにインストールする場合は、[Windows ドメイン] を選択します。
3. ユーザーを LDAP ディレクトリに対して認証する必要がある場合は、**[LDAP バインド]** を選択します。 

  LDAP バインドを使用するには、[ディレクトリの統合] アイコンをクリックし、[設定] タブで LDAP 構成を編集して、Server を指定のディレクトリにバインドできるようにします。 LDAP を構成する手順については、[LDAP プロキシの構成ガイド](multi-factor-authentication-get-started-server-ldap.md)をご覧ください。

4. 別の RADIUS サーバーに対してユーザーを認証する必要がある場合は、[RADIUS サーバー] を選択します。
5. **[追加]** をクリックして、Azure MFA Server が RADIUS 要求をプロキシするサーバーを構成します。
6. [RADIUS サーバーの追加] ダイアログ ボックスで、RADIUS サーバーの IP アドレスと共有シークレットを入力します。 

  共有シークレットは、Azure Multi-Factor Authentication Server と RADIUS サーバーの両方で同じである必要があります。 RADIUS サーバーで異なるポートが使用されている場合は、認証ポートとアカウンティング ポートを変更します。

7. **[OK]**をクリックします。
8. Azure MFA Server から受け取ったアクセス要求を処理できるように、Azure MFA Server を RADIUS クライアントとして他の RADIUS サーバーに追加します。 Azure Multi-Factor Authentication Server で構成されている同じ共有シークレットを使用します。

この手順を繰り返して RADIUS サーバーを追加し、**[上へ移動]** ボタンと **[下へ移動]** ボタンを使用して Azure MFA Server が呼び出すサーバーの順序を構成します。 

これで Azure Multi-Factor Authentication Server の構成は完了です。 Server が構成されたポートを使用して、構成されたクライアントからの RADIUS アクセス要求をリッスンするようになります。   

## <a name="radius-client-configuration"></a>RADIUS クライアントの構成
RADIUS クライアントを構成するには、ガイドラインに従います。

* RADIUS 経由で Azure Multi-Factor Authentication Server の IP アドレスに対して認証するように機器/サーバーを構成します。これにより、RADIUS サーバーとして機能します。
* 以前に構成した同じ共有シークレットを使用します。
* ユーザーの資格情報を検証して、2 段階認証を実行し、応答を受信した後、RADIUS アクセス要求に応答する時間を確保するために、RADIUS のタイムアウトを 30 ～ 60 秒に構成します。


