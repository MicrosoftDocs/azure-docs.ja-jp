---
title: 'Azure Active Directory Domain Services: Windows Server VM をマネージド ドメインに参加させる | Microsoft Docs'
description: Windows Server 仮想マシンの Azure AD DS への参加
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: ergreenl
ms.openlocfilehash: f18d3409d53123733d7f423c77864cd0739765eb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57900592"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Windows Server 仮想マシンのマネージド ドメインへの参加
この記事では、Azure Portal を使用して Windows Server 仮想マシンをデプロイする方法を示します。 その後、仮想マシンを Azure Active Directory Domain Services (Azure AD DS) のマネージド ドメインに参加させる方法を示します。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="step-1-create-a-windows-server-virtual-machine"></a>ステップ 1: Windows Server 仮想マシンを作成する
Azure AD DS を有効にした仮想ネットワークに参加する Windows 仮想マシンを作成するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のウィンドウの上部にある **[新規]** を選択します。
3. **[コンピューティング]**、**[Windows Server 2016 Datacenter]** の順に選択します。

    ![Windows Server 2016 Datacenter のリンク](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. ウィザードの **[基本]** ウィンドウで、仮想マシンの基本的な設定を構成します。

    ![[基本] ウィンドウ](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > ここで入力するユーザー名とパスワードは、仮想マシンにログオンするために使用するローカルの Administrator アカウントです。 仮想マシンをパスワードのブルート フォース攻撃から保護するために、強力なパスワードを選択してください。 ここではドメイン ユーザー アカウントの資格情報を入力しないでください。
    >

5. 仮想マシンの **[サイズ]** を選択します。 その他のサイズも表示するには、**[すべて表示]** を選択するか、**[サポートされるディスクの種類]** フィルターを変更します。

    ![[サイズの選択] ウィンドウ](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. **[設定]** ウィンドウで、Azure AD DS 管理対象ドメインがデプロイされている仮想ネットワークを選択します。 マネージド ドメインのデプロイ先とは別のサブネットを選択します。 その他の設定は既定値のままにして、**[OK]** を選択します。

    ![仮想マシンの仮想ネットワークの設定](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **適切な仮想ネットワークとサブネットを選択してください。**
    >
    > マネージド ドメインがデプロイされている仮想ネットワークか、仮想ネットワーク ピアリングを使用してマネージド ドメインに接続されている仮想ネットワークを選択します。 接続されていない仮想ネットワークを選択した場合、仮想マシンをマネージド ドメインに参加させることができなくなります。
    >
    > マネージド ドメインは専用サブネットにデプロイすることをお勧めします。 そのため、マネージド ドメインを有効にしたサブネットは選択しないでください。

7. その他の設定は既定値のままにして、**[OK]** を選択します。
8. **[購入]** ページで設定を確認し、**[OK]** を選択して仮想マシンをデプロイします。
9. VM のデプロイは、Azure ポータルのダッシュボードにピン留めされます。

    ![完了](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
10. デプロイが完了したら、**[概要]** ページで VM に関する情報を表示できます。


## <a name="step-2-connect-to-the-windows-server-virtual-machine-by-using-the-local-administrator-account"></a>ステップ 2: ローカル管理者アカウントを使用して Windows Server 仮想マシンに接続する
次に、新たに作成された Windows Server 仮想マシンをドメインに参加させるため、その仮想マシンに接続します。 仮想マシンの作成時に指定したローカル管理者の資格情報を使用します。

仮想マシンに接続するには、次の手順を実行します。

1. **[概要]** ウィンドウで、**[接続]** を選択します。  
    リモート デスクトップ プロトコル (.rdp) ファイルが作成されてダウンロードされます。

    ![Windows 仮想マシンに接続する](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. VM に接続するには、ダウンロードした RDP ファイルを開きます。 メッセージが表示されたら、**[Connect]** を選択します。
3. 仮想マシンを作成したときに指定した**ローカル管理者の資格情報** (たとえば、*localhost\mahesh*) を入力します。
4. サインイン プロセス中に証明書警告が表示された場合、**[はい]** または **[続行]** を選択して接続します。

この時点で、ローカル管理者の資格情報を使用して、新しく作成した Windows 仮想マシンにログオンしている必要があります。 次のステップでは、仮想マシンをドメインに参加させます。


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-azure-ad-ds-managed-domain"></a>ステップ 3: Windows Server 仮想マシンを Azure AD DS マネージド ドメインに参加させる
Windows Server 仮想マシンを Azure AD DS のマネージド ドメインに参加させるには、次の手順を実行します。

1. ステップ 2 で示したように、Windows Server VM に接続します。 **スタート**画面で、**サーバー マネージャー**を開きます。
2. **[サーバー マネージャー]** ウィンドウの左側のウィンドウで、**[ローカル サーバー]** を選択します。

    ![仮想マシンの [サーバー マネージャー] ウィンドウ](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. **[プロパティ]** で **[ワークグループ]** を選択します。
4. **[システム プロパティ]** ウィンドウで **[変更]** を選択し、ドメインに参加します。

    ![[システム プロパティ] ウィンドウ](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

5. **[ドメイン]** ボックスで、Azure AD DS の管理対象ドメインの名前を指定し、**[OK]** を選択します。

    ![参加するドメインの指定](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

6. ドメインに参加するために、資格情報を入力するように求められます。 "*Azure AD DC 管理者グループに属しているユーザー*" の資格情報を使用します。 このグループのメンバーだけが、マシンをマネージド ドメインに参加させる権限を有します。

    ![資格情報を指定するための [Windows セキュリティ] ウィンドウ](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

7. 次のいずれかの方法で、資格情報を指定できます。

   * **UPN 形式**: (推奨) Azure AD で構成されているユーザー アカウントのユーザー プリンシパル名 (UPN) サフィックスを指定します。 この例では、ユーザー *bob* の UPN サフィックスは、*bob\@domainservicespreview.onmicrosoft.com* です。

   * **SAMAccountName 形式**: SAMAccountName 形式でアカウント名を指定できます。 この例では、ユーザー *bob* は、「*CONTOSO100\bob*」と入力する必要があります。

     > [!TIP]
     > **UPN 形式を使用して資格情報を指定することをお勧めします。**
     >
     > ユーザーの UPN プレフィックスが最大文字数を超えていると (例: *joehasareallylongname*)、SAMAccountName が自動生成される場合があります。 Azure AD テナントで複数のユーザーが同じ UPN プレフィックス (例: *bob*) を使用していると、サービスによって SAMAccountName 形式が自動生成される場合があります。 このような場合は、UPN 形式を使用してドメインに確実にログインすることができます。
     >

8. ドメインに正常に参加できると、ドメインへの歓迎を示す次のメッセージが表示されます。

    ![ドメインへようこそ](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

9. ドメインへの参加操作を完了するには、仮想マシンを再起動します。

## <a name="troubleshoot-joining-a-domain"></a>ドメインへの参加のトラブルシューティング
### <a name="connectivity-issues"></a>接続に関する問題
仮想マシンでドメインを検索できない場合は、次のトラブルシューティング手順をお試しください。

* Azure AD DS を有効にしているのと同じ仮想ネットワークに仮想マシンが接続されていることを確認します。 接続されていない場合、仮想マシンはドメインに接続することも参加することもできません。

* 仮想マシンが、Azure AD DS を有効にした仮想ネットワークに接続されている仮想ネットワーク上に存在していることを確認します。

* マネージド ドメインの DNS ドメイン名でドメインの ping をお試しください (例: "*ping contoso100.com*")。 これを実行できない場合は、Azure AD DS を有効にしたページに表示されているドメインの IP アドレスに対して ping を試行してください (例: *ping 10.0.0.4*)。 ドメインではなく IP アドレスを ping できた場合、DNS が正しく構成されていない可能性があります。 ドメインの IP アドレスが仮想ネットワークの DNS サーバーとして構成されているかどうかを確認します。

* 仮想マシンの DNS リゾルバー キャッシュのフラッシュを試行します (*ipconfig/flushdns*)。

ドメインに参加するための資格情報の入力を求めるウィンドウが表示される場合、接続の問題はありません。

### <a name="credentials-related-issues"></a>資格情報に関連した問題
資格情報で問題があり、ドメインに参加できない場合は、次のトラブルシューティング手順をお試しください。

* UPN 形式を使用して資格情報を指定します。 たくさんのユーザーがテナントで同じ UPN プレフィックスを使用しているか、UPN プレフィックスが最大文字数を超えている場合、アカウントの SAMAccountName は自動生成される可能性があります。 そのような場合、アカウントの SAMAccountName 形式が想定した形式やオンプレミス ドメインで使用する形式と異なる可能性があります。

* "*AAD DC 管理者*" グループに属しているユーザー アカウントの資格情報を使用します。

* マネージド ドメインとの[パスワード同期を有効にしている](active-directory-ds-getting-started-password-sync.md)ことを確認します。

* Azure AD 内で構成されているユーザーの UPN (例: *bob\@domainservicespreview.onmicrosoft.com*) をサインインに使用したことを確認します。

* ファースト ステップ ガイドで指定されているとおり、パスワード同期が完了するまで十分な時間待機します。

## <a name="related-content"></a>関連コンテンツ
* [Azure AD DS ファースト ステップ ガイド](active-directory-ds-getting-started.md)
* [Azure AD DS の管理対象ドメインの管理](active-directory-ds-admin-guide-administer-domain.md)
