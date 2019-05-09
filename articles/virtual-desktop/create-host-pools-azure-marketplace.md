---
title: Azure Marketplace を使用して Windows Virtual Desktop プレビューのホスト プールを作成する - Azure
description: Azure Marketplace を使用して Windows Virtual Desktop プレビューのホスト プールを作成する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: f539a71fccca116ee031781df855ec55158eb63a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59257452"
---
# <a name="tutorial-create-a-host-pool-with-azure-marketplace"></a>チュートリアル:Azure Marketplace を使用してホスト プールを作成する

ホスト プールは、Windows Virtual Desktop プレビュー テナント環境内にある同一の仮想マシンをコレクションとしてまとめたものです。 各ホスト プールには、物理デスクトップの場合と同じようにユーザーが利用できるアプリ グループを含めることができます。

この記事では、Microsoft Azure Marketplace オファリングを使用して Windows Virtual Desktop テナント内にホスト プールを作成する方法を説明します。 これには、Windows Virtual Desktop にホスト プールを作成する方法、Azure サブスクリプション内の VM を使用してリソース グループを作成する方法、VM を Active Directory ドメインに参加させる方法、VM を Windows Virtual Desktop に登録する方法が含まれます。

作業を開始する前に、PowerShell セッションで使用する [Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)します (まだの場合のみ)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( <https://portal.azure.com> ) にサインインします。

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>新しいホスト プールをプロビジョニングするための Azure Marketplace オファリングを実行する

新しいホスト プールをプロビジョニングするための Azure Marketplace オファリングを実行する方法は次のとおりです。

1. **[+]** または **[+ リソースの作成]** を選択します。
2. Marketplace の検索ウィンドウに「**Windows Virtual Desktop**」と入力します。
3. **[Windows Virtual Desktop - Provision a host pool]\(Windows Virtual Desktop - ホスト プールのプロビジョニング\)** を選択し、**[作成]** を選択します。

どのブレードにどのような情報を入力するかについては、ガイダンスに従ってください。

### <a name="basics"></a>基本

[基本] ブレードで必要な操作は次のとおりです。

1. ホスト プールの名前を入力します。入力する名前は、Windows Virtual Desktop テナント内で一意のものを指定してください。
2. 個人用デスクトップに関する適切なオプションを選択します。 **[はい]** を選択した場合には、このホスト プールに接続するユーザーそれぞれに対して仮想マシンが 1 台、永久的に割り当てられます。
3. Azure Marketplace オファリングが完成したら、Windows Virtual Desktop クライアントにサインインし、デスクトップにアクセスできるユーザーをコンマ区切りリストにして入力します。 たとえば、user1@contoso.com と user2@contoso.com に対してアクセス権を割り当てる場合であれば、「user1@contoso.com,user2@contoso.com」と入力します。
4. **[新規作成]** を選択して、新しいリソース グループの名前を指定します。
5. **[場所]** には、Active Directory サーバーに接続している仮想ネットワークと同じ場所を選択します。
6. **[OK]** を選択します。

### <a name="configure-virtual-machines"></a>仮想マシンの構成

[Configure virtual machines]\(仮想マシンの構成\) ブレードで必要な操作は次のとおりです。

1. 既定値をそのまま使うか、VM の数とサイズをカスタマイズします。
2. 仮想マシンの名前のプレフィックスを入力します。 たとえば、"prefix" という名前を入力する場合であれば、仮想マシンの名前は "prefix-0"、"prefix-1" などのようになります。
3. **[OK]** を選択します。

### <a name="virtual-machine-settings"></a>仮想マシンの設定

[Virtual machine setting]\(仮想マシンの設定\) ブレードで必要な操作は次のとおりです。

1. **イメージ ソース**を選択し、その探し方と格納方法に関する適切な情報を入力します。 マネージド ディスクを使用しない場合には、.vhd ファイルが含まれるストレージ アカウントを選択します。
2. Active Directory ドメインに VM を参加させるドメイン アカウントのユーザー プリンシパル名とパスワードを入力します。 このユーザー名とパスワードは、仮想マシン上にローカル アカウントとして作成されます。 このローカル アカウントは、後でリセットできます。
3. Active Directory サーバーに接続している仮想ネットワークを選択し、仮想マシンをホストするサブネットを選択します。
4. **[OK]** を選択します。

### <a name="windows-virtual-desktop-preview-tenant-information"></a>Windows Virtual Desktop プレビューのテナント情報

[Windows Virtual Desktop tenant information]\(Windows Virtual Desktop テナント情報\) ブレードで必要な操作は次のとおりです。

1. テナントが含まれるテナント グループの **Windows Virtual Desktop テナント グループ名**を入力します。 具体的に予定していたテナント グループ名がなければ、既定値をそのまま採用してください。
2. このホスト プールの作成先となるテナントの **Windows Virtual Desktop テナント名**を入力します。
3. Windows Virtual Desktop テナントの RDS 所有者としての認証に使用する資格情報の種類を指定します。 **[サービス プリンシパル]** を選択した場合には、そのサービス プリンシパルに関連付けられている **Azure AD テナント ID** も入力する必要があります。
4. テナント管理者アカウントの資格情報を入力します。 パスワード資格情報が設定されているサービス プリンシパルのみサポートされます。
5. **[OK]** を選択します。

## <a name="complete-setup-and-create-the-virtual-machine"></a>仮想マシンの設定と作成を完了する

最後の 2 つのブレードで必要な操作は次のとおりです。

1. **[概要]** ブレードで、設定情報を確認します。 変更がある場合には、次に進む前に該当するブレードに戻ります。 情報が正しければ、**[OK]** をクリックします。
2. **[購入]** ブレードで、Azure Marketplace からの購入に関する追加情報を確認します。
3. **[作成]** を選択し、ホスト プールをデプロイします。

このプロセスは、作成しようとしている VM の数に応じて 30 分以上かかることもあります。

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(オプション) デスクトップ アプリケーション グループに追加のユーザーを割り当てる

Azure Marketplace オファリングが完成した後は、仮想マシン上でフル セッションのデスクトップのテストを始める前に、デスクトップ アプリケーション グループに追加のユーザーを割り当てることができます。 Azure Marketplace オファリングに既に既定のユーザーを追加しており、それ以上の追加が必要ない場合には、このセクションをスキップしてください。

デスクトップ アプリケーション グループにユーザーを割り当てるためにはまず、PowerShell ウィンドウを開く必要があります。 その後、次の 2 つのコマンドレットを入力する必要があります。

次のコマンドレットを実行して Windows Virtual Desktop 環境にサインインします。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

次のコマンドレットを使用して、Azure Marketplace オファリングで指定した Windows Virtual Desktop テナント グループにコンテキストを設定します。 Windows Virtual Desktop テナント グループの値を Azure Marketplace オファリング内の既定値のままにした場合には、この手順をスキップします。

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
```

この 2 つの作業が済んだら、次のコマンドレットを使用してデスクトップ アプリケーション グループにユーザーを追加します。

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

ユーザーの UPN は、Azure Active Directory 内のユーザーの ID (たとえば、user1@contoso.com など) と一致させる必要があります。 複数のユーザーを追加する場合は、ユーザーごとにこのコマンドレットを実行する必要があります。

ここまでの手順を完了すると、デスクトップ アプリケーション グループに追加されたユーザーが、サポートされているリモート デスクトップ クライアントを使用して Windows Virtual Desktop にサインインし、セッション デスクトップ用のリソースを参照できるようになります。

現在サポートされているクライアントは次のとおりです。

- [Windows 7 および Windows 10 用のリモート デスクトップ クライアント](connect-windows-7-and-10.md)
- [Windows Virtual Desktop Web クライアント](connect-web.md)

>[!IMPORTANT]
>Azure で Windows Virtual Desktop 環境のセキュリティを保護できるようにするには、ご利用の VM 上の受信ポート 3389 を開かないことをお勧めします。 Windows Virtual Desktop では、ユーザーがホスト プールの VM にアクセスするために、受信ポート 3389 を開く必要はありません。 トラブルシューティングの目的でポート 3389 を開く必要がある場合、[Just-In-Time VM アクセス](https://docs.microsoft.com/en-us/azure/security-center/security-center-just-in-time)を使用することをお勧めします。

## <a name="next-steps"></a>次の手順

ホスト プールを作成し、そのデスクトップにアクセスするユーザーを割り当てたので、ホスト プールに RemoteApp を設定できるようになりました。 Windows Virtual Desktop 内でアプリを管理する方法に関する詳細については、アプリ グループの管理に関するチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [アプリ グループの管理に関するチュートリアル](./manage-app-groups.md)
