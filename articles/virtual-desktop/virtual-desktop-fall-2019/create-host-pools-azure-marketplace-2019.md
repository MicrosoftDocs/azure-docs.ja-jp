---
title: Windows Virtual Desktop (クラシック) のホスト プール Azure Marketplace - Azure
description: Azure Marketplace を使用して Windows Virtual Desktop (クラシック) のホスト プールを作成する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 04184a658ae1efd03afd3e920818eb5317bc7553
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "88008595"
---
# <a name="tutorial-create-a-host-pool-in-windows-virtual-desktop-classic"></a>チュートリアル:Windows Virtual Desktop (クラシック) でホスト プールを作成する

>[!IMPORTANT]
>この内容は、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Windows Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Windows Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../create-host-pools-azure-marketplace.md)を参照してください。

このチュートリアルでは、Microsoft Azure Marketplace オファリングを使用して Windows Virtual Desktop テナント内にホスト プールを作成する方法を説明します。

ホスト プールは、Windows Virtual Desktop テナント環境内にある 1 つまたは複数の同一の仮想マシンをコレクションとしてまとめたものです。 各ホスト プールには、物理デスクトップの場合と同じようにユーザーが利用できるアプリ グループを含めることができます。

このチュートリアルでは、次のタスクを行います。

> [!div class="checklist"]
>
> * Windows Virtual Desktop でホスト プールを作成する。
> * Azure サブスクリプションで VM を含むリソース グループを作成する。
> * Active Directory ドメインに VM を参加させる。
> * Windows Virtual Desktop に VM を登録する。

## <a name="prerequisites"></a>前提条件

* Virtual Desktop でのテナント。 前の[チュートリアル](tenant-setup-azure-active-directory.md)で、テナントを作成します。
* [Windows Virtual Desktop PowerShell モジュール](/powershell/windows-virtual-desktop/overview/)。

このモジュールの入手後、次のコマンドレットを実行して、自分のアカウントにサインインします。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>新しいホスト プールをプロビジョニングするための Azure Marketplace オファリングを実行する

新しいホスト プールをプロビジョニングするための Azure Marketplace オファリングを実行する方法は次のとおりです。

1. Azure portal メニュー上または **[ホーム]** ページから **[リソースの作成]** を選択します。
1. Marketplace の検索ウィンドウに「**Windows Virtual Desktop**」と入力します。
1. **[Windows Virtual Desktop - Provision a host pool]\(Windows Virtual Desktop - ホスト プールのプロビジョニング\)** を選択し、 **[作成]** を選択します。

その後、次のセクションの手順に従って、適切なタブに情報を入力します。

### <a name="basics"></a>基本

**[基本]** タブで必要な操作は次のとおりです。

1. **サブスクリプション** を選択します。
1. **[リソース グループ]** で **[新規作成]** を選択し、新しいリソース グループの名前を指定します。
1. **リージョン** を選択します。
1. ホスト プールの名前を入力します。入力する名前は、Windows Virtual Desktop テナント内で一意のものを指定してください。
1. **デスクトップの種類** を選択します。 **[個人]** を選択した場合には、このホスト プールに接続するユーザーそれぞれに対して仮想マシンが 1 台、永久的に割り当てられます。
1. Windows Virtual Desktop クライアントにサインインしてデスクトップにアクセスできるユーザーを入力します。 コンマ区切りのリストを使用します。 たとえば、`user1@contoso.com` と `user2@contoso.com` に対してアクセス権を割り当てる場合、「 *`user1@contoso.com,user2@contoso.com`* 」と入力します
1. **[Service metadata location]\(サービス メタデータの場所\)** には、Active Directory サーバーに接続している仮想ネットワークと同じ場所を選択します。

   >[!IMPORTANT]
   >純粋な Azure Active Directory Domain Services (Azure AD DS) と Azure Active Directory (Azure AD) ソリューションを使用している場合は、ドメイン参加および資格情報のエラーを防ぐために、必ず Azure AD DS と同じリージョンにホスト プールをデプロイしてください。

1. **Next: Configure virtual machines\(次へ: 仮想マシンの構成\)** を選択します。

### <a name="configure-virtual-machines"></a>仮想マシンの構成

**[Configure virtual machines]\(仮想マシンの構成\)** タブで必要な操作は次のとおりです。

