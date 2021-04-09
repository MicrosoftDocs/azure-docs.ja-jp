---
title: ユーザー VPN 接続用の Azure AD テナント:Azure AD 認証
description: Azure Virtual WAN ユーザー VPN (ポイント対サイト) で、Azure AD 認証を使用して VNet に接続することができます
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: a54397fe7cfecef6813105645b7f2b218894875e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91367854"
---
# <a name="prepare-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>ユーザー VPN OpenVPN プロトコル接続用の Azure Active Directory テナントを準備する

IKEv2 プロトコルを介して仮想ハブに接続すると、証明書ベースの認証または RADIUS 認証を使用することができます。 ただし、OpenVPN プロトコルを使用する場合は、Azure Active Directory 認証を使用することもできます。 この記事では、OpenVPN 認証を使用して仮想 WAN ユーザー VPN (ポイント対サイト) 用の Azure AD テナントを設定する方法について説明します。

> [!NOTE]
> Azure AD 認証は、OpenVPN&reg; プロトコル接続でのみサポートされています。
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1.Azure AD テナントを作成する

Azure AD テナントがあることを確認します。 Azure AD テナントがない場合、[新しいテナントの作成](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)に関する記事の手順に従って作成できます。

* 組織名
* 初期ドメイン名

例:

   ![新しい Azure AD テナント](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2.Azure AD テナント ユーザーを作成する

次に、新しく作成した Azure AD テナントに、1 つの全体管理者アカウントと 1 つのユーザー アカウントの 2 つのユーザー アカウントを作成します。 ユーザー アカウントを使用して OpenVPN 認証をテストし、全体管理者アカウントを使用して Azure VPN アプリの登録に同意することができます。 Azure AD ユーザー アカウントを作成したら、管理アクセス許可を委任するために、**ディレクトリ ロール** をユーザーに割り当てます。

[この記事](../active-directory/fundamentals/add-users-azure-active-directory.md)の手順を使用して、Azure AD テナントに対して 2 人のユーザーを作成します。 作成されたアカウントのいずれかの **ディレクトリ ロール** を必ず **全体管理者** に変更してください。

## <a name="3-grant-consent-to-the-azure-vpn-app-registration"></a><a name="enable-authentication"></a>3.Azure VPN アプリの登録に同意する

1. **全体管理者** ロールを割り当てられたユーザーとして、Azure portal にサインインします。

2. 次に、組織の管理者の同意を付与します。これにより、Azure VPN アプリケーションでサインインしてユーザー プロファイルを読み取ることができるようになります。 デプロイの場所に関連する URL をコピーし、ブラウザーのアドレス バーに貼り付けます。

    パブリック

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

3. メッセージが表示されたら、**全体管理者** アカウントを選択します。

    ![ディレクトリ ID](./media/openvpn-create-azure-ad-tenant/pick.png)

4. メッセージが表示されたら、 **[同意する]** を選択します。

    ![[Permissions requested Accept for your organization] (アクセス許可が要求されています。組織のために同意してください) というメッセージと、その他の情報が表示されているダイアログ ボックスを示すスクリーンショット。](./media/openvpn-create-azure-ad-tenant/accept.jpg)

5. Azure AD の **[エンタープライズ アプリケーション]** に、**Azure VPN** が表示されるようになります。

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

## <a name="next-steps"></a>次のステップ

Azure AD 認証を使用して仮想ネットワークに接続するには、ユーザー VPN 構成を作成して仮想ハブに関連付ける必要があります。 「[Azure へのポイント対サイト接続に Azure AD 認証を構成する](virtual-wan-point-to-site-azure-ad.md)」を参照してください。
