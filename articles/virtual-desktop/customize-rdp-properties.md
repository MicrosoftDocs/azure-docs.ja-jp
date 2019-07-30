---
title: PowerShell を使用した RDP プロパティのカスタマイズ - Azure
description: PowerShell コマンドレットを使用して Windows Virtual Desktop 用の RDP プロパティをカスタマイズする方法。
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: v-hevem
ms.openlocfilehash: ce14f990272fa1e70d07c0f4a1f18025b536eccc
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618863"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>ホスト プールのリモート デスクトップ プロトコル プロパティをカスタマイズする

マルチ モニター エクスペリエンスやオーディオ リダイレクトなど、ホスト プールのリモート デスクトップ プロトコル (RDP) のプロパティをカスタマイズすると、ニーズに基づいてユーザーに最適なエクスペリエンスを提供できます。 **Set-RdsHostPool** コマンドレットの **-CustomRdpProperty** パラメーターを使用して、Windows Virtual Desktop の RDP プロパティをカスタマイズできます。

サポートされているプロパティとその既定値の全リストについては、「[リモート デスクトップ RDP ファイルの設定](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files)」を参照してください。

まず、まだ行っていない場合は、PowerShell セッション内で使用する [Windows Virtual Desktop PowerShell モジュールをダウンロードしてインポート](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)します。

## <a name="add-or-edit-a-single-custom-rdp-property"></a>単一のカスタム RDP プロパティを追加または編集する

単一のカスタム RDP プロパティを追加または編集するには、次の PowerShell コマンドレットを実行します。

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![Name と FriendlyName が強調表示された PowerShell コマンドレット Get-RDSRemoteApp のスクリーンショット。](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>複数のカスタム RDP プロパティを追加または編集する

複数のカスタム RDP プロパティを追加または編集するには、カスタム RDP プロパティをセミコロンで区切った文字列として指定して、次の PowerShell コマンドレットを実行します。

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![Name と FriendlyName が強調表示された PowerShell コマンドレット Get-RDSRemoteApp のスクリーンショット。](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>すべてのカスタム RDP プロパティをリセットする

「[単一のカスタム RDP プロパティを追加または編集する](#add-or-edit-a-single-custom-rdp-property)」の手順に従って個々のカスタム RDP プロパティを既定値にリセットすることも、次の PowerShell コマンドレットを実行してホスト プールのすべてのカスタム RDP プロパティをリセットすることもできます。

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![Name と FriendlyName が強調表示された PowerShell コマンドレット Get-RDSRemoteApp のスクリーンショット。](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>次の手順

特定のホスト プールの RDP プロパティをカスタマイズしたので、Windows Virtual Desktop クライアントにサインインして、それらをユーザー セッションの一部としてテストできます。 これを行うには、Windows Virtual Desktop への接続方法に関するページに進みます。

- [Windows 10 および Windows 7 から接続する](connect-windows-7-and-10.md)
- [Web ブラウザーから接続する](connect-web.md)
