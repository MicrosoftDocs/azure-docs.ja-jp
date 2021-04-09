---
title: Windows Virtual Desktop PowerShell - Azure
description: Windows Virtual Desktop 環境の設定時に PowerShell の問題を解決する方法。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: cd02ee8ab794858566aeafa96fa78919be3b85a5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100367624"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop PowerShell

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトなしで Windows Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md)を参照してください。

Windows Virtual Desktop で PowerShell を使用するときに発生するエラーと問題を解決する際、この記事を参考にしてください。 リモート デスクトップ サービスの PowerShell については、「[Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview)」を参照してください。

## <a name="provide-feedback"></a>フィードバックの提供

Windows Virtual Desktop サービスに関して製品チームや活発なコミュニティのメンバーと話をしたいときは、[Windows Virtual Desktop の技術コミュニティ](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)にアクセスしてください。

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Windows Virtual Desktop 設定時に使用される PowerShell コマンド

このセクションでは、Windows Virtual Desktop の設定時に一般的に使用される PowerShell コマンドと、その使用時に発生することがある問題の解決方法を紹介しています。

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>エラー:New-AzRoleAssignment:"The provided information does not map to an AD object ID" (指定された情報は、AD オブジェクト ID にマップされていません)

```powershell
New-AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

**原因:** *-SignInName* パラメーターによって指定されるユーザーが、Windows Virtual Desktop 環境に関連付けられている Azure Active Directory で見つかりません。

**解決策:** 次の点を確認してください。

- ユーザーは Azure Active Directory に同期します。
- ユーザーは B2C (企業-消費者間) 商取引にも B2B (企業-企業) 商取引にも関連付けません。
- Windows Virtual Desktop 環境は適切な Azure Active Directory に関連付けます。

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>エラー:New-AzRoleAssignment:"The client with object id does not have authorization to perform action over scope (code:AuthorizationFailed)" (オブジェクト ID のあるクライアントでスコープに対するアクションの実行が承認されていない (コード:AuthorizationFailed))

**原因 1:** 使用中のアカウントにサブスクリプションの所有者アクセス許可が与えられていません。

**解決策 1:** 所有者アクセス許可が与えられているユーザーは、ロールの割り当てを実行する必要があります。 あるいは、ユーザーをアプリケーション グループに割り当てられるよう、ユーザー アクセス管理者ロールにユーザーを割り当てる必要があります。

**原因 2:** 使用中のアカウントに所有者アクセス許可が与えられているが、環境の Azure Active Directory に属していないか、ユーザーが置かれている Azure Active Directory に問い合わせるアクセス許可が与えられていません。

**解決策 2:** Active Directory のアクセス許可が与えられているユーザーは、ロールの割り当てを実行する必要があります。

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>エラー:New-AzWvdHostPool -- the location is not available for resource type (場所がリソースの種類に利用できません)

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'.
```

原因: Windows Virtual Desktop では、ホスト プール、アプリケーション グループ、ワークスペースの場所を選択し、特定の場所にサービス メタデータを格納できます。 選択肢はこの機能が利用できる場所に限定されます。 このエラーは、選択した場所で機能が利用できないことを意味します。

解決策:エラー メッセージには、サポートされているリージョンの一覧があります。 サポートされているリージョンを代わりに使用してください。

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>エラー:New-AzWvdApplicationGroup must be in same location as host pool (ホスト プールと同じ場所にする必要があります)

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

**原因:** 場所が一致していません。 ホスト プール、アプリケーション グループ、ワークスペースにはすべて、サービス メタデータを格納する場所を与える必要があります。 作成したオブジェクトが互いに関連付けられている場合、同じ場所に置く必要があります。 たとえば、ホスト プールが `eastus` に置かれている場合、アプリケーション グループも `eastus` で作成する必要があります。 これらのアプリケーション グループを登録するワークスペースを作成する場合、そのワークスペースも `eastus` に置く必要があります。

**解決策:** ホスト プールが作成された場所を取得し、作成中のアプリケーション グループをその同じ場所に割り当てます。

## <a name="next-steps"></a>次のステップ

- Windows Virtual Desktop トラブルシューティングの概要とエスカレーション トラックについては、「[トラブルシューティングの概要、フィードバック、サポート](troubleshoot-set-up-overview.md)」を参照してください。
- Windows Virtual Desktop 環境とホスト プールの設定中に発生した問題を解決するには、[環境とホスト プールの作成](troubleshoot-set-up-issues.md)に関するページを参照してください。
- Windows Virtual Desktop で仮想マシン (VM) の構成中に発生した問題を解決するには、[Session host virtual machine configuration (セッション ホスト仮想マシンの構成)](troubleshoot-vm-configuration.md) に関する記事を参照してください。
- Windows Virtual Desktop クライアント接続の問題をトラブルシューティングするには、[Windows Virtual Desktop サービスの接続](troubleshoot-service-connection.md)に関するページを参照してください。
- リモート デスクトップ クライアントの問題をトラブルシューティングするには、[リモート デスクトップ クライアントのトラブルシューティング](troubleshoot-client.md) に関するページを参照してください
- サービスの詳細については、[Windows Virtual Desktop 環境](environment-setup.md)に関するページを参照してください。
- 監査アクションについては、「 [リソース マネージャーの監査操作](../azure-resource-manager/management/view-activity-logs.md)」をご覧ください。
- デプロイ時にエラーが発生した場合の対応については、 [デプロイ操作の確認](../azure-resource-manager/templates/deployment-history.md)に関するページを参照してください。
