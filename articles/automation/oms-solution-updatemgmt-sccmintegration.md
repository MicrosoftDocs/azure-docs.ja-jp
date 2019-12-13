---
title: Azure Automation での SCCM コレクションを使用したターゲットの更新 - Update Management
description: この記事は、このソリューションを使用して System Center Configuration Manager を構成し、SCCM マネージド コンピューターの更新を管理するのに役立ちます。
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 03/19/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 345adeee37f5f9bc4c794eb9bb624e7797197f22
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850212"
---
# <a name="integrate-system-center-configuration-manager-with-update-management"></a>System Center Configuration Manager と Update Management の統合

PC、サーバー、モバイル デバイスを管理するために System Center Configuration Manager に投資してきたお客様は、ソフトウェア更新管理 (SUM) サイクルの一環として、ソフトウェア更新プログラムの管理でもその強さと成熟度を活用できます。

Configuration Manager でソフトウェア更新プログラムのデプロイを作成および事前ステージングして、管理対象の Windows サーバーのレポートと更新を行えます。また、[Update Management ソリューション](automation-update-management.md)を使用して、完了した更新プログラムのデプロイの詳細な状態を取得できます。 Windows サーバーでの更新プログラムのデプロイ管理にではなく、更新プログラムのコンプライアンス レポートに Configuration Manager を使用すると、Update Management ソリューションでセキュリティ更新プログラムを管理しながら Configuration Manager へのレポートを継続できます。

## <a name="prerequisites"></a>前提条件

* [Update Management ソリューション](automation-update-management.md)を Automation アカウントに追加しておく必要があります。
* 現在 System Center Configuration Manager 環境で管理されている Windows サーバーも、Update Management ソリューションが有効になっている Log Analytics ワークスペースにレポートを行う必要があります。
* この機能は、System Center Configuration Manager の現在のブランチ バージョン 1606 以降で有効になります。 Configuration Manager の中央管理サイトまたはスタンドアロンのプライマリ サイトを Azure Monitor ログと統合してコレクションをインポートするには、[Configuration Manager と Azure Monitor ログの接続](../azure-monitor/platform/collect-sccm.md)に関するページを参照してください。  
* Windows エージェントは、Windows Server Update Services (WSUS) サーバーと通信するように構成するか、Configuration Manager からセキュリティ更新プログラムを受信しない場合は Microsoft Update にアクセスできるように構成する必要があります。   

Azure IaaS でホストされているクライアントを既存の Configuration Manager 環境でどのように管理するかは、主に Azure データセンターとインフラストラクチャの間の接続に依存します。 この接続は、Configuration Manager インフラストラクチャに必要となる可能性のある設計変更や、これらの必要な変更をサポートするための関連コストに影響します。 続行する前に評価する必要がある計画上の考慮事項を把握するには、「[Azure の Configuration Manager - よく寄せられる質問](/sccm/core/understand/configuration-manager-on-azure#networking)」を参照してください。

## <a name="configuration"></a>構成

### <a name="manage-software-updates-from-configuration-manager"></a>Configuration Manager からのソフトウェア更新プログラムの管理 

Configuration Manager から更新プログラムのデプロイを引き続き管理する場合は、次の手順を実行します。 Azure Automation は Configuration Manager に接続し、Log Analytics ワークスペースに接続されているクライアント コンピューターに更新プログラムを適用します。 更新プログラムの内容は、Configuration Manager によってデプロイが管理されているかのように、クライアント コンピューターのキャッシュから利用できます。

1. [ソフトウェア更新プログラムのデプロイのプロセス](/sccm/sum/deploy-use/deploy-software-updates)に関するページで説明されているプロセスを使用して、Configuration Manager 階層の最上位サイトからソフトウェア更新プログラムのデプロイを作成します。 標準のデプロイとは異なる構成が必要な唯一の設定は、デプロイ パッケージのダウンロード動作を制御するための **[ソフトウェアの更新をインストールしない]** オプションです。 この動作は、次の手順でスケジュールされた更新プログラムのデプロイを作成することによって、Update Management ソリューションによって管理されます。

1. Azure Automation で **[Update Management]** を選択します。 「[更新プログラムの展開の作成](automation-tutorial-update-management.md#schedule-an-update-deployment)」で説明されている手順に従って新しいデプロイを作成し、 **[種類]** ドロップダウン リストから **[Imported groups]\(インポートされたグループ\)** を選択して適切な Configuration Manager コレクションを選択します。 以下の重要な点に注意してください。a. 選択した Configuration Manager デバイス コレクションにメンテナンス期間が定義されている場合、コレクションのメンバーは、スケジュールされたデプロイで定義されている **[期間]** 設定ではなくこの設定を使用します。
    b. ターゲット コレクションのメンバーは、(直接、プロキシ サーバー経由、または Log Analytics ゲートウェイ経由で) インターネットに接続できる必要があります。

Azure Automation を介した更新プログラムのデプロイが完了した後、選択したコンピューター グループのメンバーであるターゲット コンピューターによって、スケジュールされた時刻にローカル クライアント キャッシュから更新プログラムがインストールされます。 [更新プログラムのデプロイ ステータスを表示](automation-tutorial-update-management.md#view-results-of-an-update-deployment)して、デプロイの結果を監視できます。

### <a name="manage-software-updates-from-azure-automation"></a>Azure Automation からのソフトウェア更新プログラムの管理

Configuration Manager クライアントである Windows Server VM の更新プログラムを管理するには、このソリューションによって管理されるすべてのクライアントのソフトウェア更新管理機能を無効にするようにクライアント ポリシーを構成する必要があります。 既定のクライアント設定では、階層内のすべてのデバイスが対象となります。 このポリシー設定とその構成方法の詳細については、「[System Center Configuration Manager でクライアント設定を構成する方法](/sccm/core/clients/deploy/configure-client-settings)」を確認してください。

この構成変更を実行した後、「[更新プログラムの展開の作成](automation-tutorial-update-management.md#schedule-an-update-deployment)」で説明されている手順に従って新しいデプロイを作成し、 **[種類]** ドロップダウン リストから **[Imported groups]\(インポートされたグループ\)** を選択して適切な Configuration Manager コレクションを選択します。

## <a name="next-steps"></a>次の手順

