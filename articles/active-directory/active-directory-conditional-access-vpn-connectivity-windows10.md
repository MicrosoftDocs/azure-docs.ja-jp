---
title: "仮想プライベート ネットワーク接続用の Azure Active Directory の条件付きアクセス(プレビュー) | Microsoft Docs"
description: "仮想プライベート ネットワーク接続用の Azure Active Directory の条件付きアクセスについて説明します。 "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 540d8974ee2c02f80bccf28764b4d0d243e98d85
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-conditional-access-for-virtual-private-network-connectivity-preview"></a>仮想プライベート ネットワーク接続用の Azure Active Directory の条件付きアクセス(プレビュー)

[Azure Active Directory (Azure AD) の条件付きアクセス](active-directory-conditional-access-azure-portal.md)によって、許可されたユーザーがリソースにアクセスする方法を微調整できます。 仮想プライベート ネットワーク (VPN) 接続用の Azure AD 条件付きアクセスでは、条件付きアクセスを使用して、VPN 接続を保護できます。


VPN 接続用の Azure AD 条件付きアクセスを構成するには、次の手順を完了する必要があります。 

1.  VPN サーバーを構成する
2.  VPN クライアントを構成します 
3.  条件付きアクセス ポリシーを構成する
4.  確認


## <a name="before-you-begin"></a>開始する前に

このトピックは、次のトピックを熟知していることを前提としています。

- [Azure Active Directory の条件付きアクセス](active-directory-conditional-access-azure-portal.md)
- [VPN と条件付きアクセス](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)

「[Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile)」(Windows 10 での自動 VPN プロファイルを使用したリモート アクセスの強化) を参照して、Microsoft がこの機能をどのように実装しているかの詳細を知ることができます。   


## <a name="prerequisites"></a>前提条件

仮想プライベート ネットワーク接続用の Azure Active Directory 条件付きアクセスを構成するには、VPN サーバーを構成する必要があります。 



## <a name="step-1---configure-your-vpn-server"></a>手順 1 - VPN サーバーを構成する 

この手順の目的は、Azure AD での VPN 認証用のルート証明書を構成することです。 仮想プライベート ネットワーク接続用の条件付きアクセスを構成するには、以下の操作を行う必要があります。

1. Azure ポータルで VPN 証明書を作成する
2. VPN 証明書をダウンロードする
2. 証明書を VPN サーバーにデプロイする

VPN 証明書は、VPN 接続用に Azure AD で認証するときに、Windows 10 クライアントに対して発行された証明書に署名するために Azure AD が使用する発行者です。 Windows 10 クライアントが要求するトークンが、アプリケーション (この場合は VPN サーバー) に提示される証明書であると仮定します。

![条件付きアクセス](./media/active-directory-conditional-access-vpn-connectivity-windows10/06.png)

Azure ポータルで、2 つの証明書を作成して、証明書の有効期限が近づいているときの移行を管理できます。 証明書を作成するときに、証明書がプライマリ証明書であるかどうかを選択できます。 プライマリ証明書は、認証中に接続用の証明書に署名するために実際に使用される証明書です。


**VPN 証明書を作成するには:**

