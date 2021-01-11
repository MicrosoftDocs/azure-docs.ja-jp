---
title: Windows Virtual Desktop (クラシック) ホスト プール サービスの更新プログラム - Azure
description: 運用環境に更新プログラムを展開する前にサービスの更新プログラムを監視する検証ホスト プールを作成する方法。
author: Heidilohr
ms.topic: tutorial
ms.date: 05/27/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f5a8d925a9875187358dc96b1770916974d1c6bd
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "88007133"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates-in-windows-virtual-desktop-classic"></a>チュートリアル:サービスの更新プログラムを検証する Windows Virtual Desktop (クラシック) のホスト プールを作成する

>[!IMPORTANT]
>これは、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしない Windows Virtual Desktop (クラシック) を対象としたコンテンツです。 Azure Resource Manager Windows Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../create-validation-host-pool.md)を参照してください。

ホスト プールは、Windows Virtual Desktop テナント環境内にある 1 つまたは複数の同一の仮想マシンをコレクションとしてまとめたものです。 ホスト プールを運用環境にデプロイする前に、検証ホスト プールを作成することを強くお勧めします。 更新プログラムは最初に検証ホスト プールに適用されるので、運用環境に展開する前にサービスの更新プログラムを監視できます。 検証ホスト プールがない場合、運用環境でユーザーにダウンタイムをもたらす可能性のあるエラーを招く変更を検出できないことがあります。

アプリで最新の更新プログラムを確実に処理できるようにするには、検証ホスト プールを運用環境のホスト プールとできるだけ類似したものにする必要があります。 ユーザーは、運用環境のホスト プールに接続する場合と同じくらい頻繁に、検証ホスト プールに接続する必要があります。 ホスト プールでのテストを自動化している場合は、検証ホスト プールでの自動テストも含める必要があります。

[診断機能](diagnostics-role-service-2019.md)または [Windows Virtual Desktop のトラブルシューティングの記事](troubleshoot-set-up-overview-2019.md)を使用して、検証ホスト プールでの問題をデバッグすることができます。

>[!NOTE]
> 将来のすべての更新プログラムをテストするために、検証ホスト プールを所定の場所に置いておくことをお勧めします。

作業を開始する前に、[Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](/powershell/windows-virtual-desktop/overview/)します (まだの場合のみ)。 その後、次のコマンドレットを実行して、ご自分のアカウントにサインインします。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>ホスト プールを作成する

ホスト プールは、これらのいずれかの記事の手順に従って作成できます。
- [チュートリアル:](create-host-pools-azure-marketplace-2019.md)Azure Marketplace を使用してホスト プールを作成する
- [Azure Resource Manager テンプレートを使用してホスト プールを作成する](create-host-pools-arm-template.md)
- [PowerShell を使用してホスト プールを作成する](create-host-pools-powershell-2019.md)

## <a name="define-your-host-pool-as-a-validation-host-pool"></a>検証ホスト プールとしてホスト プールを定義する

次の PowerShell コマンドレットを実行して、新しいホスト プールを検証ホスト プールとして定義します。 引用符で囲まれた値を、セッションに関連する値に置き換えます。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Set-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool" -ValidationEnv $true
```

次の PowerShell コマンドレットを実行して、検証プロパティが設定されていることを確認します。 引用符で囲まれた値を、セッションに関連する値に置き換えます。

```powershell
Get-RdsHostPool -TenantName $myTenantName -Name "contosoHostPool"
```

コマンドレットの結果は、次の出力のようになります。

```
    TenantName          : contoso
    TenantGroupName     : Default Tenant Group
    HostPoolName        : contosoHostPool
    FriendlyName        :
    Description         :
    Persistent          : False
    CustomRdpProperty    : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>更新スケジュール

サービスの更新は毎月行われます。 大きな問題がある場合は、より頻繁なペースで重要な更新プログラムが提供されます。

## <a name="next-steps"></a>次のステップ

検証ホスト プールを作成したので、Azure Service Health を使用して Windows Virtual Desktop のデプロイを監視する方法を学習できます。

> [!div class="nextstepaction"]
> [サービス アラートを設定する](set-up-service-alerts-2019.md)
