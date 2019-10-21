---
title: Azure Migrate Server Migration を使用した VMware 移行オプションを選択する | Microsoft Docs
description: Azure Migrate Server Migration を使用して VMware VM を Azure に移行するためのオプションの概要について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: 9ddb63fcf4d960d2d8ed2671eba83fc38cddcbe4
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71710045"
---
# <a name="select-a-vmware-migration-option"></a>VMware 移行オプションを選択する

Azure Migrate Server Migration ツールを使用して VMware VM を Azure に移行できます。 このツールには、VMware VM の移行のためのいくつかのオプションが用意されています。

- エージェントレス レプリケーションを使用した移行。 VM 上に何もインストールすることなく、その VM を移行します。
- レプリケーション用のエージェントを使用した移行。 レプリケーションのために VM 上にエージェントをインストールします。




## <a name="compare-migration-methods"></a>移行方法を比較する

これらの一部の比較を使用して、使用する方法を決定します。 [エージェントレス](migrate-support-matrix-vmware.md#agentless-migration-vmware-server-requirements)および[エージェント ベース](migrate-support-matrix-vmware.md#agent-based-migration-vmware-server-requirements)の移行についての完全なサポート要件を確認することもできます。

**設定** | **エージェントレス** | **エージェント ベース**
--- | --- | ---
**Azure のアクセス許可** | Azure Migrate プロジェクトを作成するためのアクセス許可、および Azure Migrate アプライアンスをデプロイするときに作成される Azure AD アプリを登録するためのアクセス許可が必要です。 | Azure サブスクリプションに対する共同作成者のアクセス許可が必要です。 
**同時レプリケーション** | vCenter Server から最大 100 の VM を同時にレプリケートできます。<br/> 移行する VM が 50 を超える場合は、VM の複数のバッチを作成します。<br/> それを超える数を一度にレプリケートすると、パフォーマンスに影響を与えます。 | NA
**アプライアンスのデプロイ** | [Azure Migrate アプライアンス](migrate-appliance.md)がオンプレミスにデプロイされます。 | [Azure Migrate レプリケーション アプライアンス](migrate-replication-appliance.md)がオンプレミスにデプロイされます。
**Site Recovery の互換性** | 互換性あり。 | Site Recovery を使用してコンピューターのレプリケーションを設定している場合は、Azure Migrate Server Migration を使用してレプリケートすることはできません。
**ターゲット ディスク** | マネージド ディスク | マネージド ディスク
**ディスクの制限** | OS ディスク:2 TB<br/><br/> データ ディスク:4 TB<br/><br/> 最大ディスク数:60 | OS ディスク:2 TB<br/><br/> データ ディスク:8 TB<br/><br/> 最大ディスク数:63
**パススルー ディスク** | サポートされていません | サポートされています
**UEFI ブート** | サポートされていません | Azure 内の移行された VM は、自動的に BIOS ブート VM に変換されます。<br/><br/> OS ディスクには最大 4 つのパーティションが必要で、ボリュームは NTFS でフォーマットされている必要があります。


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

エージェントレス移行で [VMware VM を移行する](tutorial-migrate-vmware.md)。



