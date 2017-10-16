---
title: "OMS Update Management での SCCM コレクションを使用したターゲットの更新 | Microsoft Docs"
description: "この記事は、このソリューションを使用して System Center Configuration Manager を構成し、SCCM で管理されるコンピューターの更新を管理するのに役立ちます。"
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.openlocfilehash: c3ae8da65e03fe9e11b5657a6a40d02de0567da6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="integrate-system-center-configuration-manager-with-oms-update-management-preview"></a>System Center Configuration Manager と OMS Update Management (プレビュー) の統合

PC、サーバー、モバイル デバイスを管理するために System Center Configuration Manager に投資してきたお客様は、ソフトウェア更新管理 (SUM) サイクルの一環として、ソフトウェア更新プログラムの管理でもその強さと成熟度を活用できます。  

OMS と Configuration Manager の既存の統合に基づき、Configuration Manager でソフトウェア更新プログラムのデプロイを作成および事前ステージングして、管理対象の Windows サーバーのレポートと更新を行えます。また、[Update Management ソリューション](../operations-management-suite/oms-solution-update-management.md)を使用して、完了した更新プログラムのデプロイの詳細な状態を取得できます。 Windows サーバーでの更新プログラムのデプロイ管理にではなく、更新プログラムのコンプライアンス レポートに Configuration Manager を使用すると、OMS Update Management ソリューションでセキュリティ更新プログラムを管理しながら Configuration Manager へのレポートを継続できます。

## <a name="prerequisites"></a>前提条件

* Log Analytics ワークスペースに [Update Management ソリューション](../operations-management-suite/oms-solution-update-management.md)が追加され、同じリソース グループとリージョンの Automation アカウントにリンクされている必要があります。   
* 現在 System Center Configuration Manager 環境で管理されている Windows サーバーも、Update Management ソリューションが有効になっている Log Analytics ワークスペースにレポートを行う必要があります。  
* この機能では、System Center Configuration Manager の現在のブランチ バージョン 1606 以降がサポートされます。  Configuration Manager の中央管理サイトまたはスタンドアロンのプライマリ サイトを Log Analytics と統合してコレクションをインポートするには、「[Configuration Manager を Log Analytics に接続する](../log-analytics/log-analytics-sccm.md)」を参照してください。  
* Windows エージェントは、Windows Server Update Services (WSUS) サーバーと通信するように構成するか、Configuration Manager からセキュリティ更新プログラムを受信しない場合は Microsoft Update にアクセスできるように構成する必要があります。   

Azure IaaS でホストされているクライアントを既存の Configuration Manager 環境でどのように管理するかは、主に Azure データセンターとインフラストラクチャの間の接続に依存します。 この接続は、Configuration Manager インフラストラクチャに必要となる可能性のある設計変更や、これらの必要な変更をサポートするための関連コストに影響します。  続行する前に評価する必要がある計画上の考慮事項を把握するには、「[Azure の Configuration Manager - よく寄せられる質問](https://docs.microsoft.com/sccm/core/understand/configuration-manager-on-azure#networking)」を参照してください。    

## <a name="configuration"></a>構成

### <a name="manage-software-updates-from-configuration-manager"></a>Configuration Manager からのソフトウェア更新プログラムの管理 

Configuration Manager から更新プログラムのデプロイを引き続き管理する場合は、次の手順を実行します。  OMS は Configuration Manager に接続し、Log Analytics ワークスペースに接続されているクライアント コンピューターに更新プログラムを適用します。 更新プログラムの内容は、Configuration Manager によってデプロイが管理されているかのように、クライアント コンピューターのキャッシュから利用できます。  

1. [ソフトウェア更新プログラムのデプロイのプロセス](https://docs.microsoft.com/en-us/sccm/sum/deploy-use/deploy-software-updates)に関するページで説明されているプロセスを使用して、Configuration Manager 階層の最上位サイトからソフトウェア更新プログラムのデプロイを作成します。  標準のデプロイとは異なる構成が必要な唯一の設定は、デプロイ パッケージのダウンロード動作を制御するための **[ソフトウェアの更新をインストールしない]** オプションです。 この動作は、次の手順でスケジュールされた更新プログラムのデプロイを作成することによって、OMS Update Management ソリューションによって管理されます。  
2. Azure Portal の **[Automation アカウント]** 画面で Automation アカウントを選択し、「[Azure Portal で新しい変数を作成するには](../automation/automation-variables.md#to-create-a-new-variable-with-the-azure-portal)」の手順に従って **true** の値を備えた **UseOMSForSCCMUpdates** という名前のブール型の変数を作成します。
3. OMS ポータルで Update Management ダッシュボードを開きます。  「[更新プログラムの展開の作成](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment)」で説明されている手順に従って新しいデプロイを作成し、OMS コンピューター グループとして表される適切な Configuration Manager コレクションをドロップダウン リストから選択します。  以下の重要な点に注意してください。
    1. 選択した Configuration Manager デバイス コレクションにメンテナンス期間が定義されている場合、コレクションのメンバーは、OMS のスケジュールされたデプロイで定義されている **[期間]** 設定ではなくこの設定を使用します。
    2. ターゲット コレクションのメンバーは、(直接、プロキシ サーバー経由、または OMS ゲートウェイ経由で) インターネットに接続できる必要があります。  

OMS ソリューションによる更新プログラムのデプロイが完了した後、選択したコンピューター グループのメンバーであるターゲット コンピューターによって、スケジュールされた時刻にローカル クライアント キャッシュから更新プログラムがインストールされます。  [更新プログラムのデプロイ ステータスを表示](../operations-management-suite/oms-solution-update-management.md#viewing-update-deployments)して、デプロイの結果を監視できます。  

### <a name="manage-software-updates-from-oms"></a>OMS からのソフトウェア更新プログラムの管理

Configuration Manager クライアントである Windows Server VM の更新プログラムを管理するには、このソリューションによって管理されるすべてのクライアントのソフトウェア更新管理機能を無効にするようにクライアント ポリシーを構成する必要があります。  既定のクライアント設定では、階層内のすべてのデバイスが対象となります。  このポリシー設定とその構成方法の詳細については、「[System Center Configuration Manager でクライアント設定を構成する方法](https://docs.microsoft.com/sccm/core/clients/deploy/configure-client-settings)」を確認してください。  

この構成変更を実行した後、「[更新プログラムの展開の作成](../operations-management-suite/oms-solution-update-management.md#creating-an-update-deployment)」で説明されている手順に従って新しいデプロイを作成し、OMS コンピューター グループとして表される適切な Configuration Manager コレクションをドロップダウン リストから選択します。 

