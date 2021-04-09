---
title: PowerShell を使用して Windows Virtual Desktop (クラシック) 用の RDP プロパティをカスタマイズする - Azure
description: PowerShell コマンドレットを使用して Windows Virtual Desktop (クラシック) 用の RDP プロパティをカスタマイズする方法。
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5110e97e52939ea2115bb839768cc7ab96802961
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95020710"
---
# <a name="customize-remote-desktop-protocol-properties-for-a--windows-virtual-desktop-classic-host-pool"></a>Windows Virtual Desktop (クラシック) ホスト プールのリモート デスクトップ プロトコル プロパティをカスタマイズする

>[!IMPORTANT]
>この内容は、Azure Resource Manager Windows Virtual Desktop オブジェクトをサポートしていない Windows Virtual Desktop (クラシック) に適用されます。 Azure Resource Manager Windows Virtual Desktop オブジェクトを管理しようとしている場合は、[こちらの記事](../customize-rdp-properties.md)を参照してください。

マルチ モニター エクスペリエンスやオーディオ リダイレクトなど、ホスト プールのリモート デスクトップ プロトコル (RDP) のプロパティをカスタマイズすると、ニーズに基づいてユーザーに最適なエクスペリエンスを提供できます。 **Set-RdsHostPool** コマンドレットの **-CustomRdpProperty** パラメーターを使用して、Windows Virtual Desktop の RDP プロパティをカスタマイズできます。

サポートされているプロパティとその既定値の全リストについては、「[サポートされるリモート デスクトップ RDP ファイルの設定](/windows-server/remote/remote-desktop-services/clients/rdp-files?context=%2fazure%2fvirtual-desktop%2fcontext%2fcontext)」を参照してください。

まず、まだ行っていない場合は、PowerShell セッション内で使用する [Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](/powershell/windows-virtual-desktop/overview/)します。 その後、次のコマンドレットを実行して、ご自分のアカウントにサインインします。

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>既定の RDP のプロパティ

既定では、パブリッシュされた RDP ファイルには次のプロパティが含まれています。

|RDP のプロパティ | デスクトップ | RemoteApps |
|---|---| --- |
| マルチモニター モード | Enabled | 該当なし |
| ドライブ リダイレクト有効 | ドライブ、クリップボード、プリンター、COM ポート、USB デバイス、スマートカード| ドライブ、クリップボード、プリンター |
| リモート オーディオ モード | ローカルで再生 | ローカルで再生 |

ホスト プールに対して定義したカスタム プロパティは、これらの既定値よりも優先されます。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>単一のカスタム RDP プロパティを追加または編集する

単一のカスタム RDP プロパティを追加または編集するには、次の PowerShell コマンドレットを実行します。

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

> [!div class="mx-imgBorder"]
> ![PowerShell コマンドレット Get-RDSRemoteApp を示すスクリーンショット。カスタム RDP プロパティを編集するために、Name と FriendlyName が強調表示されています。](../media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>複数のカスタム RDP プロパティを追加または編集する

複数のカスタム RDP プロパティを追加または編集するには、カスタム RDP プロパティをセミコロンで区切った文字列として指定して、次の PowerShell コマンドレットを実行します。

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

> [!div class="mx-imgBorder"]
> ![PowerShell コマンドレット Set-RDSRemoteApp を示すスクリーンショット。カスタム RDP プロパティを編集するために、Name と FriendlyName が強調表示されています。](../media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>すべてのカスタム RDP プロパティをリセットする

「[単一のカスタム RDP プロパティを追加または編集する](#add-or-edit-a-single-custom-rdp-property)」の手順に従って個々のカスタム RDP プロパティを既定値にリセットすることも、次の PowerShell コマンドレットを実行してホスト プールのすべてのカスタム RDP プロパティをリセットすることもできます。

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

> [!div class="mx-imgBorder"]
> ![Name と FriendlyName が強調表示された PowerShell コマンドレット Get-RDSRemoteApp のスクリーンショット。](../media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>次のステップ

特定のホスト プールの RDP プロパティをカスタマイズしたので、Windows Virtual Desktop クライアントにサインインして、それらをユーザー セッションの一部としてテストできます。 次の 2 つの手順では、任意のクライアントを使用してセッションに接続する方法を説明します。

- [Windows デスクトップ クライアントを使用して接続する](connect-windows-7-10-2019.md)
- [Web クライアントに接続する](connect-web-2019.md)