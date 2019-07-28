---
title: Windows Virtual Desktop PowerShell - Azure
description: Windows Virtual Desktop テナント環境の設定時に PowerShell の問題を解決する方法。
services: virtual-desktop
author: chjenk
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 06b955365ffc7c0a1dff93db95932d8696293e9f
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605240"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop PowerShell

Windows Virtual Desktop で PowerShell を使用するときに発生するエラーと問題を解決する際、この記事を参考にしてください。 リモート デスクトップ サービスの PowerShell については、「[Windows Virtual Desktop PowerShell](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/)」を参照してください。

## <a name="provide-feedback"></a>フィードバックの提供

Windows Virtual Desktop がプレビューの間は、サポート ケースを受け付けていません。 Windows Virtual Desktop サービスに関して製品チームや活発なコミュニティのメンバーと話をしたいときは、[Windows Virtual Desktop の技術コミュニティ](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)にアクセスしてください。

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Windows Virtual Desktop 設定時に使用される PowerShell コマンド

このセクションでは、Windows Virtual Desktop の設定時に一般的に使用される PowerShell コマンドと、その使用時に発生することがある問題の解決方法を紹介しています。

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>エラー:Add-RdsAppGroupUser コマンド -- 指定された UserPrincipalName が、指定されたホスト プールの RemoteApp アプリ グループに既に割り当てられています。

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**原因:** 使用されたユーザー名は、別の種類のアプリ グループに既に割り当てられています。 同じセッション ホスト プールの下で、リモート デスクトップとリモート アプリ グループの両方にユーザーを割り当てることはできません。

**解決策:** リモート アプリとリモート デスクトップの両方をユーザーが必要とする場合、別のホスト プールを作成するか、セッション ホスト VM であらゆるアプリケーションを使用することを許可する、リモート デスクトップへのアクセス権限をユーザーに付与します。

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>エラー:Add-RdsAppGroupUser コマンド -- 指定された UserPrincipalName は、リモート デスクトップ テナントに関連付けられている Azure Active Directory に存在しません。

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**原因:** -UserPrincipalName によって指定されるユーザーが、Windows Virtual Desktop テナントに関連付けられている Azure Active Directory で見つかりません。

**解決策:** 次のリストにある項目を確認します。

- ユーザーが Azure Active Directory に同期されています。
- ユーザーが B2C (企業-消費者間) 商取引にも B2B (企業-企業) 商取引にも関連付けられていません。
- Windows Virtual Desktop テナントが適切な Azure Active Directory に関連付けられていません。

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>エラー:Get-RdsDiagnosticActivities -- 管理サービスに問い合わせる許可がユーザーに与えられていません

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**原因:** -TenantName パラメーター

**解決策:** -TenantName <TenantName> を指定して Get-RdsDiagnosticActivities を実行します。

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>エラー:Get-RdsDiagnosticActivities -- 管理サービスに問い合わせる許可がユーザーに与えられていません

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**原因:** -Deployment スイッチの使用。

**解決策:** -Deployment スイッチは、展開管理者だけが使用できます。 これらの管理者は通常、リモート デスクトップ サービス/Windows Virtual Desktop チームに属しています。 -Deployment スイッチを -TenantName <TenantName> に変更します。

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>エラー:New-RdsRoleAssignment -- 管理サービスに問い合わせる許可がユーザーに与えられていません

**原因 1:** 使用中のアカウントにテナントの Remote Desktop Services の所有者アクセス許可が与えられていません。

**解決策 1:** Remote Desktop Services の所有者アクセス許可が与えられているユーザーは、ロールの割り当てを実行する必要があります。

**原因 2:** 使用中のアカウントに Remote Desktop Services の所有者アクセス許可が与えられているが、テナントの Azure Active Directory に属していないか、ユーザーが置かれている Azure Active Directory に問い合わせるアクセス許可が与えられていません。

**解決策 2:** Active Directory のアクセス許可が与えられているユーザーは、ロールの割り当てを実行する必要があります。

>[!Note]
>New-RdsRoleAssignment では、Azure Active Directory (AD) に存在しないユーザーにアクセス許可を与えることができません。

## <a name="next-steps"></a>次の手順

- Windows Virtual Desktop トラブルシューティングの概要とエスカレーション トラックについては、「[トラブルシューティングの概要、フィードバック、サポート](troubleshoot-set-up-overview.md)」を参照してください。
- Windows Virtual Desktop 環境でテナント/ホスト プールを作成しているときに発生した問題を解決するには、「[Tenant and host pool creation](troubleshoot-set-up-issues.md)」 (テナントとホスト プールの作成) を参照してください。
- Windows Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、「[セッション ホスト仮想マシンの構成](troubleshoot-vm-configuration.md)」を参照してください。
- Windows Virtual Desktop クライアント接続の問題を解決するには、「[リモート デスクトップ クライアントの接続](troubleshoot-client-connection.md)」を参照してください。
- プレビュー サービスの詳細については、[Windows Desktop プレビュー環境](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)に関するページを参照してください。
- トラブルシューティング チュートリアルについては、「[Tutorial:Resource Manager テンプレート デプロイのトラブルシューティング](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)」を参照してください。
- 監査アクションについては、「 [リソース マネージャーの監査操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)」をご覧ください。
- デプロイ時にエラーが発生した場合の対応については、 [デプロイ操作の確認](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)に関するページを参照してください。