1. 既定値をそのまま使うか、仮想マシンの数とサイズをカスタマイズします。

    >[!NOTE]
    >お探しの仮想マシン サイズがサイズ セレクターに表示されない場合、まだ Azure Marketplace ツールにオンボードされていないのが原因です。 特定のサイズを要求するには、[Windows Virtual Desktop UserVoice フォーラム](https://windowsvirtualdesktop.uservoice.com/forums/921118-general)で要求を作成するか、既存の要求に賛成票を投じてください。

1. 仮想マシンの名前のプレフィックスを入力します。 たとえば、「*prefix*」と入力すると、仮想マシンの名前は **prefix-0**、**prefix-1** などのようになります。
1. **Virtual machine settings\(仮想マシンの設定\)** を選択します。

### <a name="virtual-machine-settings"></a>仮想マシンの設定

**[Virtual machine settings]\(仮想マシンの設定\)** タブで必要な操作は次のとおりです。

1. **[イメージ ソース]** で、ソースを選択し、その探し方と格納方法に関する適切な情報を入力します。 BLOB ストレージ、マネージド イメージ、およびギャラリーでは、オプションが異なります。

   マネージド ディスクを使用しない場合には、 *.vhd* ファイルが含まれるストレージ アカウントを選択します。
1. ユーザー プリンシパル名とパスワードを入力します。 このアカウントは、Active Directory ドメインに仮想マシンを参加させるドメイン アカウントである必要があります。 このユーザー名とパスワードは、仮想マシン上にローカル アカウントとして作成されます。 このローカル アカウントは、後でリセットできます。

   >[!NOTE]
   > ご使用の仮想マシンを Azure AD DS 環境に参加させる場合は、ドメイン参加ユーザーが [AAD DC Administrators グループ](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)のメンバーであることを確認します。
   >
   > アカウントは、Azure AD DS マネージド ドメインまたは Azure AD テナントの一部でもある必要があります。 Azure AD テナントに関連付けられている外部ディレクトリのアカウントが、ドメイン参加プロセス中に正しく認証を行うことはできません。

1. Active Directory サーバーに接続している **仮想ネットワーク** を選択し、仮想マシンをホストするサブネットを選択します。
1. **Windows Virtual Desktop information\(Windows Virtual Desktop 情報\)** を選択します。

### <a name="windows-virtual-desktop-tenant-information"></a>Windows Virtual Desktop のテナント情報

**[Windows Virtual Desktop tenant information]\(Windows Virtual Desktop テナント情報\)** タブで必要な操作は次のとおりです。

1. **[Windows Virtual Desktop tenant group name]\(Windows Virtual Desktop テナント グループ名\)** に、テナントが含まれるテナント グループの名前を入力します。 具体的なテナント グループ名を指定されていないかぎり、既定値のままにしてください。
1. **[Windows Virtual Desktop tenant name]\(Windows Virtual Desktop テナント名\)** に、このホスト プールの作成先となるテナントの名前を入力します。
1. Windows Virtual Desktop テナントの RDS 所有者としての認証に使用する資格情報の種類を指定します。 UPN またはサービス プリンシパルとパスワードを入力します。

   「[PowerShell を使用してサービス プリンシパルとロールの割り当てを作成するチュートリアル](create-service-principal-role-powershell.md)」を完了したら、 **[サービス プリンシパル]** を選択します。

1. **[Azure AD テナント ID]** の **[サービス プリンシパル]** では、サービス プリンシパルが含まれている Azure AD インスタンスのテナント管理者アカウントを入力します。 パスワード資格情報が設定されているサービス プリンシパルのみサポートされます。
1. **確認と作成** をクリックします。

## <a name="complete-setup-and-create-the-virtual-machine"></a>仮想マシンの設定と作成を完了する

**[確認と作成]** で、設定情報を確認します。 何かを変更する必要がある場合は、戻って変更してください。 準備ができたら、 **[作成]** を選択し、ホスト プールをデプロイします。

このプロセスは、作成しようとしている仮想マシンの数に応じて、30 分以上かかることもあります。

>[!IMPORTANT]
> Azure で Windows Virtual Desktop 環境のセキュリティを保護できるようにするには、ご利用の仮想マシン上のインバウンド ポート 3389 を開かないことをお勧めします。 Windows Virtual Desktop では、ユーザーがホスト プールの仮想マシンにアクセスするために、インバウンド ポート 3389 を開く必要はありません。
>
> トラブルシューティングの目的でポート 3389 を開く必要がある場合は、Just-In-Time アクセスを使用することをお勧めします。 詳細については、「[Just-In-Time アクセスを使用して管理ポートをセキュリティで保護する](../../security-center/security-center-just-in-time.md)」を参照してください。

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(オプション) デスクトップ アプリケーション グループに追加のユーザーを割り当てる

Azure Marketplace でプールの作成が完了したら、さらに多くのユーザーをデスクトップ アプリケーション グループに割り当てることができます。 さらに追加しない場合は、このセクションをスキップしてください。

デスクトップ アプリケーション グループにユーザーを割り当てるには、次の手順を行います。

1. PowerShell ウィンドウを開きます。

1. 次のコマンドを実行して、Windows Virtual Desktop 環境にサインインします。

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. 次のコマンドを使用して、デスクトップ アプリケーション グループにユーザーを追加します。

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   ユーザーの UPN は、Azure AD 内のユーザーの ID と一致させる必要があります (たとえば、 *user1@contoso.com* など)。 複数のユーザーを追加する場合は、ユーザーごとにこのコマンドを実行します。

デスクトップ アプリケーション グループに追加されたユーザーが、サポートされているリモート デスクトップ クライアントを使用して Windows Virtual Desktop にサインインし、セッション デスクトップ用のリソースを参照できるようになります。

現在サポートされているクライアントは次のとおりです。

* [Windows 7 および Windows 10 用のリモート デスクトップ クライアント](connect-windows-7-10-2019.md)
* [Windows Virtual Desktop Web クライアント](connect-web-2019.md)

## <a name="next-steps"></a>次のステップ

ホスト プールを作成し、そのデスクトップにアクセスするユーザーを割り当てました。 RemoteApp プログラムを使用して、ホスト プールを設定できます。 Windows Virtual Desktop でアプリを管理する方法について詳しくは、次のチュートリアルをご覧ください。

> [!div class="nextstepaction"]
> [アプリ グループの管理に関するチュートリアル](manage-app-groups-2019.md)
