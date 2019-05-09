---
title: マネージド ドメイン用のハイブリッド Azure Active Directory 参加の構成 | Microsoft Docs
description: マネージド ドメイン用のハイブリッド Azure Active Directory 参加を構成する方法について説明します。
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: joflore
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81a9726b73226cd940a55e316ae434aeaad6ff4d
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519087"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>チュートリアル:マネージド ドメイン用のハイブリッド Azure Active Directory 参加の構成

ユーザーと同じく、デバイスは、保護の対象であると同時に、時と場所を選ばずにリソースを保護するために使用したいもう 1 つの ID になりつつあります。 この目標は、次のいずれかの方法を使用してデバイスの ID を Azure AD に設定することで達成できます。

- Azure AD 参加
- ハイブリッド Azure AD 参加
- Azure AD の登録

Azure AD にデバイスを設定して、クラウドとオンプレミスのリソースでのシングル サインオン (SSO) を実現することで、ユーザーの生産性を最大化できます。 同時に、[条件付きアクセス](../active-directory-conditional-access-azure-portal.md)を使用して、クラウドとオンプレミスのリソースへのアクセスを保護することもできます。

このチュートリアルでは、マネージド ドメイン内のデバイスのハイブリッド Azure AD 参加を構成する方法を学習します。

> [!div class="checklist"]
> * ハイブリッド Azure AD 参加の構成
> * ダウンレベルの Windows デバイスの有効化
> * 参加済みデバイスの確認 
> * トラブルシューティング 


## <a name="prerequisites"></a>前提条件

このチュートリアルは、次の事項を熟知していることを前提としています。
    
-  [Azure Active Directory のデバイス管理の概要](../device-management-introduction.md)
    
-  [ハイブリッド Azure Active Directory Join の実装を計画する方法](hybrid-azuread-join-plan.md)

-  [デバイスのハイブリッド Azure AD Join を制御する方法](hybrid-azuread-join-control.md)
  

この記事のシナリオを構成するための要件を次に示します。

- [最新バージョンの Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 以降) がインストールされていること。 

Azure AD Connect が、Azure AD に参加するハイブリッド Azure AD にするデバイスのコンピューター オブジェクトを同期済みであることを確認します。 コンピューター オブジェクトが特定の組織単位 (OU) に属している場合、これらの OU を Azure AD Connect についても構成する必要があります。

バージョン 1.1.819.0 以降の Azure AD Connect には、ハイブリッド Azure AD 参加を構成するためのウィザードが用意されています。 このウィザードを使用すると、構成プロセスを大幅に簡略化できます。 関連するウィザードは、デバイス登録のためのサービス接続ポイント (SCP) を構成します。

この記事の構成手順は、このウィザードに基づいています。 

ハイブリッド Azure AD 参加を使用するには、デバイスが組織のネットワーク内から次の Microsoft リソースにアクセスできる必要があります。  

