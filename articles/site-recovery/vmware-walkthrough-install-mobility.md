---
title: "VMware から Azure へのレプリケーションのためにモビリティ サービスをインストールする | Microsoft Docs"
description: "この記事では、Azure Site Recovery サービスを使用した VMware から Azure へのレプリケーションのためにモビリティ サービス エージェントをインストールする方法について説明します。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 3189fbcd-6b5b-4ffb-b5a9-e2080c37f9d9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: bc520bd2ea54208889861a7a3b275e3008a05d53
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-10-install-the-mobility-service"></a>手順 10: モビリティ サービスをインストールする


この記事では、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用して Azure にオンプレミスの VMware 仮想マシンをレプリケートする場合にソースとターゲットの設定を構成する方法について説明します。

モビリティ サービスによって、マシン上のデータの書き込みがキャプチャされ、プロセス サーバーに転送されます。 これは、Azure にレプリケートする各マシンにインストールする必要があります。

モビリティ サービスは、手動でインストールできるほか、レプリケーションを有効にしたときに Site Recovery プロセス サーバーからのプッシュ インストールを使用してインストールできます。また、System Center Configuration Manager ツールを使用してインストールすることもできます。 プッシュ インストールを使用すると、サービスはレプリケーションが有効になったときに VM にインストールされます。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## <a name="install-manually"></a>手動でインストールする

1. 手動インストールの[前提条件](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites)を確認してください。
2. ポータルを使用して手動でインストールする場合は、[この手順](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)に従ってください。
3. コマンド ラインからインストールする場合は、[この手順](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt)に従ってください。

## <a name="install-from-the-process-server"></a>プロセス サーバーからインストールする

VM のレプリケーションを有効にしたときにプロセス サーバーからモビリティ サービスのインストールをプッシュする場合は、プロセス サーバーが VM にアクセスするために使用できるアカウントが必要です。 このアカウントは、プッシュ インストールのみで使用されます。

1. プッシュ インストールに使用できる[アカウントを作成](vmware-walkthrough-prepare-vmware.md)しておく必要があります。 次に、Site Recovery のデプロイ時にソース設定を構成するときに使用するアカウントを指定します。
2. Windows または Linux を実行している VM 上でモビリティ サービスをプッシュする場合は、[この手順](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery)に従ってください。

## <a name="other-methods"></a>その他の方法

[Configuration Manager を使用してモビリティ サービスをインストールする方法](site-recovery-install-mobility-service-using-sccm.md)、または [Azure Automation DSC](site-recovery-automate-mobility-service-install.md) を使用する方法の詳細を確認してください。

## <a name="next-steps"></a>次のステップ

[手順 11: レプリケーションを有効にする](vmware-walkthrough-enable-replication.md)方法に関するページに進む
