---
title: "物理サーバーから Azure へのレプリケーションのためにモビリティ サービスをインストールする | Microsoft Docs"
description: "この記事では、Azure Site Recovery サービスを使用して Azure にレプリケートする物理サーバーにモビリティ サービス エージェントをインストールする方法について説明します。"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: d73267d7a64221a3138af19e9a2d5dd15809b927
ms.contentlocale: ja-jp
ms.lasthandoff: 06/29/2017

---

# <a name="step-9-install-the-mobility-service"></a>手順 9: モビリティ サービスをインストールする


この記事では、Azure Portal の [Azure Site Recovery](site-recovery-overview.md) サービスを使用してオンプレミスの Windows/Linux 物理サーバーを Azure にレプリケートする場合にモビリティ サービス コンポーネントをインストールする方法について説明します。

モビリティ サービスによって、マシン上のデータの書き込みがキャプチャされ、プロセス サーバーに転送されます。 これは、Azure にレプリケートする各サーバーにインストールする必要があります。

モビリティ サービスは、手動でインストールできるほか、レプリケーションを有効にしたときに Site Recovery プロセス サーバーからのプッシュ インストールを使用してインストールできます。また、System Center Configuration Manager などのツールを使用してインストールすることもできます。 プッシュ インストールを使用すると、サービスはレプリケーションを有効にしたときにサーバーにインストールされます。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。

## <a name="install-manually"></a>手動でインストールする

1. 手動インストールの[前提条件](site-recovery-vmware-to-azure-install-mob-svc.md#prerequisites)を確認してください。
2. ポータルを使用して手動でインストールする場合は、[この手順](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui)に従ってください。
3. コマンド ラインからインストールする場合は、[この手順](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-at-a-command-prompt)に従ってください。

## <a name="install-from-the-process-server"></a>プロセス サーバーからインストールする

マシンのレプリケーションを有効にしたときにプロセス サーバーからモビリティ サービスのインストールをプッシュする場合は、プロセス サーバーがマシンにアクセスするために使用できるアカウントが必要です。 このアカウントは、プッシュ インストールのみで使用されます。

1. アカウントを作成していない場合は、次のガイドラインに従ってアカウントを作成します。

    - ドメイン アカウントまたはローカル アカウントを使用できます。
    - Windows の場合、ドメイン アカウントを使用していなければ、ローカル マシンでリモート ユーザー アクセス コントロールを無効にする必要があります。 無効にするには、レジスタで、**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** の下に、値 1 を指定した DWORD エントリの **LocalAccountTokenFilterPolicy** を追加します。
    - CLI から Windows 用のレジストリ エントリを追加する場合は、次のように入力します。

        ```
        REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.
        ```

    - Linux の場合、アカウントは、ソース Linux サーバーの root である必要があります。

2. Windows または Linux を実行している VM 上でモビリティ サービスをプッシュする場合は、[この手順](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-by-push-installation-from-azure-site-recovery)に従ってください。

## <a name="other-installation-methods"></a>その他のインストール方法

- Configuration Manager を使用してモビリティ サービスをインストールする方法については、[こちら](site-recovery-install-mobility-service-using-sccm.md)を確認してください。
- Azure Automation DSC を使用してインストールする方法については、[こちら](site-recovery-automate-mobility-service-install.md)を確認してください。


## <a name="next-steps"></a>次のステップ

[手順 10: レプリケーションを有効にする](physical-walkthrough-enable-replication.md)方法に関するページに進む

