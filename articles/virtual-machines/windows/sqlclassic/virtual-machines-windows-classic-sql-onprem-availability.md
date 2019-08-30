---
title: オンプレミスの AlwaysOn 可用性グループの Azure への拡張 | Microsoft Docs
description: このチュートリアルでは、クラシック デプロイ モデルを使用して作成されたリソースを使用し、SQL Server Management Studio (SSMS) のレプリカの追加ウィザードを使用して、Azure に AlwaysOn 可用性グループ レプリカを追加する方法について説明します。
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: 7ca7c423-8342-4175-a70b-d5101dfb7f23
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 28819bc9d2eaf7d4b595bed59bcd1df8741b62a5
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101845"
---
# <a name="extend-on-premises-always-on-availability-groups-to-azure"></a>オンプレミスの AlwaysOn 可用性グループの Azure への拡張
AlwaysOn 可用性グループは、セカンダリ レプリカを追加することで、データベースのグループの高可用性を実現します。 これらのレプリカを使用すると、障害発生時にデータベースをフェールオーバーできます。 また、読み取りワークロードやバックアップ タスクをオフロードすることもできます。

SQL Server を含む 1 つまたは複数の Azure VM をプロビジョニングし、その VM をレプリカとしてオンプレミスの可用性グループに追加することで、オンプレミスの可用性グループを Microsoft Azure に拡張できます。

このチュートリアルでは、以下があることを前提としています。

* 有効な Azure サブスクリプション [無料試用版にサインアップ](https://azure.microsoft.com/pricing/free-trial/)できます。
* 既存のオンプレミスの AlwaysOn 可用性グループ。 可用性グループの詳細については、「 [AlwaysOn 可用性グループ (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx)」をご覧ください。
* オンプレミスのネットワークと、Azure Virtual Network 間の接続。 この仮想ネットワークを作成する方法の詳細については、「[Azure Portal を使用してサイト間接続を作成する (クラシック)](../../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)」を参照してください。

> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。

## <a name="add-azure-replica-wizard"></a>Azure レプリカ ウィザードの追加
このセクションでは、 **Azure レプリカの追加ウィザード** を使用して、AlwaysOn 可用性グループ ソリューションを拡張し、Azure レプリカを含める方法について説明します。

> [!IMPORTANT]
> **Azure レプリカの追加ウィザード**でサポートされるのは、クラシック デプロイ モデルで作成された仮想マシンのみです。 新しい VM のデプロイでは、新しい Resource Manager モデルを使用する必要があります。 Resource Manager で VM を使用する場合は、Transact-SQL コマンド (ここには示されていません) を使用して、セカンダリ Azure レプリカを手動で追加する必要があります。 このウィザードは、Resource Manager のシナリオでは機能しません。

1. SQL Server Management Studio で、 **[AlwaysOn 高可用性]** 、 >  **[可用性グループ]** 、 >  **[<可用性グループの名前>]** の順に展開します。
2. **[可用性レプリカ]** を右クリックし、 **[レプリカの追加]** をクリックします。
3. 既定では、 **可用性グループへのレプリカの追加ウィザード** が表示されます。 **[次へ]** をクリックします。  このウィザードを以前に起動したときにページの下部にある **[次回からこのページを表示しない]** チェック ボックスをオンにした場合は、この画面は表示されません。
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742861.png)
4. 既存のすべてのセカンダリ レプリカに接続する必要があります。 各レプリカの横にある **[接続...]** をクリックするか、画面下部の **[Connect All... (すべて接続...)]** をクリックできます。 認証後、 **[次へ]** をクリックして、次の画面に進みます。
5. **[レプリカの指定]** ページの上部には、いくつかのタブが表示されます。 **[レプリカ]** 、 **[エンドポイント]** 、 **[バックアップの設定]** 、および **[リスナー]** です。 **[レプリカ]** タブで、 **[Azure レプリカの追加...]** をクリックして、Azure レプリカの追加ウィザードを起動します。
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742863.png)
6. 以前に Azure 管理証明書をインストールしている場合は、ローカルの Windows 証明書ストアから既存の Azure 管理証明書を選択します。 以前に Azure サブスクリプションを利用したことがある場合は、Azure サブスクリプションの ID を選択するか入力します。 [ダウンロード] をクリックすると、Azure 管理証明書をダウンロードしてインストールし、Azure アカウントを使用しているサブスクリプションの一覧をダウンロードすることができます。
   
    ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742864.png)