1. [Azure ポータル](https://portal.azure.com)にグローバル管理者としてサインオンします。

2. 左側のナビゲーション バーで、**[Azure Active Directory]** をクリックします。 

    ![VPN 接続](./media/active-directory-conditional-access-vpn-connectivity-windows10/01.png)

3. **[Azure Active Directory]** ページの **[管理]** セクションで、**[条件付きアクセス]** をクリックします。

    ![VPN 接続](./media/active-directory-conditional-access-azure-portal-get-started/02.png)

4. **[条件付きアクセス]** ページの **[管理]** セクションで、**[VPN 接続 (プレビュー)]** をクリックします。

    ![VPN 接続](./media/active-directory-conditional-access-vpn-connectivity-windows10/03.png)

5. **[VPN 接続]** ページで、**[新しい証明書]** をクリックします。

    ![VPN 接続](./media/active-directory-conditional-access-vpn-connectivity-windows10/04.png)

6. **[新規]** ページで、次の手順を実行します。

    ![VPN 接続](./media/active-directory-conditional-access-vpn-connectivity-windows10/05.png)

    a. **[期間]** として、**[1 年]** を選択します。

    b. **[プライマリ]** として、**[はい]** を選択します。

    c. **Create** をクリックしてください。

7. [VPN 接続] ページで、**[証明書のダウンロード]** をクリックします。


この時点で、新しく作成した証明書を VPN サーバーにデプロイする準備が整いました。 VPN サーバーで、ダウンロードした証明書を *VPN 認証用の信頼されたルート CA* として追加する必要があります。

Windows の RRAS ベースのデプロイでは、次のコマンドを実行して、NPS サーバーで "*Enterprise NTauth*" ストアにルート証明書を追加する必要があります。

1. `certutil -dspublish <CACERT> RootCA`
2. `certutil -dspublish <CACERT> NtAuthCA`



## <a name="step-2---configure-your-vpn-client"></a>手順 2 - VPN クライアントを構成する 

この手順では、[VPN と条件付きアクセス](https://docs.microsoft.com/windows/access-protection/vpn/vpn-conditional-access)の説明に従って、VPN クライアントの接続プロファイルを構成する必要があります。


## <a name="step-3---configure-your-conditional-access-policy"></a>手順 3 - 条件付きアクセス ポリシーを構成する

このセクションでは、VPN 接続用の条件付きアクセス ポリシーを構成するための手順を説明します。

**条件付きアクセス ポリシーを構成するには** 

1. **[条件付きアクセス]** ページで、ツール バーの上部の **[追加]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-vpn-connectivity-windows10/07.png)

2. **[新規]** ページの **[名前]** ボックスに、ポリシーの名前 (例: **VPN policy**) を入力します。

    ![条件付きアクセス](./media/active-directory-conditional-access-vpn-connectivity-windows10/08.png)

5. **[割り当て]** セクションで **[ユーザーとグループ]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-vpn-connectivity-windows10/09.png)

6. **[ユーザーとグループ]** ページで、次の手順を実行します。

    ![条件付きアクセス](./media/active-directory-conditional-access-vpn-connectivity-windows10/10.png)

    a. **[ユーザーとグループの選択]** をクリックします。

    b. **[選択]**をクリックします。

    c. **[選択]** ページで、テスト ユーザーを選択し、**[選択]** をクリックします。

    d. **[ユーザーとグループ]** ページで、**[完了]** をクリックします。

7. **[新規]** ページで、次の手順を実行します。

    ![条件付きアクセス](./media/active-directory-conditional-access-vpn-connectivity-windows10/11.png)

    a. **[割り当て]** セクションで、**[クラウド アプリ]** をクリックします。

    b. **[クラウド アプリ]** ページで、**[アプリを選択]** をクリックし、**[選択]** をクリックします。

    c. **[選択]** ページで、**[アプリケーション]** ボックスに「**vpn**」と入力します。

    d. **[VPN サーバー]**を選択します。

    e. **[選択]**をクリックします。


13. **[新規]** ページで、**[許可]** ページを開くために、**[コントロール]** セクションで **[許可]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. **[許可]** ページで、次の手順を実行します。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. **[多要素認証が必要です]** を選択します。

    b. **[選択]**をクリックします。

15. **[新規]** ページで、**[ポリシーの有効化]** の下の **[オン]** をクリックします。

    ![条件付きアクセス](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. **[新規]** ページで **[作成]** をクリックします。



## <a name="next-steps"></a>次のステップ

Microsoft がこの機能をどのように実装しているかの詳細を知るには、「[Enhancing remote access in Windows 10 with an automatic VPN profile](https://www.microsoft.com/itshowcase/Article/Content/894/Enhancing-remote-access-in-Windows-10-with-an-automatic-VPN-profile)」(Windows 10 での自動 VPN プロファイルを使用したリモート アクセスの強化) を参照します。    

