---
title: Azure 用の SAP LaMa コネクタ | Microsoft Docs
description: SAP LaMa を使用して Azure で SAP システムを管理する
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: MSSedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: sedusch
ms.openlocfilehash: 4772fdae06f23430d829fa411068b7af7a85b3dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101668709"
---
# <a name="sap-lama-connector-for-azure"></a>Azure 用の SAP LaMa コネクタ

[1877727]:https://launchpad.support.sap.com/#/notes/1877727
[2343511]:https://launchpad.support.sap.com/#/notes/2343511
[2350235]:https://launchpad.support.sap.com/#/notes/2350235
[2562184]:https://launchpad.support.sap.com/#/notes/2562184
[2628497]:https://launchpad.support.sap.com/#/notes/2628497
[2445033]:https://launchpad.support.sap.com/#/notes/2445033
[2815988]:https://launchpad.support.sap.com/#/notes/2815988
[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png
[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[hana-ops-guide]:hana-vm-operations.md

> [!NOTE]
> 一般的なサポートに関する声明:SAP LaMa または Azure コネクタに関するサポートが必要な場合は、必ずコンポーネント BC-VCM-LVM-HYPERV で SAP に関するインシデントを開始してください。

SAP LaMa は、SAP ランドスケープの操作と監視のために多くのお客様が使用します。 SAP LaMa 3.0 SP05 以降には、Azure へのコネクタが既定で付属しています。 このコネクタを使用すると、仮想マシンの割り当ての解除と起動、マネージド ディスクのコピーと再配置、およびマネージド ディスクの削除を行うことができます。 このような基本操作では、SAP LaMa を使用して SAP システムの再配置、コピー、複製、および更新を行うことができます。

このガイドでは、SAP LaMa 用の Azure コネクタを設定する方法、適応型 SAP システムのインストールに使用可能な仮想マシンを作成する方法、およびその仮想マシンを構成する方法について説明します。

> [!NOTE]
> このコネクタは SAP LaMa Enterprise Edition でのみ使用できます。

## <a name="resources"></a>リソース

次の SAP Note は Azure 上の SAP LaMa のトピックに関連します。

| Note 番号 | タイトル |
| --- | --- |
| [2343511] |Microsoft Azure connector for SAP Landscape Management (LaMa) (SAP Landscape Management (LaMa) 用の Microsoft Azure コネクタ) |
| [2350235] |SAP Landscape Management 3.0 - Enterprise edition (SAP Landscape Management 3.0 - Enterprise Edition) |

[SAP LaMa の SAP ヘルプ ポータル](https://help.sap.com/viewer/p/SAP_LANDSCAPE_MANAGEMENT_ENTERPRISE)もご覧ください。

## <a name="general-remarks"></a>一般的な注釈

* [Setup]\(セットアップ\) -> [Settings]\(設定\) -> [Engine]\(エンジン\) で *[Automatic Mountpoint Creation]\(マウント ポイントの自動作成\)* を有効にしてください。  
  SAP LaMa が仮想マシンで SAP Adaptive Extensions を使用してボリュームをマウントする場合は、この設定が無効な状態でマウント ポイントが存在する必要があります。

* 新しい VM および SAP インスタンスのデプロイが準備解除された場合は、IP アドレスが "盗まれる" のを防ぐために、個別のサブネットを使用して、動的 IP アドレスは使用しないでください。  
  サブネットで使用する動的 IP アドレスの割り当てが SAP LaMa でも使用されている場合は、SAP LaMa による SAP システムの準備が失敗することがあります。 SAP システムが準備解除された場合は、IP アドレスが予約されず、他の仮想マシンに割り当てられる可能性があります。

* マネージド ホストにサインインする場合は、ファイル システムのマウント解除がブロックされないようにしてください。  
  Linux 仮想マシンにサインインして作業ディレクトリをマウント ポイント内のディレクトリ (/usr/sap/AH1/ASCS00/exe など) に変更する場合は、ボリュームをマウント解除できず、再配置または準備解除が失敗します。

* SUSE SLES Linux 仮想マシンで CLOUD_NETCONFIG_MANAGE を無効にしてください。 詳細については、[SUSE KB 7023633](https://www.suse.com/support/kb/doc/?id=7023633) を参照してください。

## <a name="set-up-azure-connector-for-sap-lama"></a>SAP LaMa 用の Azure コネクタの設定

Azure コネクタは SAP LaMa 3.0 SP05 以降に付属しています。 SAP LaMa 3.0 用の最新のサポート パッケージとパッチを常にインストールすることをお勧めします。

Azure コネクタでは、Azure Resource Manager API を使用して Azure リソースが管理されます。 SAP LaMa では、サービス プリンシパルまたはマネージド ID を使用して、この API に対する認証を行うことができます。 SAP LaMa が Azure VM 上で実行されている場合は、「[マネージド ID を使用して Azure API にアクセスする](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d)」で説明されているように、マネージド ID を使用することをお勧めします。 サービス プリンシパルを使用する場合は、「[サービス プリンシパルを使用して Azure API にアクセスする](lama-installation.md#913c222a-3754-487f-9c89-983c82da641e)」の手順に従います。

### <a name="use-a-service-principal-to-get-access-to-the-azure-api"></a><a name="913c222a-3754-487f-9c89-983c82da641e"></a>サービス プリンシパルを使用して Azure API にアクセスする

Azure コネクタでは、サービス プリンシパルを使用して Microsoft Azure に対する承認を行うことができます。 次の手順に従って、SAP Landscape Management (LaMa) 用のサービス プリンシパルを作成してください。

1. [https://resources.azure.com](https://portal.azure.com ) に移動します
1. [Azure Active Directory] ブレードを開きます
1. [アプリの登録] をクリックします
1. [New registration]\(新規登録\) をクリックします
1. 名前を入力して [登録] をクリックします
1. 新しいアプリを選択し、[設定] タブで [証明書とシークレット] をクリックします
1. 新しいクライアント シークレットを作成し、新しいキーの説明を入力して、[when the secret should expire]\(シークレットの有効期限が切れたとき\) を選択し、[保存] をクリックします
1. 値をメモします。 この値は、サービス プリンシパルのパスワードとして使用します
1. アプリケーション ID をメモします。 この値は、サービス プリンシパルのユーザー名として使用します

既定では、サービス プリンシパルには、Azure のリソースにアクセスする権限はありません。 それらにアクセスするためのサービス プリンシパルの権限を付与する必要があります。

1. [https://resources.azure.com](https://portal.azure.com ) に移動します
1. [リソース グループ] ブレードを開きます
1. 使用するリソース グループを選択します
1. [アクセス制御 (IAM)] を選択します
1. [ロールの割り当ての追加] をクリックします
1. 共同作成者ロールを選択します
1. 上記で作成したアプリケーションの名前を入力します
1. [保存] をクリックします。
1. SAP LaMa で使用するすべてのリソース グループについて、手順 3 ～ 8 を繰り返します

### <a name="use-a-managed-identity-to-get-access-to-the-azure-api"></a><a name="af65832e-6469-4d69-9db5-0ed09eac126d"></a>マネージド ID を使用して Azure API にアクセスする

マネージド ID を使用できるようにするには、SAP LaMa インスタンスを、システムまたはユーザーによって割り当てられた ID を持つ Azure VM 上で実行する必要があります。 マネージド ID の詳細については、「[Azure リソースのマネージド ID とは](../../../active-directory/managed-identities-azure-resources/overview.md)」および「[Azure portal を使用して Azure VM で Azure リソースのマネージド ID を構成する](../../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)」を参照してください。

既定では、マネージド ID には、Azure のリソースにアクセスする権限はありません。 アクセス許可を付与する必要があります。

1. [https://resources.azure.com](https://portal.azure.com ) に移動します
1. [リソース グループ] ブレードを開きます
1. 使用するリソース グループを選択します
1. [アクセス制御 (IAM)] を選択します
1. [追加] > [ロールの割り当ての追加] をクリックします
1. 共同作成者ロールを選択します
1. [アクセスの割り当て先] で [仮想マシン] を選択します
1. SAP LaMa インスタンスが実行されている仮想マシンを選択します
1. [保存] をクリックします。
1. SAP LaMa で使用するすべてのリソース グループについて、この手順を繰り返します

SAP LaMa Azure コネクタの構成で、[Use Managed Identity]\(マネージド ID を使用する\) を選択して、マネージド ID の使用を有効にします。 システム割り当ての ID を使用する場合は、[ユーザー名] フィールドを空のままにしておきます。 ユーザー割り当ての ID を使用する場合は、[ユーザー名] フィールドにユーザー割り当ての ID を入力します。

### <a name="create-a-new-connector-in-sap-lama"></a>SAP LaMa で新しいコネクタを作成する

SAP LaMa の Web サイトを開き、[Infrastructure]\(インフラストラクチャ\) に移動します。 [Cloud Managers]\(クラウド マネージャー\) タブに移動して [Add]\(追加\) をクリックします。 [Microsoft Azure Cloud Adapter]\(Microsoft Azure クラウド アダプター\) を選択し、[Next]\(次へ\) をクリックします。 次の情報を入力します。

* Label (ラベル):コネクタ インスタンスの名前を選択します
* User Name (ユーザー名):仮想マシンのサービス プリンシパル アプリケーション ID またはユーザーによって割り当てられた ID。 詳細については、「システム割り当て ID またはユーザー割り当て ID の使用」を参照してください。
* Password (パスワード):サービス プリンシパルのキー/パスワード。 システムまたはユーザーによって割り当てられた ID を使用する場合は、このフィールドを空のままにすることができます。
* URL:既定の `https://management.azure.com/` を使用します
* Monitoring Interval (Seconds) (監視間隔 (秒)):300 以上にする必要があります
* Use Managed Identity (マネージド ID を使用する): SAP LaMa は、システムまたはユーザーによって割り当てられた ID を使用して、Azure API に対する認証を行うことができます。 このガイドの、「[マネージド ID を使用して Azure API にアクセスする](lama-installation.md#af65832e-6469-4d69-9db5-0ed09eac126d)」を参照してください。
* Subscription ID (サブスクリプション ID):Azure サブスクリプション ID
* Azure Active Directory Tenant ID (Azure Active Directory テナント ID):Active Directory テナントの ID
* Proxy host (プロキシ ホスト):プロキシのホスト名 (SAP LaMa でプロキシを使用してインターネットに接続する必要がある場合)
* Proxy port (プロキシ ポート):プロキシの TCP ポート
* Change Storage Type to save costs (ストレージの種類を変更してコストを節約する): ディスクが使用されていないときにコストを節約するために、Azure アダプターで Managed Disks のストレージの種類を変更する必要がある場合は、この設定を有効にします。 SAP インスタンスの構成で参照されているデータ ディスクの場合、アダプターによって、インスタンスが準備されていないときはディスクの種類が Standard Storage に変更され、インスタンスが準備されているときは元のストレージの種類に戻されます。 SAP LaMa で仮想マシンを停止すると、アダプターによって、接続されているすべてのディスク (OS ディスクを含む) のストレージの種類が Standard Storage に変更されます。 SAP LaMa で仮想マシンを起動すると、アダプターによって、ストレージの種類が元のストレージの種類に戻されます。

[Test Configuration]\(構成のテスト\) をクリックして入力を検証します。 Web サイトの下部に

Connection successful: (接続成功:)Connection to Microsoft cloud was successful. (Microsoft クラウドへの接続に成功しました。) 7 resource groups found (only 10 groups requested) (7 個のリソース グループが見つかりました (10 個のグループのみが要求されました))

と表示されます。

## <a name="provision-a-new-adaptive-sap-system"></a>新しい適応型 SAP システムのプロビジョニング

新しい仮想マシンを手動でデプロイするか、または[クイック スタート リポジトリ](https://github.com/Azure/azure-quickstart-templates)内にあるいずれかの Azure テンプレートを使用できます。 このリポジトリには、[SAP NetWeaver ASCS](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-ascs)、[SAP NetWeaver アプリケーション サーバー](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-apps)、および[データベース](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-lama-database)用のテンプレートが含まれています。 これらのテンプレートを使用すると、システム コピー/複製などの一部として新しいホストをプロビジョニングすることもできます。

新しい仮想マシンおよび SAP インスタンスのデプロイが準備解除された場合に IP アドレスが "盗まれる" のを防ぐために、SAP LaMa で管理するすべての仮想マシンについて個別のサブネットを使用し、動的 IP アドレスは使用しないことをお勧めします。

> [!NOTE]
> 仮想マシンからディスクをデタッチする処理にかかる時間が長くなるため、可能な限り、すべての仮想マシン拡張機能を削除してください。

同じ ID と gid を持つ \<hanasid>adm、\<sapsid>adm の各ユーザーおよび sapsys グループがターゲット マシンに存在することを確認するか、または LDAP を使用してください。 SAP NetWeaver (A)SCS の実行に使用する必要のある仮想マシンで NFS サーバーを有効にして起動します。

### <a name="manual-deployment"></a>手動によるデプロイ

SAP LaMa は、SAP Host Agent を使用して仮想マシンと通信します。 仮想マシンを手動でデプロイする場合や、クイック スタート リポジトリにある Azure Resource Manager テンプレートを使用しない場合は、最新の SAP Host Agent と SAP Adaptive Extensions をインストールしてください。 Azure に必要なパッチ レベルについて詳しくは、SAP Note [2343511] をご覧ください。

#### <a name="manual-deployment-of-a-linux-virtual-machine"></a>Linux 仮想マシンの手動によるデプロイ

SAP Note [2343511] に記載されているいずれかのサポート対象のオペレーティング システムで新しい仮想マシンを作成します。 SAP インスタンス用の IP 構成を追加します。 各インスタンスには、少なくとも 1 つの IP アドレスが必要です。仮想ホスト名を使用してインスタンスをインストールしてください。

SAP NetWeaver ASCS インスタンスには、/sapmnt/\<SAPSID>、/usr/sap/\<SAPSID>、/usr/sap/trans、および /usr/sap/\<sapsid>adm 用のディスクが必要です。 SAP NetWeaver アプリケーション サーバーには追加のディスクは必要ありません。 SAP インスタンスに関連するすべての項目は ASCS に格納する必要があり、NFS を使用してエクスポートされます。 それ以外の場合、現時点では SAP LaMa を使用してアプリケーション サーバーを追加することができません。

![Linux 上の SAP NetWeaver ASCS](media/lama/sap-lama-ascs-app-linux.png)

#### <a name="manual-deployment-for-sap-hana"></a>SAP HANA 用の手動によるデプロイ

SAP Note [2343511] に記載されている SAP HANA 用のいずれかのサポート対象のオペレーティング システムで新しい仮想マシンを作成します。 SAP HANA と HANA テナント用に 1 つずつ IP 構成を追加します。

SAP HANA には、/hana/shared、/hana/backup、/hana/data、および /hana/log 用のディスクが必要です。

![Linux 上の SAP HANA](media/lama/sap-lama-db-hana.png)

#### <a name="manual-deployment-for-oracle-database-on-linux"></a>Linux への Oracle Database の手動によるデプロイ

SAP Note [2343511] に記載されている Oracle Database 用のいずれかのサポート対象のオペレーティング システムで新しい仮想マシンを作成します。 Oracle Database 用の IP 構成を 1 つ追加します。

Oracle Database には、/oracle、/home/oraod1、および /home/oracle 用のディスクが必要です。

![Linux 上の Oracle データベースと、必要なディスクを示す図。](media/lama/sap-lama-db-ora-lnx.png)

#### <a name="manual-deployment-for-microsoft-sql-server"></a>Microsoft SQL Server 用の手動によるデプロイ

SAP Note [2343511] に記載されている Microsoft SQL Server 用のいずれかのサポート対象のオペレーティング システムで新しい仮想マシンを作成します。 SQL Server インスタンス用の IP 構成を 1 つ追加します。

SQL Server データベース サーバーには、データベース データ用のディスクおよび c:\usr\sap 用のログ ファイルとディスクが必要です。

![Linux 上の Oracle Database](media/lama/sap-lama-db-sql.png)

SAP NetWeaver アプリケーション サーバーの再配置またはシステム コピー/複製先として使用する仮想マシンに、サポート対象の Microsoft ODBC Driver for SQL Server をインストールしてください。

SAP LaMa は SQL Server 自体を再配置できないため、データベース インスタンスの再配置またはシステム コピー/複製先として使用する仮想マシンには SQL Server をプレインストールしておく必要があります。

### <a name="deploy-virtual-machine-using-an-azure-template"></a>Azure テンプレートを使用した仮想マシンのデプロイ

仮想マシンのオペレーティング システム用の [SAP Software Marketplace](https://support.sap.com/swdc) から、次に示す入手可能な最新のアーカイブをダウンロードします。

1. SAPCAR 7.21
1. SAP HOST AGENT 7.21
1. SAP ADAPTIVE EXTENSION 1.0 EXT

また、[Microsoft ダウンロード センター](https://www.microsoft.com/download)から次のコンポーネントをダウンロードします。

1. Microsoft Visual C++ 2010 再頒布可能パッケージ (x64) (Windows のみ)
1. Microsoft ODBC Driver for SQL Server (SQL Server のみ)

これらのコンポーネントではテンプレートをデプロイする必要があります。 テンプレートでコンポーネントを使用できるようにするための最も簡単な方法としては、コンポーネントを Azure ストレージ アカウントにアップロードして Shared Access Signature (SAS) を作成します。

テンプレートには以下のパラメーターがあります。

* sapSystemId:SAP システム ID。 ディスク レイアウトの作成に使用します (例: /usr/sap/\<sapsid>)。

* computerName:新しい仮想マシンのコンピューター名。 このパラメーターは SAP LaMa でも使用されます。 このテンプレートを使用して、新しい仮想マシンをシステム コピーの一部としてプロビジョニングすると、SAP LaMa はこのコンピューター名を持つホストに到達するまで待機します。

* osType:デプロイするオペレーティング システムの種類。

* dbtype:データベースの種類。 このパラメーターは、追加する必要のある IP 構成の数とディスク レイアウトの内容を決定するために使用します。

* sapSystemSize:デプロイする SAP システムのサイズ。 仮想マシン インスタンスの種類とサイズを決定するために使用します。

* adminUsername:仮想マシンのユーザー名。

* adminPassword:仮想マシンのパスワード。 SSH の公開キーを指定することもできます。

* sshKeyData:仮想マシンの公開 SSH キー。 Linux オペレーティング システムでのみサポートされています。

* subnetId:使用するサブネットの ID。

* deployEmptyTarget:仮想マシンをインスタンスの再配置または同様の操作のターゲットとして使用する場合は、空のターゲットをデプロイできます。 この場合、追加のディスクや IP 構成はアタッチされません。

* sapcarLocation:デプロイするオペレーティング システムに一致する sapcar アプリケーションの場所。 sapcar は、他のパラメーターで指定するアーカイブの抽出に使用します。

* sapHostAgentArchiveLocation:SAP Host Agent アーカイブの場所。 SAP Host Agent は、このテンプレートのデプロイの一部としてデプロイされます。

* sapacExtLocation:SAP Adaptive Extensions の場所。 Azure に必要な最小のパッチ レベルは SAP Note [2343511] に記載されています。

* vcRedistLocation:SAP Adaptive Extensions のインストールに必要な VC ランタイムの場所。 このパラメーターは Windows でのみ必須です。

* odbcDriverLocation:インストールする ODBC ドライバーの場所。 Microsoft ODBC Driver for SQL Server のみサポートされています。

* sapadmPassword:sapadm ユーザーのパスワード。

* sapadmId:sapadm ユーザーの Linux ユーザー ID。 Windows の場合は必要ありません。

* sapsysGid:sapsys グループの Linux グループ ID。 Windows の場合は必要ありません。

* _artifactsLocation:このテンプレートで必要とされる成果物が配置されるベース URI。 付属のスクリプトを使用してテンプレートをデプロイする場合は、サブスクリプション内のプライベートな場所が使用され、この値が自動的に生成されます。 GitHub からテンプレートを展開しない場合にのみ必要です。

* _artifactsLocationSasToken:_artifactsLocation にアクセスするために必要な sasToken。 付属のスクリプトを使用してテンプレートをデプロイする場合は、sasToken が自動的に生成されます。 GitHub からテンプレートを展開しない場合にのみ必要です。

### <a name="sap-hana"></a>SAP HANA

以下の例では、システム ID が HN1 の SAP HANA およびシステム ID が AH1 の SAP NetWeaver システムをインストールしていることを前提としています。 仮想ホスト名は、HANA インスタンスについては hn1-db、SAP NetWeaver システムで使用される HANA テナントについては ah1-db、SAP NetWeaver ASCS については ah1-ascs、1 台目の SAP NetWeaver アプリケーション サーバーについては ah1-di-0 です。

#### <a name="install-sap-netweaver-ascs-for-sap-hana-using-azure-managed-disks"></a>Azure Managed Disks を使用して SAP NetWeaver ASCS for SAP HANA をインストールする

SAP Software Provisioning Manager (SWPM) を起動する前に、ASCS の仮想ホスト名の IP アドレスをマウントする必要があります。 sapacext を使用することをお勧めします。 sapacextを使用して IP アドレスをマウントする場合は、再起動後に IP アドレスを再マウントしてください。

![Linux ロゴ。][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-ascs -n 255.255.255.128
```

![Windows ロゴ。][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-ascs -n 255.255.255.128
```

SWPM を実行し、 *[ASCS Instance Host Name]\(ASCS インスタンス ホスト名\)* として *ah1-ascs* を使用します。

![Linux ロゴ。][Logo_Linux] Linux  
次のプロファイル パラメーターを SAP Host Agent プロファイルに追加します。このプロファイルは /usr/sap/hostctrl/exe/host_profile にあります。 詳しくは、SAP Note [2628497] をご覧ください。
```
acosprep/nfs_paths=/home/ah1adm,/usr/sap/trans,/sapmnt/AH1,/usr/sap/AH1
```

#### <a name="install-sap-netweaver-ascs-for-sap-hana-on-azure-netappfiles-anf-beta"></a>Azure NetAppFiles (ANF) BETA にSAP NetWeaver ASCS for SAP HANA をインストールする

> [!NOTE]
> この機能はまだ GA ではありません。 詳細については、SAP Note [2815988] を参照してください (プレビュー版のお客様にのみ表示されます)。
コンポーネント BC-VCM-LVM-HYPERV 上の SAP インシデントを開き、Azure NetApp Files 用の LaMa ストレージ アダプターへの参加を要求します

ANF は Azure に NFS を提供します。 SAP LaMa のコンテキストでは、これによりABAP Central Services (ASCS) インスタンスの作成とそれ以降のアプリケーション サーバーのインストールが簡単になります。 以前は、ASCS インスタンスは NFS サーバーとしても動作する必要がありました。また、パラメーター acosprep/nfs_paths を SAP Hostagent の host_profile に追加する必要がありました。

#### <a name="anf-is-currently-available-in-these-regions"></a>現在 ANF を使用できるリージョン:

オーストラリア東部、米国中部、米国東部、米国東部 2、北ヨーロッパ、米国中南部、西ヨーロッパ、米国西部 2。

#### <a name="network-requirements"></a>ネットワークの要件

ANF には、SAP サーバーと同じ VNET に属している必要がある委任サブネットが必要です。 そのような構成の例を次に示します。
この画面は、VNET と最初のサブネットの作成を示しています。

![SAP LaMa で Azure ANF 用の仮想ネットワークを作成する ](media/lama/sap-lama-createvn-50.png)

次の手順では、Microsoft.NetApp/ボリューム用の委任サブネットを作成します。

![SAP LaMa で委任サブネットを追加する ](media/lama/sap-lama-addsubnet-50.png)

![SAP LaMa のサブネット一覧 ](media/lama/sap-lama-subnets.png)

次は、NetApp アカウントを Azure portal で作成する必要があります。

![SAP LaMa で NetApp アカウントを作成する ](media/lama/sap-lama-create-netappaccount-50.png)

![作成された SAP LaMa NetApp アカウント ](media/lama/sap-lama-netappaccount.png)

NetApp アカウント内で、容量プールには各プールのディスクのサイズと種類を指定します。

![SAP LaMa で NetApp 容量プールを作成する ](media/lama/sap-lama-capacitypool-50.png)

![作成された SAP LaMa NetApp 容量プール ](media/lama/sap-lama-capacitypool-list.png)

これで NFS ボリュームを定義できるようになります。 1 つのプールに複数のシステムのボリュームがあるため、わかりやすい名前付けスキームを選択するようにします。 SID を追加すると、関連するボリュームをグループ化しやすくなります。 ASCS および AS インスタンスの場合、 */sapmnt/\<SID\>* 、 */usr/sap/\<SID\>* 、および */home/\<sid\>adm* のマウントが必要です。 必要に応じて、一元的なトランスポート ディレクトリ用の */usr/sap/trans* が必要です。これは、少なくとも、1 つのランドスケープのすべてのシステムで使用されます。

> [!NOTE]
> ベータ段階では、ボリュームの名前はサブスクリプション内で一意にする必要があります。

![SAP LaMa でボリューム 1 を作成する ](media/lama/sap-lama-createvolume-80.png)

![SAP LaMa でボリューム 2 を作成する ](media/lama/sap-lama-createvolume2-80.png)

![SAP LaMa でボリューム 3 を作成する ](media/lama/sap-lama-createvolume3-80.png)

他のボリュームについてもこれらの手順を繰り返す必要があります。

![SAP LaMa の作成されたボリュームの一覧 ](media/lama/sap-lama-volumes.png)

これらのボリュームは、SAP SWPM を使用した最初のインストールが実行されるシステムにマウントする必要があります。

まずマウント ポイントを作成する必要があります。 この場合、SID は AN1 なので、次のコマンドを実行する必要があります。

```bash
mkdir -p /home/an1adm
mkdir -p /sapmnt/AN1
mkdir -p /usr/sap/AN1
mkdir -p /usr/sap/trans
```
次に、次のコマンドを使用して ANF ボリュームをマウントします。

```bash
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-home-sidadm /home/an1adm
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-sapmnt-sid /sapmnt/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/an1-usr-sap-sid /usr/sap/AN1
# sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 9.9.9.132:/global-usr-sap-trans /usr/sap/trans
```
マウント コマンドはポータルからも取得できます。 ローカル マウント ポイントを調整する必要があります。

確認するには、df -h コマンドを使用します。

![SAP LaMa マウント ポイントの OS レベル ](media/lama/sap-lama-mounts.png)

次は、SWPM を使用したインストールを実行する必要があります。

少なくとも 1 つの AS インスタンスに対して同じ手順を実行する必要があります。

インストールが成功したら、SAP LaMa 内で システムを検出する必要があります。

ASCS および AS インスタンスのマウント ポイントは次のようになります。

![LaMa の SAP LaMa マウント ポイント](media/lama/sap-lama-ascs.png) (これは例です。 IP アドレスとエクスポート パスは、以前のものとは異なります)


#### <a name="install-sap-hana"></a>SAP HANA のインストール

コマンド ライン ツール hdblcm を使用して SAP HANA をインストールする場合は、--hostname パラメーターを使用して仮想ホスト名を指定します。 データベースの仮想ホスト名の IP アドレスをネットワーク インターフェイスに追加する必要があります。 sapacext を使用することをお勧めします。 sapacextを使用して IP アドレスをマウントする場合は、再起動後に IP アドレスを再マウントしてください。

HANA テナントに接続するアプリケーション サーバーで使用される名前には、別の仮想ホスト名と IP アドレスを追加してください。

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h hn1-db -n 255.255.255.128
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-db -n 255.255.255.128
```

HANA 仮想マシンではなくアプリケーション サーバー仮想マシンで SWPM のデータベース インスタンスのインストールを実行します。 *[Database for SAP System]\(SAP システム用のデータベース\)* ダイアログの *[Database Host]\(データベース ホスト\)* として *ah1-db* を使用します。

#### <a name="install-sap-netweaver-application-server-for-sap-hana"></a>SAP HANA 用の SAP NetWeaver アプリケーション サーバーのインストール

SAP Software Provisioning Manager (SWPM) を起動する前に、アプリケーション サーバーの仮想ホスト名の IP アドレスをマウントする必要があります。 sapacext を使用することをお勧めします。 sapacextを使用して IP アドレスをマウントする場合は、再起動後に IP アドレスを再マウントしてください。

![Linux ロゴ。][Logo_Linux] Linux

```bash
# /usr/sap/hostctrl/exe/sapacext -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
/usr/sap/hostctrl/exe/sapacext -a ifup -i eth0 -h ah1-di-0 -n 255.255.255.128
```

![Windows ロゴ。][Logo_Windows] Windows

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h ah1-di-0 -n 255.255.255.128
```

SAP NetWeaver プロファイル パラメーター dbs/hdb/hdb_use_ident を使用して、HDB ユーザーストアでキーの検出に使用する ID を設定することをお勧めします。 SWPM によるデータベース インスタンスのインストール後または SWPM の実行後に、このパラメーターを手動で追加することもできます。

```bash
# from https://blogs.sap.com/2015/04/14/sap-hana-client-software-different-ways-to-set-the-connectivity-data/
/sapdb/DVDs/IM_LINUX_X86_64/sapinst HDB_USE_IDENT=SYSTEM_COO
```

このパラメーターを手動で設定する場合は、HDB ユーザーストア エントリの新規作成も必要です。

```bash
# run as <sapsid>adm
/usr/sap/AH1/hdbclient/hdbuserstore LIST
# reuse the port that was listed from the command above, in this example 35041
/usr/sap/AH1/hdbclient/hdbuserstore SET DEFAULT ah1-db:35041@AH1 SAPABAP1 <password>
```

*[Primary Application Server Instance]\(プライマリ アプリケーション サーバー インスタンス\)* ダイアログの *[PAS Instance Host Name]\(PAS インスタンス ホスト名\)* として *ah1-di-0* を使用します。

#### <a name="post-installation-steps-for-sap-hana"></a>SAP HANA のインストール後の手順

テナント コピー、テナント移動、またはシステム レプリケーションの作成を行う前に、SYSTEMDB とすべてのテナント データベースをバックアップしてください。

### <a name="microsoft-sql-server"></a>Microsoft SQL Server

以下の例では、システム ID が AS1 の SAP NetWeaver システムをインストールしていることを前提としています。 仮想ホスト名は、SAP NetWeaver システムで使用される SQL Server インスタンスについては as1-db、SAP NetWeaver ASCS については as1-ascs、1 台目の SAP NetWeaver アプリケーション サーバーについては as1-di-0 です。

#### <a name="install-sap-netweaver-ascs-for-sql-server"></a>SQL Server 用の SAP NetWeaver ASCS のインストール

SAP Software Provisioning Manager (SWPM) を起動する前に、ASCS の仮想ホスト名の IP アドレスをマウントする必要があります。 sapacext を使用することをお勧めします。 sapacextを使用して IP アドレスをマウントする場合は、再起動後に IP アドレスを再マウントしてください。

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-ascs -n 255.255.255.128
```

SWPM を実行し、 *[ASCS Instance Host Name]\(ASCS インスタンス ホスト名\)* として *as1-ascs* を使用します。

#### <a name="install-sql-server"></a>SQL Server をインストールする

データベースの仮想ホスト名の IP アドレスをネットワーク インターフェイスに追加する必要があります。 sapacext を使用することをお勧めします。 sapacextを使用して IP アドレスをマウントする場合は、再起動後に IP アドレスを再マウントしてください。

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-db -n 255.255.255.128
```

SQL Server 仮想マシンで SWPM のデータベース インスタンスのインストールを実行します。 SAPINST_USE_HOSTNAME=*as1-db* を使用して、SQL Server への接続に使用するホスト名をオーバーライドします。 Azure Resource Manager テンプレートを使用して仮想マシンをデプロイした場合は、データベース データ ファイルに使用するディレクトリを *C:\sql\data*、データベース ログ ファイルに使用するディレクトリを *C:\sql\log* に設定してください。

ユーザー *NT AUTHORITY\SYSTEM* が SQL Server にアクセスできること、およびサーバー ロール *sysadmin* が割り当てられていることを確認します。 詳しくは、SAP Note [1877727] および [2562184] をご覧ください。

#### <a name="install-sap-netweaver-application-server"></a>SAP NetWeaver アプリケーション サーバーのインストール

SAP Software Provisioning Manager (SWPM) を起動する前に、アプリケーション サーバーの仮想ホスト名の IP アドレスをマウントする必要があります。 sapacext を使用することをお勧めします。 sapacextを使用して IP アドレスをマウントする場合は、再起動後に IP アドレスを再マウントしてください。

```bash
# C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i <network interface> -h <virtual hostname or IP address> -n <subnet mask>
C:\Program Files\SAP\hostctrl\exe\sapacext.exe -a ifup -i "Ethernet 3" -h as1-di-0 -n 255.255.255.128
```

*[Primary Application Server Instance]\(プライマリ アプリケーション サーバー インスタンス\)* ダイアログの *[PAS Instance Host Name]\(PAS インスタンス ホスト名\)* として *as1-di-0* を使用します。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="errors-and-warnings-during-discover"></a>検出時のエラーと警告

* SELECT 権限が拒否されました
  * [Microsoft][ODBC SQL Server Driver][SQL Server]SELECT 権限がオブジェクト 'log_shipping_primary_databases'、データベース 'msdb'、スキーマ 'dbo' で拒否されました。 [SOAPFaultException]  
  SELECT 権限がオブジェクト 'log_shipping_primary_databases'、データベース 'msdb'、スキーマ 'dbo' で拒否されました。
  * 解決策  
    *NT AUTHORITY\SYSTEM* が SQL Server にアクセスできることを確認してください。 SAP Note [2562184] をご覧ください。


### <a name="errors-and-warnings-for-instance-validation"></a>インスタンスの検証に関するエラーと警告

* HDB ユーザーストアの検証で例外が発生しました  
  * ログ ビューアーを参照  
    com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException:Exception in validator with ID 'RuntimeHDBConnectionValidator' (Validation:'VALIDATION_HDB_USERSTORE'):Could not retrieve the hdbuserstore (com.sap.nw.lm.aci.monitor.api.validation.RuntimeValidationException: ID 'RuntimeHDBConnectionValidator' でのバリデータの例外 (検証: 'VALIDATION_HDB_USERSTORE'): hdbuserstore を取得できませんでした)  
    HANA userstore is not in the correct location (HANA userstore が正しい場所にありません)
  * 解決策  
    /usr/sap/AH1/hdbclient/install/installation.ini が正しいことを確認してください。

### <a name="errors-and-warnings-during-a-system-copy"></a>システム コピー時のエラーと警告

* システムのプロビジョニング手順の検証中にエラーが発生しました
  * 原因: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r の呼び出し
  * 解決策  
    ソースの HANA システムですべてのデータベースのバックアップを作成してください。

* データベース インスタンスのシステム コピー手順の *開始*
  * Host Agent Operation '000D3A282BC91EE8A1D76CF1F92E2944' failed (OperationException. FaultCode:'127', Message:'Command execution failed. : [Microsoft][ODBC SQL Server Driver][SQL Server]User does not have permission to alter database 'AS2', the database does not exist, or the database is not in a state that allows access checks.') (ホスト エージェント操作 '000D3A282BC91EE8A1D76CF1F92E2944' に失敗しました (OperationException. FaultCode: '127', Message: 'コマンドの実行に失敗しました。: [Microsoft][ODBC SQL Server Driver][SQL Server]ユーザーにデータベース 'AS2' を変更するアクセス許可がないか、データベースが存在しないか、データベースがアクセス チェックできない状態です。'))
  * 解決策  
    *NT AUTHORITY\SYSTEM* が SQL Server にアクセスできることを確認してください。 SAP Note [2562184] をご覧ください。

### <a name="errors-and-warnings-during-a-system-clone"></a>システム複製時のエラーと警告

* アプリケーション サーバーまたは ASCS の *[Forced Register and Start Instance Agent]\(インスタンス エージェントの強制登録および起動\)* の手順でインスタンス エージェントを登録するときにエラーが発生しました
  * Error occurred when trying to register instance agent. (インスタンス エージェントの登録中にエラーが発生しました。) (RemoteException:'Failed to load instance data from profile '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0':Cannot access profile '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0':No such file or directory.') (RemoteException: 'プロファイル 'as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0' からインスタンス データを読み込めませんでした: プロファイル 'as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0' にアクセスできません: ファイルまたはディレクトリが存在しません。')
  * 解決策  
   ASCS/SCS 上の sapmnt 共有に SAP_AS1_GlobalAdmin のフル アクセスがあることを確認してください。

* *[Enable Startup Protection for Clone]\(複製に対するスタートアップ保護の有効化\)* の手順におけるエラー
  * Failed to open file '\\as1-ascs\sapmnt\AS1\SYS\profile\AS1_D00_as1-di-0' Cause:を開けませんでした 原因: ファイルまたはディレクトリが存在しません
  * 解決策  
    アプリケーション サーバーのコンピューター アカウントにはプロファイルへの書き込みアクセスが必要です。

### <a name="errors-and-warnings-during-create-system-replication"></a>システム レプリケーションの作成時のエラーと警告

* [Create System Replication]\(システム レプリケーションの作成\) をクリックしたときの例外
  * 原因: com.sap.nw.lm.aci.engine.base.api.util.exception.HAOperationException   '/usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r' | /usr/sap/hostctrl/exe/sapacext -a ShowHanaBackups -m HN1 -f 50 -h hn1-db -o level=0\;status=5\;port=35013 pf=/usr/sap/hostctrl/exe/host_profile -R -T dev_lvminfo -u SYSTEM -p hook -r の呼び出し
  * 解決策  
    sapacext を `<hanasid`>adm として実行できるかどうかをテストしてください。

* [Storage]\(ストレージ\) の手順でフル コピーが無効な場合のエラー
  * An error occurred when reporting a context attribute message for path IStorageCopyData.storageVolumeCopyList:1 and field targetStorageSystemId (パス IStorageCopyData.storageVolumeCopyList:1 およびフィールド targetStorageSystemId のコンテキスト属性メッセージの報告時にエラーが発生しました)
  * 解決策  
    この手順の警告は無視してやり直してください。 この問題は、SAP LaMa の新しいサポート パッケージ/パッチで修正される予定です。

### <a name="errors-and-warnings-during-relocate"></a>再配置時のエラーと警告

* nfs の再エクスポートでパス '/usr/sap/AH1' が許可されていません。
  * 詳しくは、SAP Note [2628497] を確認してください。
  * 解決策  
    ASCS HostAgent プロファイルに ASCS エクスポートを追加します。 SAP Note [2628497] をご覧ください。

* ASCS の再配置時に関数が実装されていません
  * コマンド出力: exportfs: host:/usr/sap/AX1:Function not implemented (関数が実装されていません)
  * 解決策  
    再配置先の仮想マシンで NFS サーバー サービスが有効になっていることを確認してください。

### <a name="errors-and-warnings-during-application-server-installation"></a>アプリケーション サーバーのインストール時のエラーと警告

* SAPinst 手順の実行中のエラー: getProfileDir
  * エラー: (この手順から報告される最後のエラー:Caught ESAPinstException in module call:Validator of step '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_readProfileDir|ind|ind|ind|ind|readProfile|0|getProfileDir' reported an error:Node \\\as1-ascs\sapmnt\AS1\SYS\profile does not exist. (モジュール呼び出しで ESAPinstException がキャッチされました: 手順のバリデータ '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_readProfileDir|ind|ind|ind|ind|readProfile|0|getProfileDir' からエラーが報告されました: ノード\\as1-ascs\sapmnt\AS1\SYS\profile が存在しません。) Start SAPinst in interactive mode to solve this problem (モジュール呼び出しで ESAPinstException がキャッチされました: 手順のバリデータ '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_readProfileDir|ind|ind|ind|ind|readProfile|0|getProfileDir' からエラーが報告されました: ノード \as1-ascs\sapmnt\AS1\SYS\profile は存在しません。対話モードで SAPinst を開始してこの問題を解決してください))
  * 解決策  
    プロファイルへのアクセス権を持つユーザーによって SWPM が実行されていることを確認してください。 このユーザーはアプリケーション サーバーのインストール ウィザードで構成できます。

* SAPinst 手順の実行中のエラー: askUnicode
  * エラー: (この手順から報告される最後のエラー:Caught ESAPinstException in module call:Validator of step '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_getUnicode|ind|ind|ind|ind|unicode|0|askUnicode' reported an error:Start SAPinst in interactive mode to solve this problem (モジュール呼び出しで ESAPinstException がキャッチされました: 手順のバリデータ '|NW_DI|ind|ind|ind|ind|0|0|NW_GetSidFromProfiles|ind|ind|ind|ind|getSid|0|NW_readProfileDir|ind|ind|ind|ind|readProfile|0|getProfileDir' からエラーが報告されました: ノード \as1-ascs\sapmnt\AS1\SYS\profile は存在しません。対話モードで SAPinst を開始してこの問題を解決してください))
  * 解決策  
    最新の SAP カーネルを使用すると、SWPM では、システムが Unicode システムであるかどうかを ASCS のメッセージ サーバーを使用して判断できなくなります。 詳しくは、SAP Note [2445033] をご覧ください。  
    この問題は、SAP LaMa の新しいサポート パッケージ/パッチで修正される予定です。  
    この問題を回避するには、SAP システムの既定のプロファイルでプロファイル パラメーター OS_UNICODE=uc を設定します。

* SAPinst 手順の実行中のエラー: dCheckGivenServer
  * SAPinst 手順の実行中のエラー: dCheckGivenServer" version="1.0" ERROR:(この手順から報告される最後のエラー:\<p> インストールはユーザーによってキャンセルされました。 \</p>
  * 解決策  
    プロファイルへのアクセス権を持つユーザーによって SWPM が実行されていることを確認してください。 このユーザーはアプリケーション サーバーのインストール ウィザードで構成できます。

* SAPinst 手順の実行中のエラー: checkClient
  * SAPinst 手順の実行中のエラー: checkClient" version="1.0" ERROR:(この手順から報告される最後のエラー:\<p> インストールはユーザーによってキャンセルされました。 \</p>)
  * 解決策  
    アプリケーション サーバーをインストールする仮想マシンに Microsoft ODBC Driver for SQL Server がインストールされていることを確認してください。

* SAPinst 手順の実行中のエラー: copyScripts
  * この手順から報告される最後のエラー:System call failed. (システムの呼び出しに失敗しました。) DETAILS:Error 13 (0x0000000d) (Permission denied) in execution of system call 'fopenU' with parameter (\\\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w), line (494) in file (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp), stack trace: (詳細: エラー 13 (0x0000000d) (アクセス許可が拒否されました) パラメーター (\as1-ascs/sapmnt/AS1/SYS/exe/uc/NTAMD64/strdbs.cmd, w) を指定してシステム呼び出し 'fopenU' の実行中、行 (494)、ファイル (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/filesystem/syxxcfstrm2.cpp)、スタック トレース:)  
  CThrThread.cpp:85:CThrThread::threadFunction()  
  CSiServiceSet.cpp:63:CSiServiceSet::executeService()  
  CSiStepExecute.cpp:913:CSiStepExecute::execute()  
  EJSController.cpp:179:EJSControllerImpl::executeScript()  
  JSExtension.hpp:1136:CallFunctionBase::call()  
  iaxxcfile.cpp:183: iastring CIaOsFileConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcfile.cpp:1849: iastring CIaOsFileConnect::newFileStream(args_t const& _args)  
  iaxxbfile.cpp:773:CIaOsFile::newFileStream_impl(4)  
  syxxcfile.cpp:233:CSyFileImpl::openStream(ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp:29:CSyFileStreamImpl::CSyFileStreamImpl(CSyFileStream*,iastring,ISyFile::eFileOpenMode)  
  syxxcfstrm.cpp:265:CSyFileStreamImpl::open()  
  syxxcfstrm2.cpp:58:CSyFileStream2Impl::CSyFileStream2Impl(const CSyPath & \\\aw1-ascs/sapmnt/AW1/SYS/exe/uc/NTAMD64/strdbs.cmd, 0x4)  
  syxxcfstrm2.cpp:456:CSyFileStream2Impl::open()
  * 解決策  
    プロファイルへのアクセス権を持つユーザーによって SWPM が実行されていることを確認してください。 このユーザーはアプリケーション サーバーのインストール ウィザードで構成できます。

* SAPinst 手順の実行中のエラー: askPasswords
  * この手順から報告される最後のエラー:System call failed. (システムの呼び出しに失敗しました。) DETAILS:Error 5 (0x00000005) (Access is denied.) in execution of system call 'NetValidatePasswordPolicy' with parameter (...), line (359) in file (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp), stack trace: (詳細: エラー (0x00000005) (アクセス許可が拒否されました) パラメーター (...) を指定してシステム呼び出し 'NetValidatePasswordPolicy' の実行中、行 (359)、ファイル (\bas/bas/749_REL/bc_749_REL/src/ins/SAPINST/impl/src/syslib/account/synxcaccmg.cpp)、スタック トレース:)  
  CThrThread.cpp:85:CThrThread::threadFunction()  
  CSiServiceSet.cpp:63:CSiServiceSet::executeService()  
  CSiStepExecute.cpp:913:CSiStepExecute::execute()  
  EJSController.cpp:179:EJSControllerImpl::executeScript()  
  JSExtension.hpp:1136:CallFunctionBase::call()  
  CSiStepExecute.cpp:764:CSiStepExecute::invokeDialog()  
  DarkModeGuiEngine.cpp:56:DarkModeGuiEngine::showDialogCalledByJs()  
  DarkModeDialog.cpp:85:DarkModeDialog::submit()  
  EJSController.cpp:179:EJSControllerImpl::executeScript()  
  JSExtension.hpp:1136:CallFunctionBase::call()  
  iaxxcaccount.cpp:107: iastring CIaOsAccountConnect::callMemberFunction(iastring const& name, args_t const& args)  
  iaxxcaccount.cpp:1186: iastring CIaOsAccountConnect::validatePasswordPolicy(args_t const& _args)  
  iaxxbaccount.cpp:430:CIaOsAccount::validatePasswordPolicy_impl()  
  synxcaccmg.cpp:297:ISyAccountMgt::PasswordValidationMessage CSyAccountMgtImpl::validatePasswordPolicy(saponazure,*****) const )
  * 解決策  
    *[Isolation]\(分離\)* の手順でホスト ルールを追加して、VM からドメイン コントローラーへの通信を許可してください。

## <a name="next-steps"></a>次のステップ
* [SAP HANA on Azure 運用ガイド][hana-ops-guide]
* [SAP のための Azure Virtual Machines の計画と実装][planning-guide]
* [SAP のための Azure Virtual Machines のデプロイ][deployment-guide]
* [SAP のための Azure Virtual Machines DBMS のデプロイ][dbms-guide]
