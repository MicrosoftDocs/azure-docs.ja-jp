---
title: チュートリアル:SQL Database マネージド インスタンスをフェールオーバー グループに追加する
description: Azure SQL Database マネージド インスタンスのフェールオーバー グループの構成について学習します。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sashan, carlrab
manager: jroth
ms.date: 06/27/2019
ms.openlocfilehash: e4b7de3931c0d3508e5af6aa6bf85dfa18641aee
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624982"
---
# <a name="tutorial-add-a-sql-database-managed-instance-to-a-failover-group"></a>チュートリアル:SQL Database マネージド インスタンスをフェールオーバー グループに追加する

SQL Database マネージド インスタンスをフェールオーバー グループに追加します。 この記事では、次のことについて説明します。

> [!div class="checklist"]
> - プライマリ マネージド インスタンスを作成する
> - [フェールオーバー グループ](sql-database-auto-failover-group.md)の一部として、セカンダリ マネージド インスタンスを作成する。 
> - [テスト フェールオーバー]

  > [!NOTE]
  > マネージド インスタンスの作成にはかなりの時間がかかることがあります。 そのため、このチュートリアルの完了には数時間かかることがあります。 プロビジョニング時間の詳細については、「[マネージド インスタンスの管理操作](sql-database-managed-instance.md#managed-instance-management-operations)」を参照してください。 マネージド インスタンスでのフェールオーバー グループの使用は、現在プレビュー段階です。 

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のものが必要です。 

- Azure サブスクリプション。まだお持ちでない場合は、[無料アカウント](https://azure.microsoft.com/free/)を作成してください。 


## <a name="1----create-resource-group-and-primary-managed-instance"></a>1 - リソース グループとプライマリ マネージド インスタンスを作成する
この手順では、Azure portal を使用して、リソース グループとフェールオーバー グループのプライマリ マネージド インスタンスを作成します。 

1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. Azure portal の左上隅にある **[リソースの作成]** を選択します。 
1. 検索ボックスに「`managed instance`」と入力し、Azure SQL Managed Instance のオプションを選択します。 
1. **[作成]** を選択して、**SQL マネージド インスタンス**の作成ページを起動します。 
1. **[Azure SQL Database Managed Instance の作成]** ページの **[基本]** タブでは、次のようにします。
    1. **[プロジェクトの詳細]** で、ドロップダウンから自分の**サブスクリプション**を選び、リソース グループを**新規作成**することを選択します。 「`myResourceGroup`」など、リソース グループの名前を入力します。 
    1. **[Managed Instance Details]\(マネージド インスタンスの詳細\)** で、マネージド インスタンスの名前と、マネージド インスタンスをデプロイするリージョンを指定します。 必ず、[ペアのリージョン](/azure/best-practices-availability-paired-regions)が存在するリージョンを選択してください。 **[コンピューティングとストレージ]** は既定値のままにしておきます。 
    1. **[管理者アカウント]** で、`azureuser` などの管理者ログインと、複雑な管理者パスワードを指定します。 

    ![プライマリ MI を作成する](media/sql-database-managed-instance-failover-group-tutorial/primary-sql-mi-values.png)

1. 残りの設定は既定値のままにし、 **[確認と作成]** を選択してマネージド インスタンスの設定を確認します。 
1. **[作成]** を選択して、プライマリ マネージド インスタンスを作成します。 


## <a name="2---create-a-virtual-network"></a>2 - 仮想ネットワークを作成する
この手順では、セカンダリ マネージド インスタンス用の仮想ネットワークを作成します。 この手順が必要なのは、プライマリ マネージド インスタンスとセカンダリ マネージド インスタンスのサブネットのアドレス範囲が重複していないという要件があるためです。 

プライマリ仮想ネットワークのサブネット範囲を確認するには、これらの手順に従います。
1. [Azure portal](https://portal.azure.com) で、リソース グループに移動し、プライマリ インスタンスの仮想ネットワークを選択します。 
1. **[設定]** で **[サブネット]** を選択し、**アドレス範囲**を書き留めます。 セカンダリ マネージド インスタンス用の仮想ネットワークのサブネット アドレス範囲は、これと重複することはできません。 


   ![プライマリ サブネット](media/sql-database-managed-instance-failover-group-tutorial/verify-primary-subnet-range.png)

仮想ネットワークを作成するには、これらの手順に従います。

1. [Azure portal](https://portal.azure.com) で、 **[リソースの作成]** を選択し、*仮想ネットワーク*を検索します。 
1. Microsoft によって公開された**仮想ネットワーク** オプションを選び、次のページで **[作成]** を選択します。 
1. セカンダリ マネージド インスタンスの仮想ネットワークを構成するために必要なフィールドに入力し、 **[作成]** を選択します。 

   次の表には、セカンダリ仮想ネットワークに必要な値が示されています。

    | **フィールド** | 値 |
    | --- | --- |
    | **Name** |  セカンダリ マネージド インスタンスで使用される仮想ネットワークの名前 (`vnet-sql-mi-secondary` など)。 |
    | **アドレス空間** | 仮想ネットワークのアドレス空間 (`10.128.0.0/16` など)。 | 
    | **サブスクリプション** | プライマリ マネージド インスタンスとリソース グループが存在するサブスクリプション。 |
    | **[リージョン]** | セカンダリ マネージド インスタンスをデプロイする場所。これは、プライマリ マネージド インスタンスと[ペアのリージョン](/azure/best-practices-availability-paired-regions)に存在する必要があります。  |
    | **サブネット** | サブネットの名前。 `default` は既定で自動的に指定されます。 |
    | **アドレス範囲**| サブネットのアドレス範囲。 これは、`10.128.0.0/24` など、プライマリ マネージド インスタンスの仮想ネットワークで使用されるサブネット アドレス範囲とは異なる必要があります。  |
    | &nbsp; | &nbsp; |

    ![セカンダリ仮想ネットワークの値](media/sql-database-managed-instance-failover-group-tutorial/secondary-virtual-network.png)


## <a name="3---create-a-secondary-managed-instance"></a>3 - セカンダリ マネージド インスタンスを作成する
この手順では、Azure portal でセカンダリ マネージド インスタンスを作成します。これにより、2 つのマネージド インスタンス間のネットワークも構成されます。 

2 つ目のマネージド インスタンスは、次のようなものである必要があります。
- 空である。 
- プライマリ マネージド インスタンスに対応する[ペアのリージョン](/azure/best-practices-availability-paired-regions)内に配置されている。 
- プライマリ マネージド インスタンスとは異なるサブネットと IP 範囲がある。 

セカンダリ マネージド インスタンスを作成するには、これらの手順に従います。 

1. [Azure portal](http://portal.azure.com) で、 **[リソースの作成]** を選択し、*Azure SQL Managed Instance* を検索します。 
1. Microsoft によって公開された **Azure SQL Managed Instance** オプションを選び、次のページで **[作成]** を選択します。
1. **[Azure SQL Database Managed Instance の作成]** ページの **[基本]** タブで、セカンダリ マネージド インスタンスを構成するために必要なフィールドに入力します。 

   次の表には、セカンダリ マネージド インスタンスに必要な値が示されています。
 
    | **フィールド** | 値 |
    | --- | --- |
    | **サブスクリプション** |  プライマリ マネージド インスタンスがあるサブスクリプション。 |
    | **リソース グループ**| プライマリ マネージド インスタンスがあるリソース グループ。 |
    | **マネージド インスタンス名** | 新しいセカンダリ マネージド インスタンスの名前 (`sql-mi-secondary` など)  | 
    | **[リージョン]**| セカンダリ マネージド インスタンスの[ペアのリージョン](/azure/best-practices-availability-paired-regions)の場所。  |
    | **マネージド インスタンス管理者ログイン** | 新しいセカンダリ マネージド インスタンスに使用するログイン (`azureuser` など)。 |
    | **パスワード** | 新しいセカンダリ マネージド インスタンス用の管理者ログインで使用される複雑なパスワード。  |
    | &nbsp; | &nbsp; |

1. **[ネットワーク]** タブの **[仮想ネットワーク]** で、ドロップダウンからセカンダリ マネージド インスタンス用に作成した仮想ネットワークを選択します。

   ![セカンダリ MI ネットワーク](media/sql-database-managed-instance-failover-group-tutorial/networking-settings-for-secondary-mi.png)

1. **[追加設定]** タブの **[geo レプリケーション]** で、 **[はい]** を選択し、_フェールオーバー セカンダリとして使用する_ようにします。 ドロップダウンからプライマリ マネージド インスタンスを選択します。 
    1. 照合順序とタイム ゾーンがプライマリ マネージド インスタンスのものと一致していることを確認します。 このチュートリアルで作成されたプライマリ マネージド インスタンスでは、既定値の `SQL_Latin1_General_CP1_CI_AS` 照合順序および `(UTC) Coordinated Universal Time` タイム ゾーンが使用されていました。 

   ![セカンダリ MI ネットワーク](media/sql-database-managed-instance-failover-group-tutorial/secondary-mi-failover.png)

1. **[確認と作成]** を選択して、セカンダリ マネージド インスタンスの設定を確認します。 
1. **[作成]** を選択して、セカンダリ マネージド インスタンスを作成します。 


## <a name="4---create-primary-virtual-network-gateway"></a>4 - プライマリ仮想ネットワーク ゲートウェイを作成する 

2 つのマネージド インスタンスをフェールオーバー グループに参加させるには、2 つのマネージド インスタンスの仮想ネットワークの間にネットワーク通信を許可するゲートウェイが構成されている必要があります。 Azure portal を使用して、プライマリ マネージド インスタンスのゲートウェイを作成できます。

1. [Azure portal](https://portal.azure.com) で、リソース グループに移動し、プライマリ マネージド インスタンスの**仮想ネットワーク** リソースを選択します。 
1. **[設定]** で **[サブネット]** を選び、新しい**ゲートウェイ サブネット**を追加することを選択します。 既定値のままにします。 

   ![プライマリ マネージド インスタンスのゲートウェイを追加する](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. サブネット ゲートウェイが作成されたら、左側のナビゲーション ウィンドウで **[リソースの作成]** を選択し、検索ボックスに「`Virtual network gateway`」と入力します。 **Microsoft** によって公開された**仮想ネットワーク ゲートウェイ** リソースを選択します。 

   ![新しい仮想ネットワーク ゲートウェイを作成する](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. プライマリ マネージド インスタンスのゲートウェイを構成するために必要なフィールドに入力します。 

   次の表には、プライマリ マネージド インスタンスのゲートウェイに必要な値が示されています。
 
    | **フィールド** | 値 |
    | --- | --- |
    | **サブスクリプション** |  プライマリ マネージド インスタンスがあるサブスクリプション。 |
    | **Name** | 仮想ネットワーク ゲートウェイの名前 (`primary-mi-gateway` など)。 | 
    | **[リージョン]** | セカンダリ マネージド インスタンスがあるリージョン。 |
    | **ゲートウェイの種類** | **[VPN]** を選択します。 |
    | **VPN の種類** | **[ルート ベース]** を選択します |
    | **SKU**| 既定値の `VpnGw1` のままにします。 |
    | **Location**| プライマリ マネージド インスタンスおよびプライマリ仮想ネットワークがある場所。   |
    | **Virtual Network**| セクション 2 で作成された仮想ネットワーク (`vnet-sql-mi-primary` など) を選択します。 |
    | **パブリック IP アドレス**| **[新規作成]** を選択します。 |
    | **パブリック IP アドレス名**| IP アドレスの名前 (`primary-gateway-IP` など) を入力します。 |
    | &nbsp; | &nbsp; |
1. その他の値は既定値のままにし、 **[確認と作成]** を選択して仮想ネットワーク ゲートウェイの設定を確認します。

   ![プライマリ ゲートウェイの設定](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. **[作成]** を選択して、新しい仮想ネットワーク ゲートウェイを作成します。 

## <a name="5---configure-secondary-virtual-network-gateway"></a>5 - セカンダリ仮想ネットワーク ゲートウェイを構成する 

前のセクションの手順を繰り返し、セカンダリ マネージド インスタンスの仮想ネットワーク サブネットとゲートウェイを作成します。 セカンダリ マネージド インスタンスのゲートウェイを構成するために必要なフィールドに入力します。 

   次の表には、セカンダリ マネージド インスタンスのゲートウェイに必要な値が示されています。

   | **フィールド** | 値 |
   | --- | --- |
   | **サブスクリプション** |  セカンダリ マネージド インスタンスがあるサブスクリプション。 |
   | **Name** | 仮想ネットワーク ゲートウェイの名前 (`secondary-mi-gateway` など)。 | 
   | **[リージョン]** | セカンダリ マネージド インスタンスがあるリージョン。 |
   | **ゲートウェイの種類** | **[VPN]** を選択します。 |
   | **VPN の種類** | **[ルート ベース]** を選択します |
   | **SKU**| 既定値の `VpnGw1` のままにします。 |
   | **Location**| セカンダリ マネージド インスタンスおよびセカンダリ仮想ネットワークがある場所。   |
   | **Virtual Network**| セクション 2 で作成された仮想ネットワーク (`vnet-sql-mi-secondary` など) を選択します。 |
   | **パブリック IP アドレス**| **[新規作成]** を選択します。 |
   | **パブリック IP アドレス名**| IP アドレスの名前 (`secondary-gateway-IP` など) を入力します。 |
   | &nbsp; | &nbsp; |

   ![セカンダリ ゲートウェイの設定](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

## <a name="6---connect-the-gateways"></a>6 - ゲートウェイを接続する
この手順では、ゲートウェイ間の接続を作成します。 プライマリ ゲートウェイからセカンダリ ゲートウェイへの接続を確立してから、セカンダリ ゲートウェイとプライマリ ゲートウェイの間に別の接続を確立する必要があります。 両方のゲートウェイ間の接続を構成するときは、必ず、同じ**共有キー**を使用してください。 

接続を構成するには、これらの手順に従います。

1. [Azure portal](https://portal.azure.com) でリソース グループに移動し、手順 4 で作成したプライマリ ゲートウェイを選択します。 
1. **[設定]** で **[接続]** を選び、 **[追加]** を選択して新しい接続を作成します。 

   ![プライマリ ゲートウェイへの接続を追加する](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. `Primary-connection` などの接続の名前を入力し、`mi1mi2psk` などの**共有キー**の値を入力します。 
1. **[2 番目の仮想ネットワーク ゲートウェイ]** を選択し、セカンダリ マネージド インスタンスのゲートウェイ (`secondary-mi-gateway` など) を選びます。 

   ![プライマリからセカンダリへの接続を作成する](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. **[OK]** を選択して、新しいプライマリ ゲートウェイからセカンダリ ゲートウェイへの接続を追加します。
1. これらの手順を繰り返し、セカンダリ マネージド インスタンスのゲートウェイから、プライマリ マネージド インスタンスのゲートウェイへの接続を作成します。 

   ![セカンダリからプライマリへの接続を作成する](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)


## <a name="7---create-a-failover-group"></a>7 - フェールオーバー グループを作成する
この手順では、フェールオーバー グループを作成し、それに両方のマネージド インスタンスを追加します。 

1. [Azure portal](https://portal.azure.com) で、 **[すべてのサービス]** に移動し、検索ボックスに「`managed instance`」と入力します。 
1. (省略可能) **[SQL マネージド インスタンス]** の横にある星を選択し、左側のナビゲーション バーにショートカットとしてマネージド インスタンスを追加します。 
1. **[SQL マネージド インスタンス]** を選択し、プライマリ マネージド インスタンス (`sql-mi-primary` など) を選びます。 
1. **[設定]** で **[インスタンスのフェールオーバー グループ]** に移動し、 **[グループの追加]** を選択して **[インスタンスのフェールオーバー グループ]** ページを開きます。 

   ![フェールオーバー グループを追加する](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. **[インスタンスのフェールオーバー グループ]** ページで、`failovergrouptutorial` などのフェールオーバー グループの名前を入力し、ドロップダウンから `sql-mi-secondary` などのセカンダリ マネージド インスタンスを選択します。 **[作成]** を選択して、フェールオーバー グループを作成します。 

   ![フェールオーバー グループの作成](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. フェールオーバー グループのデプロイが完了すると、 **[フェールオーバー グループ]** ページに戻ります。 

## <a name="8---test-failover"></a>8 - フェールオーバーをテストする
この手順では、フェールオーバー グループをセカンダリ サーバーにフェールオーバーしてから、Azure portal を使用してフェールバックします。 

1. [Azure portal](https://portal.azure.com) 内のマネージド インスタンスに移動し、[設定] で **[インスタンスのフェールオーバー グループ]** を選択します。 
1. どのマネージド インスタンスがプライマリであり、どのマネージド インスタンスがセカンダリであるかを確認します。 
1. **[フェールオーバー]** を選択し、切断中の TDS セッションに関する警告で **[はい]** を選択します。 

   ![フェールオーバー グループをフェールオーバーする](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. どのマネージド インスタンスがプライマリであり、どのインスタンスがセカンダリであるかを確認します。 フェールオーバーが成功した場合は、2 つのインスタンスのロールが切り替わっているはずです。 

   ![フェールオーバー後にマネージド インスタンスのロールが切り替わっている](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. もう一度 **[フェールオーバー]** を選択し、プライマリ インスタンスをプライマリ ロールにフェールバックします。 


## <a name="clean-up-resources"></a>リソースのクリーンアップ
リソースをクリーンアップするには、まず、マネージド インスタンス、次に仮想クラスターを削除します。その後、残りのリソース、最後にリソース グループを削除します。 

1. [Azure Portal](https://portal.azure.com) で、リソース グループに移動します。 
1. マネージド インスタンスを選び、 **[削除]** を選択します。 テキスト ボックスに「`yes`」と入力して、リソースを削除することを確認し、 **[削除]** を選択します。 このプロセスがバックグラウンドで完了するまで時間がかかる場合があります。これが完了するまで、"*仮想クラスター*" やその他の依存リソースを削除することはできません。 [アクティビティ] タブで削除を監視して、マネージド インスタンスが削除されたことを確認します。 
1. マネージド インスタンスが削除されたら、"*仮想クラスター*" を削除します。そのためには、リソース グループでそれを選び、 **[削除]** を選択します。 テキスト ボックスに「`yes`」と入力して、リソースを削除することを確認し、 **[削除]** を選択します。 
1. 残りのリソースを削除します。 テキスト ボックスに「`yes`」と入力して、リソースを削除することを確認し、 **[削除]** を選択します。 
1. リソース グループを削除するには、 **[リソース グループの削除]** を選択し、リソース グループの名前 (`myResourceGroup`) を入力して、 **[削除]** を選びます。 

## <a name="next-steps"></a>次の手順

このチュートリアルでは、2 つのマネージド インスタンスの間にフェールオーバー グループを構成しました。 以下の方法について学習しました。

> [!div class="checklist"]
> - プライマリ マネージド インスタンスを作成する
> - [フェールオーバー グループ](sql-database-auto-failover-group.md)の一部として、セカンダリ マネージド インスタンスを作成する。 
> - [テスト フェールオーバー]

マネージド インスタンスに接続する方法と、マネージド インスタンスにデータベースを復元する方法に関する次のクイックスタートに進みます。 

> [!div class="nextstepaction"]
> [マネージド インスタンスに接続する](sql-database-managed-instance-configure-vm.md)
> [データベースをマネージド インスタンスに復元する](sql-database-managed-instance-get-started-restore.md)


