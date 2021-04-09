---
title: Azure Automation Update Management 用に Windows Update の設定を構成する
description: この記事では、Azure Automation Update Management と連携するように Windows Update の設定を構成する方法について説明します。
services: automation
ms.subservice: update-management
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: a1f95ca856223628974a9519b7c4811bde43965e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92221666"
---
# <a name="configure-windows-update-settings-for-azure-automation-update-management"></a>Azure Automation Update Management 用に Windows Update の設定を構成する

Azure Automation Update Management では、Windows の更新プログラムのダウンロードとインストールに [Windows Update クライアント](/windows/deployment/update/windows-update-overview)を使用しています。 Windows Server Update Services (WSUS) または Windows Update への接続時に、Windows Update クライアントによって使用される固有の設定があります。 これらの設定の多くは、以下によって管理できます。

- ローカル グループ ポリシー エディター
- グループ ポリシー
- PowerShell
- レジストリの直接編集

Update Management では Windows Update クライアントを制御するために指定される多くの設定が尊重されます。 Windows 以外の更新プログラムを有効にする設定を使用している場合、Update Management では、それらの更新プログラムも管理されます。 更新プログラムの展開が行われる前の更新プログラムのダウンロードを有効にすると、更新プログラムの展開がより速く効率的になり、メンテナンス期間を超過する可能性が低くなります。

Azure サブスクリプションで WSUS を設定し、Windows 仮想マシンを安全に最新の状態に保つ方法に関するその他の推奨事項については、[WSUS を使用して Azure で Windows 仮想マシンを更新するためのデプロイを計画する](/azure/architecture/example-scenario/wsus/)方法に関する記事を確認してください。

## <a name="pre-download-updates"></a>更新プログラムの事前ダウンロード

更新プログラムを自動的にインストールすることなく自動的にダウンロードするように構成する場合、グループ ポリシーを使用して [[自動更新を構成する] 設定](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates)を 3 に設定できます。 この設定により、必要な更新プログラムがバックグラウンドでダウンロードされ、更新プログラムをインストールする準備ができたことが通知されるようになります。 この方法により、Update Management がスケジュール管理下にある状態で、Update Management のメンテナンス期間外に更新プログラムをダウンロードできます。 この動作により、Update Management の `Maintenance window exceeded` エラーを防ぎます。

この設定は PowerShell で有効にできます。

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>再起動の設定を構成する

「[レジストリを編集して自動更新を構成する](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)」と「[再起動の管理に使われるレジストリ キー](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)」に示されているレジストリ キーを使用すると、 **[更新プログラムのデプロイ]** の設定で **[再起動しない]** を指定している場合でも、コンピューターの再起動が行われる可能性があります。 これらのレジストリ キーは、ご利用の環境に最適になるように構成してください。

## <a name="enable-updates-for-other-microsoft-products"></a>他の Microsoft 製品の更新プログラムを有効にする

既定では、Windows Update クライアントは、Windows のみに更新プログラムを提供するように構成されています。 **[Windows の更新時に他の Microsoft 製品の更新プログラムも入手します]** を有効にすると、Microsoft SQL Server や他の Microsoft ソフトウェアのセキュリティ修正プログラムなど、他の製品の更新プログラムも提供されます。 このオプションは、Windows 2016 以降で使用可能な最新の[管理用テンプレート ファイル](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)をダウンロードしてコピーした場合に構成できます。

Windows Server 2012 R2 を実行しているコンピューターがある場合は、グループ ポリシーでこの設定を構成することはできません。 それらのマシンでは、次の PowerShell コマンドを実行します。

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="make-wsus-configuration-settings"></a>WSUS 構成設定を行う

Update Management では WSUS 設定がサポートされています。 「[イントラネットの Microsoft 更新サービスの場所を指定する](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)」の手順を使用して、更新プログラムをスキャンおよびダウンロードするためのソースを指定できます。 既定では、Windows Update クライアントは Windows Update から更新プログラムをダウンロードするように構成されています。 WSUS サーバーをお使いのマシン用のソースとして指定する場合、更新プログラムが WSUS で承認されていないと、更新プログラムのデプロイは失敗します。 

その内部の更新サービスにマシンを制限するには、[インターネット上の Windows Update に接続しない](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations)ように設定します。

## <a name="next-steps"></a>次のステップ

「[VM の更新プログラムとパッチの管理](manage-updates-for-vm.md)」の手順に従って、更新プログラムの展開をスケジュールします。
