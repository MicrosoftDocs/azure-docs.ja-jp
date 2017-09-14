---
title: "AWS から Azure への VM の移行 | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用して、Amazon Web Services (AWS) で実行中の仮想マシンを Azure に移行する方法を説明します。"
services: site-recovery
documentationcenter: 
author: bsiva
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/31/2017
ms.author: bsiva
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b3c0727a279649f4f7dae30d41027129ce5b04ee
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Azure Site Recovery を使用して Amazon Web Services (AWS) から Azure に仮想マシンを移行する

この記事では、[Azure Site Recovery](site-recovery-overview.md) サービスを使用して、AWS Windows インスタンスを Azure 仮想マシンに移行する方法を説明します。

移行は事実上 AWS から Azure へのフェールオーバーです。 マシンを AWS にフェールバックすることはできず、進行中のレプリケーションはありません。 この記事は Azure Portal での移行手順を説明しており、[物理マシンを Azure にレプリケートする](site-recovery-vmware-to-azure.md)手順に基づいています。

コメントや質問はこの記事の末尾、または [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

Site Recovery を使用すると、次のどのオペレーティング システムで実行している EC2 インスタンスでも移行できます。

- Windows (64 ビットのみ)
    - Windows Server 2008 R2 SP1+ (Citrix PV ドライバーまたは AWS PV ドライバーのみ。 **RedHat PV ドライバーを実行しているインスタンスはサポートされません**) Windows Server 2012 Windows Server 2012 R2
- Linux (64 ビットのみ)
    - Red Hat Enterprise Linux 6.7 (HVM 仮想化インスタンスのみ)

## <a name="prerequisites"></a>前提条件

このデプロイで必要なものを次に示します。

* **構成サーバー**: Windows Server 2012 R2 を実行する Amazon EC2 VM が構成サーバーとしてデプロイされています。 既定では、構成サーバーをデプロイするときに、他の Azure Site Recovery コンポーネント (プロセス サーバーとマスター ターゲット サーバー) がインストールされます。 この記事では、Azure Portal での移行手順を説明しています。基となる手順の詳細については、[こちら](site-recovery-components.md)を参照してください。

* **EC2 インスタンス**: 移行対象の Amazon EC2 仮想マシンのインスタンス。

## <a name="deployment-steps"></a>デプロイメントの手順

1. Recovery Services コンテナーを作成します。
2. EC2 インスタンスのセキュリティ グループでは、移行する EC2 インスタンスと、構成サーバーのデプロイを予定しているインスタンスの間の通信を許可するルールを構成しておく必要があります。

3. EC2 インスタンスと同じ Amazon Virtual Private Cloud に、ASR 構成サーバーをデプロイします。 構成サーバーのデプロイの要件については、"VMware/物理サーバーから Azure" の前提条件を参照してください。

    ![DeployCS](./media/site-recovery-migrate-aws-to-azure/migration_pic2.png)

4.  構成サーバーが AWS にデプロイされ、Recovery Services コンテナーに登録されると、次に示すように Site Recovery インフラストラクチャの下に構成サーバーとプロセス サーバーが表示されます。

    ![CSinVault](./media/site-recovery-migrate-aws-to-azure/migration_pic3.png)

5. 構成サーバーをデプロイしたら (表示されるまでに最大 15 分かかる場合があります)、移行する VM と通信できることを検証します。

6. [レプリケーション設定をセットアップします](site-recovery-setup-replication-settings-vmware.md)。

7. レプリケーションを有効にします。移行する VM のレプリケーションを有効にします。 EC2 コンソールから取得できるプライベート IP アドレスを使用して、EC2 インスタンスを検出できます。

    ![SelectVM](./media/site-recovery-migrate-aws-to-azure/migration_pic4.png)

8. EC2 インスタンスが保護され、Azure へのレプリケーションが完了したら、[テスト フェールオーバーを実行](site-recovery-test-failover-to-azure.md)して、Azure におけるアプリケーションのパフォーマンスを検証します。

    ![TFI](./media/site-recovery-migrate-aws-to-azure/migration_pic5.png)

9. VM ごとに、AWS から Azure へのフェールオーバーを実行します。 必要に応じて、復旧計画を作成してフェールオーバーを実行すると、複数の仮想マシンを AWS から Azure に移行することができます。 [こちら](site-recovery-create-recovery-plans.md) をご覧ください。

10. 移行の場合、フェールオーバーをコミットする必要はありません。 代わりに、移行する各マシンの [移行の完了] オプションを選択します。 [移行の完了] アクションにより、移行プロセスが完了し、マシンのレプリケーションが削除され、マシンの Site Recovery の課金が停止されます。

    ![移行](./media/site-recovery-migrate-aws-to-azure/migration_pic6.png)

## <a name="next-steps"></a>次のステップ

- ディザスター リカバリーの要件に沿って、[移行したマシンを別のリージョンにレプリケートできるように準備する](site-recovery-azure-to-azure-after-migration.md)
- [Azure 仮想マシンをレプリケート](site-recovery-azure-to-azure.md)してワークロードの保護を開始する

