---
title: Windows Virtual Desktop (classic) のホスト プール Azure Resource Manager - Azure
description: Azure Resource Manager テンプレートを使用して Windows Virtual Desktop (classic) にホスト プールを作成する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b9999ae92840d79bb19464216c0f28504011b3f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008323"
---
# <a name="create-a-host-pool-in-windows-virtual-desktop-classic-with-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用して Windows Virtual Desktop (classic) にホスト プールを作成する

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Windows Virtual Desktop (classic) に適用されます。

ホスト プールは、Windows Virtual Desktop テナント環境内にある 1 つまたは複数の同一の仮想マシンをコレクションとしてまとめたものです。 各ホスト プールには、物理デスクトップの場合と同じようにユーザーが利用できるアプリ グループを含めることができます。

マイクロソフトが提供する Azure Resource Manager テンプレートを使用して Windows Virtual Desktop テナントのホスト プールを作成するには、このセクションの手順に従います。 この記事では、Windows Virtual Desktop にホスト プールを作成し、Azure サブスクリプション内の VM を使用してリソース グループを作成し、それらの VM を AD ドメインに参加させて、VM を Windows Virtual Desktop に登録する方法を説明します。

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの実行に必要なもの

Azure Resource Manager テンプレートを実行する前に、次のことを確認しておきます。

- 使用するイメージのソースがある場所。 Azure ギャラリーから取得したものですか。それともカスタムですか。
- ドメイン参加資格情報。
- Windows Virtual Desktop 資格情報。

Azure Resource Manager テンプレートを使用して Windows Virtual Desktop ホスト プールを作成するときに、Azure ギャラリー、マネージド イメージ、またはアンマネージド イメージから仮想マシンを作成できます。 VM イメージの作成方法については、「[Azure にアップロードする Windows VHD または VHDX を準備する](../../virtual-machines/windows/prepare-for-upload-vhd-image.md)」、および「[Azure で一般化された VM の管理対象イメージを作成する](../../virtual-machines/windows/capture-image-resource.md)」をご覧ください。

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>新規ホスト プールのプロビジョニングのための Azure Resource Manager テンプレートの実行

開始するには、[こちらの GitHub URL](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool) に移動します。

### <a name="deploy-the-template-to-azure"></a>テンプレートを Azure にデプロイする

Enterprise サブスクリプションにデプロイする場合は、下にスクロールして **[Deploy to Azure]\(Azure へのデプロイ\)** を選択し、イメージ ソースに基づくパラメーターの入力に進みます。

クラウド ソリューション プロバイダー サブスクリプションにデプロイする場合は、次の手順に従って Azure にデプロイします。

1. 下にスクロールし、 **[Deploy to Azure]\(Azure へのデプロイ\)** を右クリックしてから **[リンク先をコピー]** を選択します。
2. メモ帳などのテキスト エディターを開き、そこにリンクを貼り付けます。
3. "https://portal.azure.com/" の直後のハッシュタグ (#) の前に、アット マーク (@) とそれに続けてテナント ドメイン名を入力します。 使用する必要がある形式の例を次に示します: `https://portal.azure.com/@Contoso.onmicrosoft.com#create/`。
4. クラウド ソリューション プロバイダー サブスクリプションに対する管理者/共同作成者のアクセス許可を持つユーザーとして Azure portal にサインインします。
5. テキスト エディターにコピーしたリンクをアドレス バーに貼り付けます。

自分のシナリオで入力する必要のあるパラメーターについてのガイダンスは、Windows Virtual Desktop の [Readme ファイル](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)をご覧ください。 Readme は常に最新の変更内容で更新されます。

## <a name="assign-users-to-the-desktop-application-group"></a>デスクトップ アプリケーション グループへのユーザーの割り当て

GitHub の Azure Resource Manager テンプレートが完了した後、仮想マシン上で完全なセッション デスクトップのテストを開始する前に、ユーザーにアクセス権を割り当てます。

まず、まだ行っていない場合は、PowerShell セッション内で使用する [Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](/powershell/windows-virtual-desktop/overview/)します。

デスクトップ アプリケーション グループにユーザーを割り当てるには、PowerShell ウィンドウを開き、次のコマンドレットを実行して、Windows Virtual Desktop 環境にサインインします。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

その後、次のコマンドレットを使用してデスクトップ アプリケーション グループにユーザーを追加します。

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

ユーザーの UPN は、Azure Active Directory 内のユーザーの ID (たとえば、user1@contoso.com など) と一致させる必要があります。 複数のユーザーを追加する場合は、ユーザーごとにこのコマンドレットを実行する必要があります。

ここまでの手順を完了すると、デスクトップ アプリケーション グループに追加されたユーザーが、サポートされているリモート デスクトップ クライアントを使用して Windows Virtual Desktop にサインインし、セッション デスクトップ用のリソースを参照できるようになります。

>[!IMPORTANT]
>Azure で Windows Virtual Desktop 環境のセキュリティを保護できるようにするには、ご利用の VM 上の受信ポート 3389 を開かないことをお勧めします。 Windows Virtual Desktop では、ユーザーがホスト プールの VM にアクセスするために、受信ポート 3389 を開く必要はありません。 トラブルシューティングの目的でポート 3389 を開く必要がある場合は、[Just-In-Time VM アクセス](../../security-center/security-center-just-in-time.md)を使用することをお勧めします。