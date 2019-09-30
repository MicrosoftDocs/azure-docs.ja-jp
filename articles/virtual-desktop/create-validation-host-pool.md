---
title: サービスの更新プログラムを検証するための Windows Virtual Desktop プレビューのホスト プールを作成する - Azure
description: 運用環境に更新プログラムを展開する前にサービスの更新プログラムを監視する検証ホスト プールを作成する方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: 3843a03b3b6d41520b121e5088fb13eac956e6fa
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70163593"
---
# <a name="tutorial-create-a-host-pool-to-validate-service-updates"></a>チュートリアル:サービスの更新プログラムを検証するためのホスト プールを作成する

ホスト プールは、Windows Virtual Desktop プレビュー テナント環境内にある同一の仮想マシンをコレクションとしてまとめたものです。 ホスト プールを運用環境にデプロイする前に、検証ホスト プールを作成することを強くお勧めします。 更新プログラムは最初に検証ホスト プールに適用されるので、運用環境に展開する前にサービスの更新プログラムを監視できます。 検証ホスト プールがない場合、運用環境でユーザーにダウンタイムをもたらす可能性のあるエラーを招く変更を検出できないことがあります。

アプリで最新の更新プログラムを確実に処理できるようにするには、検証ホスト プールを運用環境のホスト プールとできるだけ類似したものにする必要があります。 ユーザーは、運用環境のホスト プールに接続する場合と同じくらい頻繁に、検証ホスト プールに接続する必要があります。 ホスト プールでのテストを自動化している場合は、検証ホスト プールでの自動テストも含める必要があります。

[診断機能](diagnostics-role-service.md)または [Windows Virtual Desktop のトラブルシューティングの記事](https://docs.microsoft.com/Azure/virtual-desktop/troubleshoot-set-up-overview)を使用して、検証ホスト プールでの問題をデバッグすることができます。

>[!NOTE]
> 将来のすべての更新プログラムをテストするために、検証ホスト プールを所定の場所に置いておくことをお勧めします。

作業を開始する前に、[Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)します (まだの場合のみ)。 その後、次のコマンドレットを実行して、アカウントにサインインします。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="create-your-host-pool"></a>ホスト プールを作成する

ホスト プールは、これらのいずれかの記事の手順に従って作成できます。
- [チュートリアル:](create-host-pools-azure-marketplace.md)Azure Marketplace を使用してホスト プールを作成する
- [Azure Resource Manager テンプレートを使用してホスト プールを作成する](create-host-pools-arm-template.md)
- [PowerShell を使用してホスト プールを作成する](create-host-pools-powershell.md)

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
    CustomRdpProperty   : use multimon:i:0;
    MaxSessionLimit     : 10
    LoadBalancerType    : BreadthFirst
    ValidationEnv       : True
    Ring                :
```

## <a name="update-schedule"></a>更新スケジュール

プレビューで、サービスの更新はおよそ毎月 1 回のペースで行われます。 大きな問題がある場合は、より頻繁なペースで重要な更新プログラムが提供されます。

## <a name="next-steps"></a>次の手順

検証ホスト プールを作成したので、次は Microsoft 仮想デスクトップ リソースを管理する管理ツールをデプロイし、接続する方法を学習できます。

> [!div class="nextstepaction"]
> [管理ツールのデプロイのチュートリアル](./manage-resources-using-ui.md)
