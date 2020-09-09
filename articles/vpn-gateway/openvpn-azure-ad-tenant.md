---
title: 'VPN Gateway: P2S VPN 接続用の Azure AD テナント:Azure AD 認証'
description: P2S VPN を使用して VNet に接続する場合には、Azure AD 認証を使用できます
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/17/2020
ms.author: alzam
ms.openlocfilehash: 74999b2bf1a34e3c7b8190dd04206b2b541c465f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087035"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>P2S OpenVPN プロトコル接続用の Azure Active Directory テナントを作成する

VNet に接続する際には、証明書ベースの認証か、 RADIUS 認証を使用できます。 ただし、Open VPN プロトコルを使用する場合は、Azure Active Directory 認証を使用することもできます。 この記事では、P2S Open VPN 認証用の Azure AD テナントを設定する方法について説明します。

> [!NOTE]
> Azure AD 認証は、OpenVPN® プロトコル接続でのみサポートされています。
>


## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1.Azure AD テナントを確認する

Azure AD テナントがあることを確認します。 Azure AD テナントがない場合、[新しいテナントの作成](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)に関する記事の手順に従って作成できます。

* 組織名
* 初期ドメイン名

例:

   ![新しい Azure AD テナント](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2.Azure AD テナント ユーザーを作成する

Azure AD テナントには、全体管理者アカウントとマスター ユーザー アカウントが必要です。 マスター ユーザー アカウントは、マスター埋め込みアカウント (サービス アカウント) として使用されます。 Azure AD テナント ユーザー アカウントを作成する際には、作成するユーザーの種類に応じてディレクトリ ロールを調整します。

[この記事](../active-directory/fundamentals/add-users-azure-active-directory.md)の手順を使用して、Azure AD テナントに対して少なくとも 2 人のユーザーを作成します。 作成するアカウントの種類 (下記) に応じて、**ディレクトリ ロール**を変更してください。

* 全体管理者
* User

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3.VPN ゲートウェイでの Azure AD 認証を有効にする

1. 認証に使用するディレクトリのディレクトリ ID を特定します。 これは、[Active Directory] ページの [プロパティ] セクションに表示されます。

    ![ディレクトリ ID](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. ディレクトリ ID をコピーします。

3. **全体管理者**ロールを割り当てられたユーザーとして、Azure portal にサインインします。

4. 次に、管理者の同意を与えます。 デプロイの場所に関連する URL をコピーし、ブラウザーのアドレス バーに貼り付けます。

    パブリック

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Germany

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. メッセージが表示されたら、**全体管理者**アカウントを選択します。

    ![ディレクトリ ID](./media/openvpn-create-azure-ad-tenant/pick.png)

6. メッセージが表示されたら、 **[同意する]** を選択します。

    ![Accept](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Azure AD の **[エンタープライズ アプリケーション]** に、**Azure VPN** が表示されます。

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. 機能しているポイント対サイト環境がまだない場合は、指示に従って作成します。 ポイント対サイト VPN ゲートウェイを作成して構成する方法については、[ポイント対サイト VPN の作成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)に関する記事を参照してください。 

    > [!IMPORTANT]
    > Basic SKU は OpenVPN ではサポートされていません。

9. VPN ゲートウェイで Azure AD 認証を有効にするには、 **[ポイント対サイトの構成]** に移動し、 **[トンネルの種類]** として **[OpenVPN (SSL)]** を選択します。 **[認証の種類]** として **[Azure Active Directory]** を選択し、 **[Azure Active Directory]** セクションに情報を入力します。

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png)


   > [!NOTE]
   > `AadIssuerUri` 値の末尾にスラッシュが含まれていることを確認してください。 それ以外の場合は、接続に失敗する可能性があります。

10. **[VPN クライアントのダウンロード]** リンクをクリックして、プロファイルを作成してダウンロードします。

11. ダウンロードした zip ファイルを解凍します。

12. 解凍された "AzureVPN" フォルダーを参照します。

13. "azurevpnconfig.xml" ファイルの場所をメモしておきます。 azurevpnconfig.xml には VPN 接続の設定が含まれています。このファイルは Azure VPN クライアント アプリケーションに直接インポートできます。 このファイルは、接続する必要があるすべてのユーザーに、電子メールやその他の方法で配布することもできます。 ユーザーが正常に接続するには、有効な Azure AD 資格情報が必要になります。

## <a name="next-steps"></a>次のステップ

仮想ネットワークに接続するには、VPN クライアント プロファイルを作成し、構成する必要があります。 「[VPN クライアントを P2S VPN 接続用に構成する](openvpn-azure-ad-client.md)」を参照してください。