- https://enterpriseregistration.windows.net
- https://login.microsoftonline.com
- https://device.login.microsoftonline.com
- [https://autologon.microsoftazuread-sso.com](https://autologon.microsoftazuread-sso.com) (シームレス SSO を使用している場合、または使用する予定の場合)

Windows 10 1709 以降では、組織が送信プロキシ経由でインターネットにアクセスする必要がある場合に、[グループ ポリシー オブジェクト (GPO) を使用してコンピューターのプロキシ設定を構成できます](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)。 お使いのコンピューターで Windows 10 1709 より古いバージョンが実行されている場合は、Web Proxy Auto-Discovery (WPAD) を実装して、Windows 10 コンピューターが Azure AD にデバイスを登録できるようにする必要があります。 

組織が認証された送信プロキシ経由でインターネットにアクセスする必要がある場合は、Windows 10 コンピューターが送信プロキシに対して正常に認証されることを確認する必要があります。 Windows 10 コンピューターではマシン コンテキストを使用してデバイス登録が実行されるため、マシン コンテキストを使用して送信プロキシ認証を構成する必要があります。 構成要件については、送信プロキシ プロバイダーに確認してください。 



## <a name="configure-hybrid-azure-ad-join"></a>ハイブリッド Azure AD 参加の構成

Azure AD Connect を使用してハイブリッド Azure AD 参加を構成するには、次のものが必要です。

- Azure AD テナントの全体管理者の資格情報。  

- 各フォレストのエンタープライズ管理者の資格情報。


**Azure AD Connect を使用してハイブリッド Azure AD 参加を構成するには:**

1. Azure AD Connect を起動し、**[構成]** をクリックします。

    ![ようこそ](./media/hybrid-azuread-join-managed-domains/11.png)

2. **[追加のタスク]** ページで、**[デバイス オプションの構成]** を選択し、**[次へ]** をクリックします。 

    ![追加のタスク](./media/hybrid-azuread-join-managed-domains/12.png)

3. **[概要]** ページで、**[次へ]** をクリックします。 

    ![概要](./media/hybrid-azuread-join-managed-domains/13.png)

4. **[Azure AD に接続]** ページで、Azure AD テナントの全体管理者の資格情報を入力します。  

    ![Azure への接続](./media/hybrid-azuread-join-managed-domains/14.png)

5. **[デバイス オプション]** ページで、**[ハイブリッド Azure AD 参加の構成]** を選択し、**[次へ]** をクリックします。 

    ![デバイス オプション](./media/hybrid-azuread-join-managed-domains/15.png)

6. **[SCP]** ページで、Azure AD Connect で SCP を構成するフォレストごとに次の手順を実行し、**[次へ]** をクリックします。 

    ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

    a. フォレストを選択します。

    b. 認証サービスを選択します。

    c. **[追加]** をクリックして、エンタープライズ管理者の資格情報を入力します。


7. **[デバイスのオペレーティング システム]** ページで、Active Directory 環境内のデバイスで使用されているオペレーティング システムを選択し、**[次へ]** をクリックします。 

    ![デバイスのオペレーティング システム](./media/hybrid-azuread-join-managed-domains/17.png)


8. **[構成の準備完了]** ページで、**[構成]** をクリックします。 

    ![構成の準備完了](./media/hybrid-azuread-join-managed-domains/19.png)

9. **[構成が完了しました]** ページで、**[終了]** をクリックします。 

    ![構成の完了](./media/hybrid-azuread-join-managed-domains/20.png)




## <a name="enable-windows-down-level-devices"></a>ダウンレベルの Windows デバイスの有効化

ドメイン参加済みデバイスの一部がダウンレベルの Windows デバイスである場合は、以下の操作が必要です。

- デバイス設定の更新
 
- デバイスの登録用のローカル イントラネット設定の構成

- シームレス シングル サインオン (SSO) の構成

- ダウンレベルの Windows デバイスの制御 


### <a name="update-device-settings"></a>デバイス設定の更新 

ダウンレベルの Windows デバイスを登録するには、Azure AD へのデバイスの登録をユーザーに許可するデバイス設定になっている必要があります。 この設定は、Azure portal の次の場所で確認できます。

`Home > [Name of your tenant] > Devices - Device settings`  


    
次のポリシーを **[すべて]** に設定する必要があります。**[ユーザーはデバイスを Azure AD に登録できます]**

![デバイスを登録する](media/hybrid-azuread-join-managed-domains/23.png)



### <a name="configure-the-local-intranet-settings-for-device-registration"></a>デバイスの登録用のローカル イントラネット設定の構成

ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加を正常に完了するため、およびデバイスが Azure AD で認証を受けるときに証明書の指定を求めるメッセージが表示されないようにするために、ドメイン参加済みデバイスにポリシーをプッシュして、以下の URL を Internet Explorer のローカル イントラネット ゾーンに追加することができます。

- `https://device.login.microsoftonline.com`

- `https://autologon.microsoftazuread-sso.com`

さらに、ユーザーのローカル イントラネット ゾーンで **[スクリプトを介したステータス バーの更新を許可する]** を有効にする必要があります。


### <a name="configure-seamless-sso"></a>シームレス SSO の構成

Azure AD クラウド認証方法として Pass-through Authentication (PTA) または Password Hash Sync (PHS) を使用しているマネージド ドメインで、ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加を正常に完了するには、[シームレス SSO を構成](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start#step-2-enable-the-feature)する必要もあります。 


### <a name="control-windows-down-level-devices"></a>ダウンレベルの Windows デバイスの制御 

ダウンレベルの Windows デバイスを登録するには、Windows インストーラー パッケージ (.msi) をダウンロード センターからダウンロードし、インストールする必要があります。 詳細については、[こちら](hybrid-azuread-join-control.md#control-windows-down-level-devices)をクリックしてください。 


## <a name="verify-the-registration"></a>登録の確認

Azure テナントのデバイス登録状態を確認するには、**[Azure Active Directory PowerShell モジュール](/powershell/azure/install-msonlinev1?view=azureadps-2.0)** の **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** コマンドレットを使用できます。

**Get-MSolDevice** コマンドレットを使用してサービスの詳細を確認する場合:

- Windows クライアントの ID と一致する**デバイス ID** を持つオブジェクトが存在する必要があります。
- **DeviceTrustType** の値は **[ドメイン参加済み]** でなければなりません。 これは、Azure AD ポータルの [デバイス] ページの **[ハイブリッド Azure AD 参加済み]** 状態に相当します。
- 条件付きアクセスで使用されるデバイスの **Enabled** の値は **True**、**DeviceTrustLevel**の値は **Managed** でなければなりません。 


**サービスの詳細を確認するには:**

1. **Windows PowerShell** を管理者として開きます。

2. 「`Connect-MsolService`」と入力して Azure テナントに接続します。  

3. 「 `get-msoldevice -deviceId <deviceId>`」と入力します。

6. **[有効]** が **[True]** に設定されていることを確認します。





## <a name="troubleshoot-your-implementation"></a>実装のトラブルシューティング

ドメイン参加済み Windows デバイスのハイブリッド Azure AD 参加を行うときに問題が発生した場合は、次のトピックを参照してください。

- [最新の Windows デバイスのハイブリッド Azure AD 参加のトラブルシューティング](troubleshoot-hybrid-join-windows-current.md)
- [ダウンレベルの Windows デバイスのハイブリッド Azure AD 参加のトラブルシューティング](troubleshoot-hybrid-join-windows-legacy.md)


## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [フェデレーション ドメインのハイブリッド Azure Active Directory 参加を構成する](hybrid-azuread-join-federated-domains.md)
> [ハイブリッド Azure Active Directory 参加を手動で構成する](hybrid-azuread-join-manual.md)

