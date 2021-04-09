---
title: Azure Automation Update Management を Microsoft Endpoint Configuration Manager と統合する
description: この記事では、マネージャー クライアントにソフトウェア更新プログラムを展開するために、Update Management を使用して Microsoft Endpoint Configuration Manager を構成する方法について説明します。
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: a848c7c15bf786ba26b8a1fdb1dab41b9aa20b8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575766"
---
# <a name="integrate-update-management-with-microsoft-endpoint-configuration-manager"></a>Microsoft Endpoint Configuration Manager と Update Management を統合する

PC、サーバー、モバイル デバイスを管理するために Microsoft Endpoint Configuration Manager に投資してきたお客様は、ソフトウェア更新管理 (SUM) サイクルの一環として、ソフトウェア更新プログラムの管理でもその強さと成熟度を活用できます。

Microsoft Endpoint Configuration Manager でソフトウェア更新プログラムの展開を作成および事前ステージングして、マネージド Windows サーバーのレポートと更新を行えます。また、[Update Management](overview.md) を使用して、完了した更新プログラムの展開の詳細な状態を取得できます。 Windows サーバーでの更新プログラムの展開管理にではなく、更新プログラムのコンプライアンス レポートに Microsoft Endpoint Configuration Manager を使用すると、Azure Automation Update Management でセキュリティ更新プログラムを管理しながら Microsoft Endpoint Configuration Manager へのレポートを継続できます。

>[!NOTE]
>Update Management により、Windows Server 2008 R2 の更新プログラムの評価と修正プログラムの適用はサポートされていますが、このオペレーティング システムを実行している Microsoft Endpoint Configuration Manager で管理されているクライアントはサポートされていません。

## <a name="prerequisites"></a>前提条件

* [Azure Automation Update Management](overview.md) を Automation アカウントに追加しておく必要があります。
* ご利用の Microsoft Endpoint Configuration Manager 環境で現在管理されている Windows サーバーも、Update Management が有効になっている Log Analytics ワークスペースにレポートを行う必要があります。
* この機能は、Microsoft Endpoint Configuration Manager の現在のブランチ バージョン 1606 以降で有効になります。 Microsoft Endpoint Configuration Manager の中央管理サイトまたはスタンドアロンのプライマリ サイトを Azure Monitor ログと統合してコレクションをインポートするには、[Configuration Manager と Azure Monitor ログの接続](../../azure-monitor/logs/collect-sccm.md)に関するページをご確認ください。  
* Windows エージェントは、Windows Server Update Services (WSUS) サーバーと通信するように構成するか、Microsoft Endpoint Configuration Manager からセキュリティ更新プログラムを受信しない場合は Microsoft Update にアクセスできるように構成する必要があります。

Azure IaaS でホストされているクライアントを既存の Microsoft Endpoint Configuration Manager 環境でどのように管理するかは、主に Azure データセンターとインフラストラクチャの間の接続に依存します。 この接続は、Microsoft Endpoint Configuration Manager インフラストラクチャに必要となる可能性のある設計変更や、これらの必要な変更をサポートするための関連コストに影響します。 続行する前に評価する必要がある計画上の考慮事項を把握するには、「[Azure の Configuration Manager - よく寄せられる質問](/configmgr/core/understand/configuration-manager-on-azure#networking)」を参照してください。

## <a name="manage-software-updates-from-microsoft-endpoint-configuration-manager"></a>Microsoft Endpoint Configuration Manager からのソフトウェア更新プログラムの管理

Microsoft Endpoint Configuration Manager から更新プログラムの展開を引き続き管理する場合は、次の手順を行います。 Azure Automation は Microsoft Endpoint Configuration Manager に接続し、Log Analytics ワークスペースに接続されているクライアント コンピューターに更新プログラムを適用します。 更新プログラムの内容は、Microsoft Endpoint Configuration Manager によって展開が管理されているかのように、クライアント コンピューターのキャッシュから利用できます。

1. [ソフトウェア更新プログラムの展開](/configmgr/sum/deploy-use/deploy-software-updates)に関するページで説明されているプロセスを使用して、Microsoft Endpoint Configuration Manager 階層の最上位サイトからソフトウェア更新プログラムの展開を作成します。 標準のデプロイとは異なる構成が必要な唯一の設定は、デプロイ パッケージのダウンロード動作を制御するための **[ソフトウェアの更新をインストールしない]** オプションです。 この動作は、次の手順でスケジュールされた更新プログラムのデプロイを作成することによって、Update Management によって管理されます。

2. Azure Automation で **[Update Management]** を選択します。 [更新プログラムの展開の作成](deploy-updates.md#schedule-an-update-deployment)に関するページで説明されている手順に従って新しい展開を作成し、 **[種類]** ドロップダウンから **[インポートされたグループ]** を選択して適切な Microsoft Endpoint Configuration Manager コレクションを選択します。 以下の重要な点に注意してください。

    a. 選択した Microsoft Endpoint Configuration Manager デバイス コレクションにメンテナンス期間が定義されている場合、コレクションのメンバーは、スケジュールされた展開で定義されている **[期間]** 設定ではなくこれを使用します。

    b. ターゲット コレクションのメンバーは、(直接、プロキシ サーバー経由、または Log Analytics ゲートウェイ経由で) インターネットに接続できる必要があります。

Azure Automation を介した更新プログラムのデプロイが完了した後、選択したコンピューター グループのメンバーであるターゲット コンピューターによって、スケジュールされた時刻にローカル クライアント キャッシュから更新プログラムがインストールされます。 [更新プログラムのデプロイ ステータスを表示](deploy-updates.md#check-deployment-status)して、デプロイの結果を監視できます。

## <a name="manage-software-updates-from-azure-automation"></a>Azure Automation からのソフトウェア更新プログラムの管理

Microsoft Endpoint Configuration Manager クライアントである Windows Server VM の更新プログラムを管理するには、Update Management によって管理されるすべてのクライアントのソフトウェアの更新管理機能を無効にするようにクライアント ポリシーを構成する必要があります。 既定のクライアント設定では、階層内のすべてのデバイスが対象となります。 このポリシー設定とその構成方法の詳細については、「[Configuration Manager でクライアント設定を構成する方法](/configmgr/core/clients/deploy/configure-client-settings)」を確認してください。

この構成変更を実行した後、[更新プログラムの展開の作成](deploy-updates.md#schedule-an-update-deployment)に関するページで説明されている手順に従って新しい展開を作成し、 **[種類]** ドロップダウン リストから **[インポートされたグループ]** を選択して適切な Microsoft Endpoint Configuration Manager コレクションを選択します。

## <a name="next-steps"></a>次のステップ

統合スケジュールを設定するには、「[更新プログラムのデプロイをスケジュールする](deploy-updates.md#schedule-an-update-deployment)」を参照してください。
