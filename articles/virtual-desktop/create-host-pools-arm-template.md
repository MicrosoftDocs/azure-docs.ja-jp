---
title: Azure Resource Manager テンプレートを使用した Windows Virtual Desktop プレビューのホスト プールの作成 - Azure
description: Azure Resource Manager テンプレートを使用して Windows Virtual Desktop プレビューにホスト プールを作成する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: ba98328002cafbcede855b1187881d39f1de8fc5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796428"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用してホスト プールを作成する

ホスト プールは、Windows Virtual Desktop プレビュー テナント環境内の 1 つ以上の同一仮想マシンのコレクションです。 各ホスト プールには、物理デスクトップ上と同様にユーザーが対話できるアプリ グループを含めることができます。

マイクロソフトが提供する Azure Resource Manager テンプレートを使用して Windows Virtual Desktop テナントのホスト プールを作成するには、このセクションの手順に従います。 この記事では、Windows Virtual Desktop にホスト プールを作成し、Azure サブスクリプション内の VM を使用してリソース グループを作成し、それらの VM を AD ドメインに参加させて、VM を Windows Virtual Desktop に登録する方法を説明します。

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの実行に必要なもの

Azure Resource Manager テンプレートを実行する前に、次のことを確認しておきます。

- 使用するイメージのソースがある場所。 Azure ギャラリーから取得したものですか。それともカスタムですか。
- ドメイン参加資格情報。
- Windows Virtual Desktop 資格情報。

Azure Resource Manager テンプレートを使用して Windows Virtual Desktop ホスト プールを作成するときに、Azure ギャラリー、マネージド イメージ、またはアンマネージド イメージから仮想マシンを作成できます。 VM イメージの作成方法については、「[Azure にアップロードする Windows VHD または VHDX を準備する](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image)」、および「[Azure で一般化された VM の管理対象イメージを作成する](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)」をご覧ください。

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>新規ホスト プールのプロビジョニングのための Azure Resource Manager テンプレートの実行

開始するには、[こちらの GitHub URL](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool) に移動します。

### <a name="deploy-the-template-to-azure"></a>テンプレートを Azure にデプロイする

Enterprise サブスクリプションにデプロイする場合は、下にスクロールして **[Deploy to Azure]\(Azure へのデプロイ\)** を選択し、イメージ ソースに基づくパラメーターの入力に進みます。

クラウド ソリューション プロバイダー サブスクリプションにデプロイする場合は、次の手順に従って Azure にデプロイします。

1. 下にスクロールし、**[Deploy to Azure]\(Azure へのデプロイ\)** を右クリックしてから **[リンク先をコピー]** を選択します。
2. メモ帳などのテキスト エディターを開き、そこにリンクを貼り付けます。
3. "https://portal.azure.com/" の直後のハッシュタグ (#) の前に、アット マーク (@) とそれに続けてテナント ドメイン名を入力します。 使用する必要がある形式の例を次に示します: https://portal.azure.com/@Contoso.onmicrosoft.com#create/。
4. クラウド ソリューション プロバイダー サブスクリプションに対する管理者/共同作成者のアクセス許可を持つユーザーとして Azure portal にサインインします。
5. テキスト エディターにコピーしたリンクをアドレス バーに貼り付けます。

自分のシナリオで入力する必要のあるパラメーターについてのガイダンスは、Windows Virtual Desktop の [Readme ファイル](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)をご覧ください。 Readme は常に最新の変更内容で更新されます。

## <a name="assign-users-to-the-desktop-application-group"></a>デスクトップ アプリケーション グループへのユーザーの割り当て

GitHub の Azure Resource Manager テンプレートが完了した後、仮想マシン上で完全なセッション デスクトップのテストを開始する前に、ユーザーにアクセス権を割り当てます。

まず、まだ行っていない場合は、PowerShell セッション内で使用する [Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)します。

デスクトップ アプリケーション グループにユーザーを割り当てるには、PowerShell ウィンドウを開き、次のコマンドレットを実行して、Windows Virtual Desktop 環境にサインインします。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

次に、次のコマンドレットを使用して、Azure Resource Manager テンプレート内で指定されているテナント グループにコンテキストを設定します。

```powershell
Set-RdsContext -TenantGroupName <Tenant Group name>
```

その後、次のコマンドレットを使用してデスクトップ アプリケーション グループにユーザーを追加します。

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

ユーザーの UPN は、Azure Active Directory 内のユーザーの ID (たとえば、user1@contoso.com など) と一致する必要があります。 複数のユーザーを追加する場合は、ユーザーごとにこのコマンドレットを実行する必要があります。

ここまでの手順を完了すると、デスクトップ アプリケーション グループに追加されたユーザーが、サポートされているリモート デスクトップ クライアントを使用して Windows Virtual Desktop にサインインし、セッション デスクトップ用のリソースを参照できるようになります。

>[!IMPORTANT]
>Azure で Windows Virtual Desktop 環境のセキュリティを保護できるようにするには、ご利用の VM 上の受信ポート 3389 を開かないことをお勧めします。 Windows Virtual Desktop では、ユーザーがホスト プールの VM にアクセスするために、受信ポート 3389 を開く必要はありません。 トラブルシューティングの目的でポート 3389 を開く必要がある場合は、[Just-In-Time VM アクセス](https://docs.microsoft.com/en-us/azure/security-center/security-center-just-in-time)を使用することをお勧めします。