7. ページの各フィールドに、レプリカをホストする Azure 仮想マシン (VM) を作成するために使用する値を入力します。
   
   | Setting | 説明 |
   | --- | --- |
   | **イメージ** |OS と SQL Server の目的の組み合わせを選択します。 |
   | **VM サイズ** |ビジネス ニーズに最も適した VM のサイズを選択します。 |
   | **VM 名** |新しい VM の一意の名前を指定します。 名前は、3 ～ 15 文字で指定する必要があります。文字、数字、およびハイフンのみ使用でき、文字で始まり、文字または数字で終わる必要があります。 |
   | **VM ユーザー名** |VM の管理者アカウントになるユーザー名を指定します。 |
   | **VM 管理者パスワード** |新しいアカウントのパスワードを指定します。 |
   | **パスワードの確認** |確認のためにもう一度新しいアカウントのパスワードを入力します。 |
   | **Virtual Network** |新しい VM で使用する Azure Virtual Network を指定します。 仮想ネットワークの詳細については、「 [Virtual Network の概要](../../../virtual-network/virtual-networks-overview.md)」を参照してください。 |
   | **Virtual Network サブネット** |新しい VM で使用する仮想ネットワーク サブネットを指定します。 |
   | **ドメイン** |あらかじめ入力されているドメインの値が正しいことを確認します。 |
   | **ドメイン ユーザー名** |ローカル クラスター ノード上のローカルの Administrators グループに属しているアカウントを指定します。 |
   | **パスワード** |ドメイン ユーザー名のパスワードを指定します。 |
8. **[OK]** をクリックして、デプロイ設定を検証します。
9. 次に、法律条項が表示されます。 これらの条項を読み、同意する場合は **[OK]** をクリックします。
10. **[レプリカの指定]** ページが再び表示されます。 **[レプリカ]** 、 **[エンドポイント]** 、および **[バックアップの設定]** の各タブで新しい Azure レプリカの設定を確認します。 ビジネス要件に合わせて設定を変更します。  これらのタブに表示されるパラメーターについて詳しくは、「[[レプリカの指定] ページ (新しい可用性グループ ウィザード/レプリカの追加ウィザード)](https://msdn.microsoft.com/library/hh213088.aspx)」をご覧ください。Azure レプリカを含む可用性グループのリスナーは [リスナー] タブを使用して作成できないのでご注意ください。 また、ウィザードを起動する前にリスナーを既に作成している場合は、そのリスナーが Azure ではサポートされないことを示すメッセージが表示されます。 リスナーを作成する方法については、「**可用性グループ リスナーの作成**」セクションで説明します。
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742865.png)
11. **[次へ]** をクリックします。
12. **[最初のデータの同期を選択]** ページで、使用するデータの同期方法を選択し、 **[次へ]** をクリックします。 ほとんどのシナリオでは、 **[完全データ同期]** を選択します。 データ同期方法の詳細については、「 [[最初のデータの同期を選択] ページ (AlwaysOn 可用性グループ ウィザード)](https://msdn.microsoft.com/library/hh231021.aspx)」をご覧ください。
13. **[検証]** ページで結果を確認します。 未解決の問題を修正し、必要に応じて、検証を再実行します。 **[次へ]** をクリックします。
    
     ![SQL](./media/virtual-machines-windows-classic-sql-onprem-availability/IC742866.png)
14. **[概要]** ページで設定を確認し、 **[完了]** をクリックします。
15. プロビジョニング プロセスが開始されます。 ウィザードが正常に完了したら、 **[閉じる]** をクリックして、ウィザードを終了します。

> [!NOTE]
> Azure レプリカの追加ウィザードでは、Users\User Name\AppData\Local\SQL Server\AddReplicaWizard にログ ファイルが作成されます。 このログ ファイルは、障害が発生した Azure レプリカのデプロイのトラブルシューティングに使用できます。 ウィザードで操作の実行に失敗すると、プロビジョニングされた VM が削除されるなど、前のすべての操作がロールバックされます。
> 
> 

## <a name="create-an-availability-group-listener"></a>可用性グループ リスナーの作成
可用性グループを作成したら、レプリカに接続するクライアントのリスナーを作成する必要があります。 リスナーは、着信接続をプライマリ レプリカまたは読み取り専用のセカンダリ レプリカに転送します。 リスナーについて詳しくは、「[Azure での AlwaysOn 可用性グループの ILB リスナーの構成](../classic/ps-sql-int-listener.md)」をご覧ください。

## <a name="next-steps"></a>次の手順
**Azure レプリカの追加ウィザード** を使用して AlwaysOn 可用性グループを Azure に拡張するだけでなく、一部の SQL Server ワークロードを完全に Azure に移行することもできます。 まず、「[Azure での SQL Server 仮想マシンのプロビジョニング](../sql/virtual-machines-windows-portal-sql-server-provision.md)」をご覧ください。

Azure VM での SQL Server の実行に関するその他のトピックについては、「 [Azure Virtual Machines における SQL Server](../sql/virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

