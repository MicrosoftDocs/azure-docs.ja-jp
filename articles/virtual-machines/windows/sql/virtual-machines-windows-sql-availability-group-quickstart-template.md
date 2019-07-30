---
title: Azure クイック スタート テンプレートを使用して Azure VM で SQL Server の Always On 可用性グループを構成する
description: Azure クイック スタート テンプレートを使用して、Windows フェールオーバー クラスターを作成し、SQL Server VM をそのクラスターに参加させ、リスナーを作成し、Azure で内部ロード バランサーを構成します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/04/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 406bd11765e4b580849e8719939c3e11c19d99a8
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67604562"
---
# <a name="use-azure-quickstart-templates-to-configure-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Azure クイック スタート テンプレートを使用して Azure VM で SQL Server の Always On 可用性グループを構成する
この記事では、Azure クイック スタート テンプレートを使用して、Azure での SQL Server Virtual Machines 用の Always On 可用性グループ構成のデプロイを部分的に自動化する方法について説明します。 このプロセスでは、2 つの Azure クイック スタート テンプレートが使用されます。 

   | Template | 説明 |
   | --- | --- |
   | [101-sql-vm-ag-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) | Windows フェールオーバー クラスターを作成し、それに SQL Server VM を参加させます。 |
   | [101-sql-vm-aglistener-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) | 可用性グループ リスナーを作成し、内部ロード バランサーを構成します。 このテンプレートは、Windows フェールオーバー クラスターが **101-sql-vm-ag-setup** テンプレートを使用して作成された場合にのみ使用できます。 |
   | &nbsp; | &nbsp; |

可用性グループの作成や、内部ロード バランサーの作成など、可用性グループ構成の他の部分は手動で行う必要があります。 この記事では、一連の自動および手動の手順を示します。
 

