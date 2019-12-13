---
title: Azure Update Management と連携するように Windows Update の設定を構成する
description: この記事では、Azure Update Management と連携するように構成する Windows Update の設定について説明します。
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 804f42121293e142cf77ad73c4aab36e62e3242d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850416"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Update Management 用に Windows Update の設定を構成する

Azure Update Management では、Windows の更新プログラムのダウンロードとインストールに Windows Update を使用しています。 そのため、Update Management では Windows Update によって使用される多くの設定を尊重しています。 Windows 以外の更新プログラムを有効にする設定を使用している場合、Update Management では、それらの更新プログラムも管理されます。 更新プログラムの展開が行われる前の更新プログラムのダウンロードを有効にすると、更新プログラムの展開がより速く効率的になり、メンテナンス期間を超過する可能性が低くなります。

## <a name="pre-download-updates"></a>更新プログラムの事前ダウンロード

グループ ポリシーで更新プログラムを自動的にダウンロードするように構成するには、[[自動更新を構成する] 設定](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) を **[3]** に設定します。 この設定によって、バックグラウンドでの必要な更新プログラムのダウンロードが有効になりますが、インストールは行われません。 この方法により、Update Management がスケジュール管理下にある状態で、Update Management のメンテナンス期間外に更新プログラムをダウンロードできます。 この動作によって、Update Management での "メンテナンス期間を超過しました" エラーが回避されます。

また更新プログラムの自動ダウンロードを構成するシステム上で、次の PowerShell コマンドを実行して、この設定をオンにすることができます。

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>自動インストールを無効化する

Azure 仮想マシン (VM) 上では既定で、更新プログラムの自動インストールが有効になっています。 これにより、Update Management によるインストールをユーザーがスケジュールする前に、更新プログラムがインストールされる可能性があります。 この動作は、`NoAutoUpdate` レジストリ キーを `1` に設定すると無効化できます。 次の PowerShell スニペットは、これを行う方法を示しています。

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>再起動の設定を構成する

「[レジストリを編集して自動更新を構成する](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)」と「[再起動の管理に使われるレジストリ キー](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)」に示されているレジストリ キーを使用すると、 **[更新プログラムのデプロイ]** の設定で **[再起動しない]** を指定している場合でも、コンピューターの再起動が行われる可能性があります。 これらのレジストリ キーは、ご利用の環境に最適になるように構成してください。

## <a name="enable-updates-for-other-microsoft-products"></a>他の Microsoft 製品の更新プログラムを有効にする

既定では、Windows Update では Windows 向けの更新プログラムのみを提供しています。 **[Windows の更新時に他の Microsoft 製品の更新プログラムも入手します]** を有効にすると、Microsoft SQL Server や他の Microsoft ソフトウェアのセキュリティ修正プログラムなど、他の製品の更新プログラムも提供されます。 このオプションをグループ ポリシーで構成することはできません。 他の Microsoft 更新プログラムを有効にするシステム上で、次の PowerShell コマンドを実行してください。 Update Management はこの設定に準拠します。

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>WSUS 構成設定

Update Management は、Windows Server Update Services (WSUS) の設定に準拠します。 Update Management と連携させるために構成できる WSUS 設定を、以下の一覧に示します。

### <a name="intranet-microsoft-update-service-location"></a>イントラネットの Microsoft 更新サービスの場所

「[イントラネットの Microsoft 更新サービスの場所を指定する](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)」で、更新プログラムをスキャンおよびダウンロードするためのソースを指定できます。

## <a name="next-steps"></a>次の手順

Windows Update の設定を構成した後、「[Azure VM の更新プログラムとパッチの管理](automation-tutorial-update-management.md)」の手順に従って、更新プログラムのデプロイをスケジュールすることができます。
