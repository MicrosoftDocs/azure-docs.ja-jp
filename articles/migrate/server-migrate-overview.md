---
title: Azure Migrate Server Migration を使用した VMware 移行オプションを選択する | Microsoft Docs
description: Azure Migrate Server Migration を使用して VMware VM を Azure に移行するためのオプションの概要について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: f8bfbe26dc4c6ddbcf622f91938ba060de00b2ec
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810150"
---
# <a name="select-a-vmware-migration-option"></a>VMware 移行オプションを選択する

Azure Migrate Server Migration ツールを使用して VMware VM を Azure に移行できます。 このツールには、VMware VM の移行のためのいくつかのオプションが用意されています。

- エージェントレス レプリケーションを使用した移行。 VM 上に何もインストールすることなく、その VM を移行します。
- レプリケーション用のエージェントを使用した移行。 レプリケーションのために VM 上にエージェントをインストールします。


デプロイの観点からはエージェントレス レプリケーションの方が簡単ですが、それには現在、いくつかの制限があります。

## <a name="agentless-migration-limitations"></a>エージェントレス移行の制限

制限は次のとおりです。

- **同時レプリケーション**: vCenter Server から最大 50 の VM を同時にレプリケートできます。<br/> 移行する VM が 50 を超える場合は、VM の複数のバッチを作成します。<br/> それを超える数を一度にレプリケートすると、パフォーマンスに影響を与えます。
- **VM ディスク**: 移行する VM のディスクは 60 以下である必要があります。
- **VM オペレーティング システム**: 一般に、Azure Migrate は任意の Windows Server または Linux オペレーティング システムを移行できますが、Azure で実行できるように VM に変更が必要になる場合があります。 Azure Migrate は、次のオペレーティング システムの場合、これらの変更を自動的に行います。
    - Red Hat Enterprise Linux 6.5+、7.0+
    - CentOS 6.5+、7.0+
    - SUSE Linux Enterprise Server 12 SP1+
    - Ubuntu 14.04LTS、16.04LTS、18.04LTS
    - Debian 7、8
    - その他のオペレーティング システムの場合は、移行の前に手動で調整を行う必要があります。 [移行に関するチュートリアル](tutorial-migrate-vmware.md)では、これを行う方法について説明しています。
- **Linux ブート**: /boot が専用のパーティションに存在する場合は、OS ディスク上に存在する必要があり、複数のディスクに分散していてはいけません。<br/> /boot がルート (/) パーティションの一部である場合、'/' パーティションは OS ディスク上に存在する必要があり、他のディスクにまたがっていてはいけません。
- **UEFI ブート**: UEFI ブートを使用した VM の移行はサポートされません。
- **暗号化されたディスク/ボリューム (BitLocker、cryptfs)** : 暗号化されたディスク/ボリュームを含む VM の移行はサポートされません。
- **RDM/パススルー ディスク**: VM に RDM またはパススルー ディスクが含まれている場合、これらのディスクは Azure にレプリケートされません。
- **NFS**: VM 上のボリュームとしてマウントされた NFS ボリュームはレプリケートされません。
- **ターゲット ストレージ**: VMware VM は、マネージド ディスク (Standard HDD、Premium SSD) を含む Azure VM にのみ移行できます。



## <a name="deployment-steps-comparison"></a>デプロイ手順の比較

制限を確認した後、各ソリューションのデプロイに関連する手順を理解することが、どのオプションを選択するかを決定するために役立つ場合があります。

**タスク** | **詳細** |**エージェントレス** | **エージェント ベース**
--- | --- | --- | ---
**VMware サーバーと VM を移行のために準備する** | VMware サーバーと VM でいくつかの設定を構成します。 | 必須 | 必須
**Server Migration ツールを追加する** | Azure Migrate プロジェクトで Azure Migrate Server Migration ツールを追加します。 | 必須 | 必須
**Azure Migrate アプライアンスをデプロイする** | VMware VM 上に VM の検出と評価のための軽量アプライアンスを設定します。 | 必須 | 不要。
**VM 上にモビリティ サービスをインストールする** | レプリケートする各 VM 上にモビリティ サービスをインストールします。 | 必要なし | 必須
**Azure Migrate Server Migration レプリケーション アプライアンスをデプロイする** | VMware VM 上に VM を検出するためのアプライアンスを設定し、VM 上で実行されているモビリティ サービスと Azure Migrate Server Migration の間をブリッジします。 | 必要なし | 必須
**VM をレプリケートする**。 VM レプリケーションを有効にする。 | レプリケーション設定を構成し、レプリケートする VM を選択します。 | 必須 | 必須
**テスト移行を実行する** | すべてが想定どおりに動作していることを確認するためにテスト移行を実行します。 | 必須 | 必須
**完全な移行を実行する** | VM を移行します。 | 必須 | 必須




## <a name="next-steps"></a>次の手順

エージェントレス移行を使用して [VMware VM を移行します](tutorial-migrate-vmware.md)。