## <a name="prerequisites"></a>前提条件 
クイック スタート テンプレートを使用して Always On 可用性グループのセットアップを自動化するには、次の前提条件が必要です。 
- [Azure サブスクリプション](https://azure.microsoft.com/free/)。
- ドメイン コントローラーを含むリソース グループ。 
- [SQL VM リソース プロバイダーに登録](virtual-machines-windows-sql-register-with-resource-provider.md)されているのと同じ可用性セットまたは可用性ゾーンにある、1 つ以上のドメイン参加済みの、[SQL Server 2016 (またはそれ以上の) Enterprise エディションを実行する Azure の VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。  
- 可用性グループと同じサブネット内の内部ロード バランサー用に 1 つと、可用性グループ リスナー用に 1 つの、2 つの使用可能な (どのエンティティでも使用されていない) IP アドレス。 既存のロード バランサーが使用されている場合は、使用可能な IP アドレスが 1 つだけ必要です。  

## <a name="permissions"></a>アクセス許可
Azure クイック スタート テンプレートを使用して Always On 可用性グループを構成するには、次のアクセス許可が必要です。 

- ドメインで 'コンピューター オブジェクトを作成する' ためのアクセス許可を持っている既存のドメイン ユーザー アカウント。  たとえばドメイン管理者アカウントは、一般に十分なアクセス許可を持っています (例: account@domain.com)。 _クラスターを作成するには、このアカウントが、各 VM でローカル管理者グループに含まれている必要もあります。_
- SQL Server サービスを制御するドメイン ユーザー アカウント。 


## <a name="step-1---create-the-wsfc-and-join-sql-server-vms-to-the-cluster-using-quickstart-template"></a>手順 1 - WSFC を作成し、クイック スタート テンプレートを使用して SQL Server VM をクラスターに参加させる 
SQL Server VM が SQL VM の新しいリソース プロバイダーに登録されたら、SQL Server VM を *SqlVirtualMachineGroups* に参加させることができます。 このリソースでは、バージョン、エディション、完全修飾ドメイン名、クラスターと SQL サービスの両方を管理するための AD アカウント、クラウド監視としてのストレージ アカウントなど、Windows フェールオーバー クラスターのメタデータを定義します。 SQL Server VM を *SqlVirtualMachineGroups* リソース グループに追加することで、Windows フェールオーバー クラスター サービスをブートストラップし、クラスターを作成します。次にそのクラスターに、それらの SQL Server VM を参加させます。 この手順は **101-sql-vm-ag-setup** クイック スタート テンプレートで自動化されますが、以下の手順を使用して実装することができます。

1. [**101-sql-vm-ag-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-ag-setup) クイック スタート テンプレートに移動し、 **[Azure へのデプロイ]** を選択して、Azure portal 内でクイック スタート テンプレートを起動します。
1. 必須フィールドに入力し、Windows フェールオーバー クラスターのメタデータを構成します。 省略可能なフィールドは空白のままでかまいません。

    次の表には、テンプレートに必要な値が示されています。 

   | **フィールド** | 値 |
   | --- | --- |
   | **サブスクリプション** |  SQL Server VM が存在するサブスクリプション。 |
   |**リソース グループ** | SQL Server VM が属するリソース グループ。 | 
   |**フェールオーバー クラスター名** | 新しい Windows フェールオーバー クラスターの任意の名前。 |
   | **既存の VM のリスト** | 可用性グループに参加させるため、この新しいクラスターの一部となる、SQL Server VM。 これらの値はコンマとスペースで区切ります (例:SQLVM1, SQLVM2)。 |
   | **SQL Server のバージョン** | ドロップダウンから、SQL Server VM の SQL Server のバージョンを選択します。 現在サポートされているのは、SQL 2016 と SQL 2017 のイメージのみです。 |
   | **既存の完全修飾ドメイン名** | SQL Server VM が属するドメインの既存の FQDN。 |
   | **既存のドメイン アカウント** | テンプレートのデプロイ時に、ドメインで [CNO](/windows-server/failover-clustering/prestage-cluster-adds) としてコンピューター オブジェクトを作成するアクセス許可を持っている既存のドメイン ユーザー アカウントが作成されます。 たとえばドメイン管理者アカウントは、一般に十分なアクセス許可を持っています (例: account@domain.com)。 *クラスターを作成するには、このアカウントが、各 VM でローカル管理者グループに含まれている必要もあります。*| 
   | **ドメイン アカウントのパスワード** | 前述のドメイン ユーザー アカウントのパスワード。 | 
   | **既存の SQL サービス アカウント** | 可用性グループのデプロイ時に [SQL Server サービス](/sql/database-engine/configure-windows/configure-windows-service-accounts-and-permissions)を制御するドメイン ユーザー アカウント (例: account@domain.com)。 |
   | **SQL サービス パスワード** | SQL Server サービスを制御するドメイン ユーザー アカウントで使用されるパスワード。 |
   | **クラウド監視の名前** | これは、クラウド監視のために作成され、使用される新しい Azure ストレージ アカウントです。 この名前は変更できます。 |
   | **\_artifacts の場所** | このフィールドは既定で設定されます。変更はしないでください。 |
   | **Sas トークンとしての \_artifacts の場所** | このフィールドは、意図的に空白のままにされています。 |
   | &nbsp; | &nbsp; |

1. 使用条件に同意する場合は、 **[上記の使用条件に同意する]** の横のチェック ボックスをオンにし、 **[購入]** を選択してクイック スタート テンプレートのデプロイを完了します。 
1. デプロイを監視するには、上部のナビゲーション バナーにある **[通知]** ベル アイコンからデプロイを選択するか、Azure portal で **[リソース グループ]** に移動し、 **[設定]** フィールドの **[デプロイ]** を選択して、'Microsoft.Template' デプロイを選びます。 

   >[!NOTE]
   > テンプレートのデプロイ中に指定された資格情報は、デプロイの期間中のみ保存されます。 デプロイの完了後、それらのパスワードは削除されるので、SQL Server VM をさらにクラスターに追加する必要が生じると、もう一度パスワードを指定するよう求められます。 


## <a name="step-2---manually-create-the-availability-group"></a>手順 2 - 可用性グループを手動で作成する 
[SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio)、[PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)、[Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql) のいずれかを使用して、通常どおりに可用性グループを手動で作成します。 

  >[!IMPORTANT]
  > 手順 4 で **101-sql-vm-aglistener-setup** クイック スタート テンプレートによって自動化されるため、この時点ではリスナーを作成**しない**でください。 

## <a name="step-3---manually-create-the-internal-load-balancer-ilb"></a>手順 3 - 内部ロード バランサー (ILB) を手動で作成する
Always On 可用性グループ (AG) リスナーには、内部 Azure Load Balancer (ILB) が必要です。 ILB では、高速フェールオーバーおよび再接続を可能にする AG リスナー用の "フローティング" IP アドレスが提供されます。 可用性グループ内の SQL Server VM が同じ可用性セットの一部である場合は、Basic Load Balancer を使用できます。それ以外の場合は、Standard Load Balancer を使用する必要があります。  **ILB は、SQL Server VM インスタンスと同じ vNet に存在する必要があります。** 作成する必要があるのは ILB のみです。構成の残りの部分 (バックエンド プール、正常性プローブ、負荷分散規則など) は手順 4 で **101-sql-vm-aglistener-setup** クイック スタート テンプレートによって処理されます。 

1. Azure ポータルで、SQL Server の仮想マシンを含んだリソース グループを開きます。 
2. リソース グループで **[追加]** をクリックします。
3. **ロード バランサー**を探し、検索結果から **[ロード バランサー]** (**Microsoft** によって発行されたもの) を選択します。
4. **[ロード バランサー]** ブレードで **[作成]** をクリックします。
5. **[ロード バランサーの作成]** ダイアログボックスで、次のようにロード バランサーを構成します。

   | Setting | 値 |
   | --- | --- |
   | **Name** |ロード バランサーを表すテキスト名 (例: **sqlLB**)。 |
   | **Type** |**内部**:ほとんどの実装では、内部ロード バランサーを使います。この場合、同じ仮想ネットワーク内のアプリケーションを可用性グループに接続できます。  </br> **外部**:アプリケーションをパブリック インターネット接続経由で可用性グループに接続できます。 |
   | **Virtual Network** | SQL Server インスタンスが存在する仮想ネットワークを選択します。 |
   | **サブネット** | SQL Server インスタンスが存在するサブネットを選択します。 |
   | **IP アドレスの割り当て** |**静的** |
   | **プライベート IP アドレス** | サブネット内の利用可能な IP アドレスを指定します。 |
   | **サブスクリプション** |複数のサブスクリプションを所有している場合、このフィールドが表示されます。 このリソースに関連付けられているサブスクリプションを選択します。 通常は、可用性グループのすべてのリソースについて同じサブスクリプションを選択してください。 |
   | **リソース グループ** |SQL Server インスタンスが存在するリソース グループを選択します。 |
   | **Location** |Azure において SQL Server インスタンスが存在する場所を選択します。 |
   | &nbsp; | &nbsp; |

6. **作成** を選択します。 


  >[!IMPORTANT]
  > 各 SQL Server VM 用のパブリック IP リソースには、Standard Load Balancer と互換性のある Standard SKU が必要です。 VM のパブリック IP リソースの SKU を決定するには、 **[リソース グループ]** に移動し、目的の SQL Server VM 用の **[パブリック IP アドレス]** リソースを選択し、 **[概要]** ウィンドウの **[SKU]** で値を見つけます。 

## <a name="step-4---create-the-ag-listener-and-configure-the-ilb-with-the-quickstart-template"></a>手順 4 - AG リスナーを作成し、クイック スタート テンプレートを使用して ILB を構成する

可用性グループ リスナーを作成し、Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/AvailabilityGroupListener リソースのプロビジョニング時に、**101-sql-vm-aglistener-setup** クイック スタート テンプレートを使用して自動的に内部ロード バランサー (ILB) を構成します。 **101-sql-vm-aglistener-setup** クイック スタート テンプレートでは、SQL VM リソース プロバイダーを介して、以下の操作を行います。

 - リスナーのために (デプロイ時に指定された IP アドレスの値に基づく) 新しいフロント エンド IP リソースを作成します。 
 - クラスターと ILB のネットワーク設定を構成する。 
 - ILB バックエンド プール、正常性プローブ、および負荷分散規則を構成する。
 - 特定の IP アドレスと名前を使用して、AG リスナーを作成する。
 
   >[!NOTE]
   > **101-sql-vm-aglistener-setup** は、Windows フェールオーバー クラスターが **101-sql-vm-ag-setup** テンプレートを使用して作成された場合にのみ使用できます。
   
   
ILB を構成し、AG リスナーを作成するには、次の操作を行います。
1. [**101-sql-vm-aglistener-setup**](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-aglistener-setup) クイック スタート テンプレートに移動し、 **[Azure へのデプロイ]** を選択して、Azure portal 内でクイック スタート テンプレートを起動します。
1. 必須フィールドに入力して ILB を構成し、可用性グループ リスナーを作成します。 省略可能なフィールドは空白のままでかまいません。 

    次の表には、テンプレートに必要な値が示されています。 

   | **フィールド** | 値 |
   | --- | --- |
   |**リソース グループ** | SQL Server VM と可用性グループが存在する、リソース グループ。 | 
   |**既存のフェールオーバー クラスター名** | SQL Server VM が参加しているクラスターの名前。 |
   | **既存の SQL 可用性グループ**| SQL Server VM が属する可用性グループの名前。 |
   | **既存の VM のリスト** | 前述の可用性グループに属する SQL Server VM の名前。 名前はコンマとスペースで区切る必要があります (例:SQLVM1, SQLVM2)。 |
   | **リスナー** | リスナーに割り当てたい DNS 名。 既定では、このテンプレートで 'aglistener' という名前が指定されますが、これは変更できます。 名前は 15 文字以下にしてください。 |
   | **リスナー ポート** | リスナーで使用するポート。 通常、このポートは既定のポートである 1433 にする必要があります。そのため、これはこのテンプレートで指定されたポート番号となります。 しかし、既定のポートが変更された場合、リスナー ポートでは代わりにその値を使用する必要があります。 | 
   | **リスナー IP** | リスナーで使用する IP。  この IP アドレスは、テンプレートのデプロイ中に作成されるので、未使用の IP アドレスを指定してください。  |
   | **既存のサブネット** | SQL Server VM の内部サブネットの*名前* (例: 既定値)。 この値は、 **[リソース グループ]** に移動し、 **[vNet]** を選択し、 **[設定]** ウィンドウで **[サブネット]** を選び、 **[名前]** で値をコピーすることで、決定できます。 |
   | **既存の内部ロード バランサー** | 手順 3 で作成した ILB の名前。 |
   | **プローブ ポート** | ILB で使用するプローブ ポート。 テンプレートでは既定で 59999 を使用しますが、この値は変更できます。 |
   | &nbsp; | &nbsp; |

1. 使用条件に同意する場合は、 **[上記の使用条件に同意する]** の横のチェック ボックスをオンにし、 **[購入]** を選択してクイック スタート テンプレートのデプロイを完了します。 
1. デプロイを監視するには、上部のナビゲーション バナーにある **[通知]** ベル アイコンからデプロイを選択するか、Azure portal で **[リソース グループ]** に移動し、 **[設定]** フィールドの **[デプロイ]** を選択して、'Microsoft.Template' デプロイを選びます。 

   >[!NOTE]
   >デプロイが途中で失敗した場合は、**101-sql-vm-aglistener-setup** クイック スタート テンプレートを再デプロイする前に、PowerShell を使用して、手動で[新たに作成されたリスナーを削除する](#remove-availability-group-listener)必要があります。 

## <a name="remove-availability-group-listener"></a>可用性グループ リスナーを削除する
テンプレートで構成された可用性グループ リスナーを後で削除する必要がある場合は、SQL VM リソース プロバイダーを経由する必要があります。 リスナーは SQL VM リソース プロバイダーを介して登録されるため、SQL Server Management Studio を使用して削除するだけでは十分ではありません。 実際には、PowerShell を使用し、SQL VM リソース プロバイダーを介して削除する必要があります。 そうすることで、SQL VM リソース プロバイダーから AG リスナー メタデータが削除され、可用性グループから物理的にリスナーが削除されます。 

次のコード スニペットでは、SQL リソース プロバイダーと、可用性グループの両方から SQL 可用性グループ リスナーを削除します。 

```powershell
# Remove the AG listener
# example: Remove-AzResource -ResourceId '/subscriptions/a1a11a11-1a1a-aa11-aa11-1aa1a11aa11a/resourceGroups/SQLAG-RG/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/Cluster/availabilitygrouplisteners/aglistener' -Force
Remove-AzResource -ResourceId '/subscriptions/<SubscriptionID>/resourceGroups/<resource-group-name>/providers/Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups/<cluster-name>/availabilitygrouplisteners/<listener-name>' -Force
```
 
## <a name="common-errors"></a>一般的なエラー
このセクションでは、いくつかの既知の問題とその考えられる解決策について説明します。 

### <a name="availability-group-listener-for-availability-group-ag-name-already-exists"></a>可用性グループ '\<AG 名>' の可用性グループ リスナーが既に存在する
AG リスナーの Azure クイック スタート テンプレートで使用されている、選択された可用性グループには、可用性グループ内に物理的に、または SQL VM リソース プロバイダー内のメタデータとして、リスナーが既に含まれています。  **101-sql-vm-aglistener-setup** クイック スタート テンプレートを再デプロイする前に、[PowerShell](#remove-availability-group-listener) を使用してリスナーを削除します。 

### <a name="connection-only-works-from-primary-replica"></a>プライマリ レプリカからの接続のみが機能する
**101-sql-vm-aglistener-setup** テンプレートのデプロイに失敗し、ILB 構成が不整合な状態のままになっている場合にこのような動作になる可能性があります。 バックエンド プールで可用性セットがリストされ、正常性プローブの規則と負荷分散規則が存在することを確認します。 何かが不足している場合、ILB 構成は不整合状態になっています。 

この動作を解決するには、[PowerShell](#remove-availability-group-listener) を使用してリスナーを削除し、Azure portal を介して内部ロード バランサーを削除し、再び手順 3 から始めます。 

### <a name="badrequest---only-sql-virtual-machine-list-can-be-updated"></a>BadRequest - SQL 仮想マシン リストしか更新できない
このエラーは、リスナーが SQL Server Management Studio (SSMS) を介して削除されたが、SQL VM リソース プロバイダーからは削除されなかった場合に **101-sql-vm-aglistener-setup** テンプレートをデプロイしたときに発生する可能性があります。 SSMS を介してリスナーを削除しても、SQL VM リソース プロバイダーからはリスナーのメタデータは削除されません。[PowerShell](#remove-availability-group-listener) を使用して、リソース プロバイダーからリスナーを削除する必要があります。 

### <a name="domain-account-does-not-exist"></a>ドメイン アカウントが存在しない
このエラーは、2 つの理由のいずれかが原因となる可能性があります。 指定されたドメイン アカウントが実際には存在しないか、[ユーザー プリンシパル名 (UPN)](/windows/desktop/ad/naming-properties#userprincipalname) のデータがありません。 **101-sql-vm-ag-setup** テンプレートでは、ドメイン アカウントが UPN 形式 (つまり user@domain.com) になっている必要がありますが、一部のドメイン アカウントがその形式で指定されていない可能性があります。 これは一般に、サーバーがドメイン コントローラーに昇格されたときにローカル ユーザーが最初のドメイン管理者アカウントに移行された場合や、ユーザーが PowerShell を使用して作成された場合に発生します。 

 アカウントが確かに存在することを確認してください。 その場合、2 番目の状況になっている可能性があります。 これを解決するには、次の操作を行ってください。

1. ドメイン コントローラーで、**サーバー マネージャー**の **[ツール]** オプションから **[Active Directory ユーザーとコンピューター]** ウィンドウを開きます。 
2. 左側のウィンドウで **[ユーザー]** を選択し、目的のアカウントに移動します。
3. 目的のアカウントを右クリックし、 **[プロパティ]** を選択します。
4. **[アカウント]** タブを選択し、 **[ユーザー ログオン名]** が空白かどうかを確認します。 その場合、これがエラーの原因です。 

    ![ユーザー アカウントが空白になっていることは、UPN が指定されていないことを示しています](media/virtual-machines-windows-sql-availability-group-quickstart-template/account-missing-upn.png)

5. ユーザーの名前と一致するように **[ユーザー ログオン名]** に入力し、ドロップダウンから適切なドメインを選択します。 
6. **[適用]** を選択して変更内容を保存し、 **[OK]** を選択してダイアログ ボックスを閉じます。 

   これらの変更を加えたら、もう一度 Azure クイック スタート テンプレートのデプロイを試みます。 



## <a name="next-steps"></a>次の手順

詳細については、次の記事を参照してください。 

* [SQL Server VM の概要](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server VM の FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server VM の料金ガイダンス](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server VM のリリース ノート](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [SQL Server VM のライセンス モデルの切り替え](virtual-machines-windows-sql-ahb.md)



