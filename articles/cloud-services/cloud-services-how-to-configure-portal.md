---
title: クラウド サービスの構成方法 (ポータル) | Microsoft Docs
description: Azure のクラウド サービスの構成方法について説明します。 クラウド サービスの構成の更新方法と、ロール インスタンスへのリモート アクセスの構成方法を紹介します。 これらの例では、Azure ポータルを使用します。
services: cloud-services
documentationcenter: ''
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 12/07/2016
ms.author: gwallace
ms.openlocfilehash: 8b60a81e06b95c69a02f88ff3275743b056c191d
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359658"
---
# <a name="how-to-configure-cloud-services"></a>Cloud Services の構成方法

クラウド サービスで最もよく使用される設定は Azure ポータルで構成できます。 また、構成ファイルを直接更新する場合は、サービス構成ファイルをダウンロードして内容を更新し、更新したファイルをアップロードして、クラウド サービスの構成を更新します。 どちらの方法でも、構成の更新はすべてのロール インスタンスに適用されます。

また、クラウド サービス ロールまたはリモート デスクトップのインスタンスを管理することもできます。

Azure で構成の更新中に 99.95% の可用性を保証できるのは、各ロールに少なくとも 2 つのロール インスタンスがある場合だけです。 この場合、1 台の仮想マシンでクライアントからの要求を処理し、もう 1 台で更新を行うことができます。 詳細については、「 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/)」を参照してください。

## <a name="change-a-cloud-service"></a>クラウド サービスの変更

[Azure ポータル](https://portal.azure.com/)を開いた後、クラウド サービスに移動します。 ここから、多くの部分を管理します。

![[設定] ページ](./media/cloud-services-how-to-configure-portal/cloud-service.png)

**[設定]** または **[すべての設定]** リンクからは **[設定]** が開き、 **[プロパティ]** の変更、 **[構成]** の変更、 **[証明書]** の管理、 **[アラート ルール]** のセットアップ、このクラウド サービスへのアクセス権を持つ **[ユーザー]** の管理を行うことができます。

![Azure クラウド サービス設定](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

### <a name="manage-guest-os-version"></a>ゲスト OS バージョンの管理

既定では、ゲスト OS は、サービス構成 (.cscfg) で指定した Windows Server 2016 などの OS ファミリー内で、サポートされている最新のイメージに定期的に更新されます。

特定の OS バージョンを対象とする必要がある場合は、 **[構成]** で設定できます。

![OS バージョンの設定](./media/cloud-services-how-to-configure-portal/cs-settings-config-guestosversion.png)

>[!IMPORTANT]
> 特定の OS バージョンを選択すると、OS の自動更新は無効になり、パッチ適用はユーザー自身の責任で行う必要があります。 ロール インスタンスが確実に更新プログラムを受け取らないと、アプリケーションのセキュリティが脆弱になる可能性があります。

## <a name="monitoring"></a>監視

クラウド サービスにアラートを追加できます。 **[設定]**  >  **[アラート ルール]**  >  **[アラートの追加]** の順にクリックします。

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

ここでは、アラートをセットアップできます。 **[メトリック]** ドロップダウン ボックスの一覧で、次の種類のデータのアラートを設定できます。

* ディスクの読み取り
* ディスクの書き込み
* ネットワーク受信
* ネットワーク送信
* CPU の割合

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>メトリック タイルから監視を構成する

**[設定]**  >  **[アラート ルール]** を使用する代わりに、クラウド サービス ブレードの **[監視]** セクションでいずれかのメトリック タイルをクリックする方法もあります。

![クラウド サービスの監視](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

ここからは、タイルで使用するグラフをカスタマイズしたり、アラート ルールを追加したりできます。

## <a name="reboot-reimage-or-remote-desktop"></a>再起動、再イメージ化、またはリモート デスクトップ

[Azure Portal (リモート デスクトップの設定)](cloud-services-role-enable-remote-desktop-new-portal.md)、[PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)、または [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md) を使用してリモート デスクトップを設定できます。

クラウド サービスの再起動、再イメージ化、またはリモート接続を行うには、クラウド サービス インスタンスを選択します。

![クラウド サービス インスタンス](./media/cloud-services-how-to-configure-portal/cs-instance.png)

リモート デスクトップ接続の開始、インスタンスのリモート再起動、インスタンスのリモートでの再イメージ化 (新しいイメージで開始) を行うことができます。

![クラウド サービス インスタンスのボタン](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)

## <a name="reconfigure-your-cscfg"></a>.cscfg を再構成する

[サービス構成 (cscfg)](cloud-services-model-and-package.md#cscfg) ファイルを使用してクラウド サービスを再構成することが必要な場合があります。 .cscfg ファイルをダウンロードし、変更して、アップロードする必要があります。

1. **[設定]** アイコンまたは **[すべての設定]** リンクをクリックして、 **[設定]** を開きます。

    ![[設定] ページ](./media/cloud-services-how-to-configure-portal/cloud-service.png)
2. **[構成]** 項目をクリックします。

    ![[構成] ブレード](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)
3. **[ダウンロード]** ボタンをクリックします。

    ![[ダウンロード]](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)
4. サービス構成ファイルを更新した後、次のステップでファイルをアップロードして構成の更新内容を適用します。

    ![アップロード](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png)
5. .cscfg ファイルを選択し、 **[OK]** をクリックします。

## <a name="next-steps"></a>次の手順

* 方法: [クラウド サービスをデプロイする](cloud-services-how-to-create-deploy-portal.md)
* [カスタム ドメイン名を構成する](cloud-services-custom-domain-name-portal.md)
* [クラウド サービスを管理する](cloud-services-how-to-manage-portal.md)
* [SSL 証明書を構成する](cloud-services-configure-ssl-certificate-portal.md)
