---
title: クイックスタート - Red Hat Enterprise Linux (RHEL) 上の JBoss Enterprise Application Platform (EAP) を Azure VM と仮想マシン スケール セットにデプロイする
description: Azure の RHEL VM および仮想マシン スケール セット上の Red Hat JBoss EAP を使用してエンタープライズ Java アプリケーションをデプロイする方法。
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: 4027e9c336d938c3877e56b0eb59edea6ac1d992
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101671964"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux 上の JBoss EAP を使用してエンタープライズ Java アプリケーションを Azure にデプロイする

この記事の Azure クイックスタート テンプレートでは、[Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) を使用して [JBoss Enterprise Application Platform (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) を Azure 仮想マシン (VM) および仮想マシン スケール セットにデプロイする方法を示します。 サンプルの Java アプリを使用してデプロイを検証します。 

JBoss EAP は、オープンソースのアプリケーション サーバー プラットフォームです。 Java アプリケーションにエンタープライズ レベルのセキュリティ、スケーラビリティ、パフォーマンスを提供します。 RHEL は、オープンソースのオペレーティング システム (OS) プラットフォームです。 既存のアプリをスケーリングすると共に、最新のテクノロジをすべての環境にわたってロールアウトすることができます。 

JBoss EAP と RHEL には、あらゆる環境でエンタープライズ Java アプリケーションを構築、実行、デプロイ、管理するために必要なすべてのものが含まれています。 その組み合わせは、オンプレミス、仮想環境、およびプライベート、パブリック、ハイブリッドのクラウドのためのオープンソース ソリューションです。

## <a name="prerequisites"></a>前提条件 

* アクティブなサブスクリプションが含まれる Azure アカウント。 Azure サブスクリプションを入手するには、[Visual Studio サブスクライバー向けの Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)をアクティブにするか、[アカウントを無料で作成](https://azure.microsoft.com/pricing/free-trial)します。

* JBoss EAP のインストール。 JBoss EAP の Red Hat Subscription Management (RHSM) エンタイトルメントがある Red Hat アカウントが必要です。 このエンタイトルメントにより、Red Hat によるテストと認定が済んだ JBoss EAP バージョンをダウンロードすることができます。  

  EAP のエンタイトルメントがない場合は、先に進む前に [JBoss EAP 評価サブスクリプション](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation)を入手してください。 新しい Red Hat サブスクリプションを作成するには、[Red Hat カスタマー ポータル](https://access.redhat.com/)に移動して、アカウントを設定します。

* [Azure CLI](/cli/azure/overview)。

* RHEL オプション。 従量課金制 (PAYG) またはサブスクリプション持ち込み (BYOS) のどちらかを選択します。 BYOS を選択した場合、クイックスタート テンプレートをデプロイする前に [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold Image をアクティブにする必要があります。

## <a name="java-ee-and-jakarta-ee-application-migration"></a>Java EE と Jakarata EE アプリケーションの移行

### <a name="migrate-to-jboss-eap"></a>JBoss EAP への移行
JBoss EAP 7.2 および 7.3 は、Java Enterprise Edition (Java EE) 8 と Jakarta EE 8 の仕様に対する認定済みの実装です。 JBoss EAP には、高可用性 (HA) クラスタリング、メッセージング、分散キャッシュなどの機能に関して、事前構成済みのオプションが用意されています。 また、JBoss EAP によって提供されるさまざまな API とサービスを使用して、アプリケーションを作成、デプロイ、実行することもできます。  

JBoss EAP の詳細については、[JBoss EAP 7.2 の概要](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index)または [JBoss EAP 7.3 の概要](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index)に関するページを参照してください。

 #### <a name="applications-on-jboss-eap"></a>JBoss EAP 上のアプリケーション

* **Web サービス アプリケーション**。 Web サービスにより、ソフトウェア アプリケーション間の相互運用を実現するための標準的な方法が提供されます。 実行環境となるプラットフォームやフレームワークは、アプリケーションごとに異なる場合があります。 内部の異種サブシステムの通信を支援するのが、これらの Web サービスとなります。 

  詳細については、[EAP 7.2 上の Web サービス アプリケーションの開発](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index)または [EAP 7.3 上の Web サービス アプリケーションの開発](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index)に関するページを参照してください。

* **Enterprise Java Beans (EJB) アプリケーション**。 EJB 3.2 は、分散型でトランザクション対応の、セキュリティで保護された移植可能な Java EE および Jakarta EE アプリケーションを開発するための API です。 EJB では、Enterprise Bean と呼ばれるサーバー側コンポーネントを使用して、再利用を促進する分離された方法でアプリケーションのビジネス ロジックを実装します。 

  詳細については、[EAP 7.2 上の EJB アプリケーションの開発](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index)または [EAP 7.3 上の EJB アプリケーションの開発](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index)に関するページを参照してください。

* **Hibernate アプリケーション**。 開発者および管理者は JBoss EAP を使用して、Java Persistence API (JPA) および Hibernate アプリケーションを開発し、デプロイすることができます。 Hibernate Core は、Java 言語のためのオブジェクト リレーショナル マッピング フレームワークです。 オブジェクト指向のドメイン モデルをリレーショナル データベースにマッピングするためのフレームワークが備わっているため、アプリケーションから直接データベースを操作する必要がありません。 

  Hibernate Entity Manager は、[JPA 2.1 仕様](https://www.jcp.org/en/jsr/overview)によって定義されたプログラミング インターフェイスとライフサイクル ルールを実装します。 このラッパーが Hibernate Annotations と連携することで、完成度の高い Hibernate Core 上に完全 (かつスタンドアロン) な JPA ソリューションが実装されます。 
  
  Hibernate の詳細については、[EAP 7.2 上の JPA](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) または [EAP 7.3 上の Jakarta Persistence](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api) に関するページを参照してください。

#### <a name="red-hat-migration-toolkit-for-applications"></a>Red Hat Migration Toolkit for Applications
[Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) は、Java アプリケーション サーバー用の移行ツールです。 他のアプリ サーバーから JBoss EAP に移行するには、このツールを使用します。 [Eclipse IDE](https://www.eclipse.org/ide/)、[Red Hat CodeReady Workspaces](https://developers.redhat.com/products/codeready-workspaces/overview)、[Visual Studio Code ](https://code.visualstudio.com/docs/languages/java) for Java 用の IDE プラグインで動作します。 

MTA は、次の機能を備えた無料のオープンソース ツールです。
* アプリケーションの分析を自動化する。
* 作業量の見積もりを支援する。
* コードの移行を促進する。
* コンテナー化をサポートする。
* Azure Workload Builder と連携する。

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>オンプレミスから Azure への JBoss EAP の移行
Azure Marketplace の JBoss EAP on RHEL プランを使用すると、Azure VM のインストールとプロビジョニングが 20 分かからずに行えます。 これらのプランには、[Azure Marketplace](https://azuremarketplace.microsoft.com/) からアクセスできます。

この Azure Marketplace プランには、お客様の要件をサポートするために、EAP と RHEL のバージョンのさまざまな組み合わせが用意されています。 JBoss EAP は常に BYOS ですが、RHEL OS の場合は、BYOS または PAYG を選択できます。 Azure Marketplace の JBoss EAP on RHEL プランでは、スタンドアロンの VM とクラスター化された VM のどちらかを選択できるようになっています。

* JBoss EAP 7.2 on RHEL 7.7 VM (PAYG)
* JBoss EAP 7.2 on RHEL 8.0 VM (PAYG)
* JBoss EAP 7.3 on RHEL 8.0 VM (PAYG)
* JBoss EAP 7.2 on RHEL 7.7 VM (BYOS)
* JBoss EAP 7.2 on RHEL 8.0 VM (BYOS)
* JBoss EAP 7.3 on RHEL 8.0 VM (BYOS)

Azure Marketplace の各プランと共に、Azure への移行を始めるためのクイックスタート テンプレートが用意されています。 これらのクイックスタートには、あらかじめ構築された Azure Resource Manager (ARM) テンプレートとスクリプトが含まれており、さまざまな構成とバージョンの組み合わせで JBoss EAP on RHEL をデプロイすることができます。 内容は次のとおりです。

* ロード バランサー。
* 負荷分散と VM のためのプライベート IP。
* 仮想ネットワークと 1 つのサブネット。
* VM の構成 (クラスターまたはスタンドアロン)。
* サンプル Java アプリケーション。

これらのテンプレートのソリューション アーキテクチャには以下が含まれます。

* スタンドアロン RHEL VM 上の JBoss EAP。
* 複数の RHEL VM にわたってクラスター化された JBoss EAP。
* Azure 仮想マシン スケール セットを使用してクラスター化された JBoss EAP。

#### <a name="linux-workload-migration-for-jboss-eap"></a>JBoss EAP の Linux ワークロードの移行
Azure Workload Builder を使用すると、オンプレミスの Java アプリを Azure に移行するときの概念実証、評価、移行プロセスが簡単になります。 Azure Migrate 検出ツールと統合されたWorkload Builder により、JBoss EAP サーバーが識別されます。 その後、JBoss EAP サーバー デプロイ用の Ansible プレイブックが動的に生成されます。 他のアプリ サーバーから JBoss EAP にサーバーを移行するために、Red Hat MTA ツールが使用されます。 

移行を簡略化するための手順は、次のとおりです。
1. **評価**。 Azure VM または仮想マシン スケール セットを使用する JBoss EAP クラスターを評価します。
1. **アセスメント**。 アプリケーションとインフラストラクチャをスキャンします。
1. **インフラストラクチャの構成**。 ワークロード プロファイルを作成します。
1. **デプロイとテスト**。 ワークロードをデプロイ、移行、テストします。
1. **デプロイ後の構成**。 データ、監視、セキュリティ、バックアップなどと統合します。

## <a name="server-configuration-choice"></a>サーバー構成の選択

RHEL VM のデプロイの場合は、PAYG または BYOS を選択できます。 [Azure Marketplace](https://azuremarketplace.microsoft.com) のイメージの場合は、既定で PAYG になります。 独自の RHEL OS イメージを使用する場合は、BYOS タイプの RHEL VM をデプロイします。 VM または仮想マシン スケール セットをデプロイする前に、使用する RHSM アカウントに Cloud Access からの BYOS エンタイトルメントがあることを確認します。

JBoss EAP には、強力な管理機能と共に、アプリケーションのための機能と API も用意されています。 これらの管理機能は、どの動作モードを使用して JBoss EAP を起動しているかによって異なります。 それは、RHEL サーバーと Windows Server でサポートされます。 JBoss EAP には、個別のインスタンスを管理するためのスタンドアロン サーバー動作モードが用意されています。 さらに、インスタンスのグループを単一の制御ポイントから管理するためのマネージド ドメイン動作モードも用意されています。 

> [!NOTE]
> Microsoft Azure では、Azure インフラストラクチャ サービスによって HA 機能が管理されるため、JBoss EAP のマネージド ドメインはサポートされていません。 

JBoss EAP のインストールへのパスは、環境変数 `EAP_HOME` によって示されます。 スタンドアロン モードで JBoss EAP サービスを開始するには、次のコマンドを使用します。

```
$EAP_HOME/bin/standalone.sh
```
    
この起動スクリプトでは、EAP_HOME/bin/standalone.conf ファイルを使用して、いくつかの既定の初期設定 (JVM オプションなど) を行います。 このファイルの設定はカスタマイズできます。 既定では、JBoss EAP は standalone.xml 構成ファイルを使用してスタンドアロン モードで起動しますが、別のモードを使用して起動することもできます。 

使用可能なスタンドアロン構成ファイルとその使用方法について詳しくは、[EAP 7.2 のスタンドアロン サーバー構成ファイル](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files)または [EAP 7.3 のスタンドアロン サーバー構成ファイル](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files)に関するページを参照してください。 

別の構成で JBoss EAP を起動するには、`--server-config` 引数を使用します。 次に例を示します。
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
使用可能なすべての起動スクリプト引数とその目的の完全な一覧を見るには、`--help` 引数を使用してください。 詳細については、[EAP 7.2 のサーバー実行時引数](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime)または [EAP 7.3 のサーバー実行時引数](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime)に関するページを参照してください。
    
JBoss EAP は、クラスター モードで動作させることもできます。 JBoss EAP のクラスター メッセージングを使用すると、JBoss EAP のメッセージング サーバーをグループ化して、メッセージ処理の負荷を分担させることができます。 クラスター内の各アクティブ ノードがアクティブな JBoss EAP メッセージング サーバーとして、それぞれが分担するメッセージを管理し、固有の接続を処理します。 詳細については、[EAP 7.2 におけるクラスターの概要](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview)または [EAP 7.3 におけるクラスターの概要](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview)に関するページを参照してください。 

## <a name="support-and-subscription-notes"></a>サポートとサブスクリプションに関する注記
これらのクイックスタート テンプレートは次のように提供されます。 

- RHEL OS は、Red Hat Gold Image モデルによる BYOS または PAYG として提供されます。
- JBoss EAP は BYOS としてのみ提供されます。

#### <a name="using-rhel-os-with-the-payg-model"></a>PAYG モデルでの RHEL OS の使用

これらのクイックスタート テンプレートでは、Azure Marketplace のオンデマンド RHEL 7.7 または 8.0 PAYG イメージが既定で使用されます。 PAYG イメージを使用すると、コンピューティング、ネットワーク、ストレージの標準コストとは別に、時間単位の RHEL サブスクリプション料が課金されます。 同時に、そのインスタンスは、ご利用の Red Hat サブスクリプションに登録されます。 つまり、自分に付与されたいずれかのエンタイトルメントが使用されます。 

この PAYG イメージによって "二重請求" が発生します。 この問題は、独自の RHEL イメージを作成することで回避できます。 詳細については、Red Hat のナレッジ ベースで、[Microsoft Azure 向けに RHEL VM をプロビジョニングする方法](https://access.redhat.com/articles/uploading-rhel-image-to-azure)に関する記事を参照してください。 または、[Red Hat Cloud Access](https://access.redhat.com/) の RHEL Gold Image をアクティブにしてください。

PAYG VM の価格の詳細については、[Red Hat Enterprise Linux の価格](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/)に関するページを参照してください。 RHEL を PAYG モデルで使用するには、[Azure Marketplace での RHEL 7.7](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) または [Azure Marketplace での RHEL 8.0](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM) の支払方法が指定された Azure サブスクリプションが必要です。 これらのプランの支払方法が Azure サブスクリプションで指定されている必要があります。

#### <a name="using-rhel-os-with-the-byos-model"></a>BYOS モデルでの RHEL OS の使用

RHEL OS に BYOS を使用するには、Azure で RHEL OS を使用するためのエンタイトルメントが付与された有効な Red Hat サブスクリプションを所有している必要があります。 BYOS モデルで RHEL OS をデプロイする前に、次の前提条件を満たしてください。

1. Red Hat サブスクリプションに RHEL OS と JBoss EAP のエンタイトルメントが付与されていることを確認します。
2. ご利用の Azure サブスクリプション ID で RHEL BYOS イメージを使用することを承認します。 [Red Hat Subscription Management のドキュメント](https://access.redhat.com/documentation/red_hat_subscription_management/1/)に従って、以下の手順が含まれているプロセスを実行します。

   1. Red Hat Cloud Access ダッシュボードで、Microsoft Azure をプロバイダーとして有効にします。

   1. Azure サブスクリプション ID を追加します。

   1. Microsoft Azure で新しい製品の Cloud Access を有効にします。
    
   1. Azure サブスクリプションの Red Hat Gold Image をアクティブにします。 詳細については、「[Microsoft Azure での Red Hat Gold Image](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/cloud-access-gold-images_cloud-access#proc_using-gold-images-azure_cloud-access)」を参照してください。

   1. Azure サブスクリプションで Red Hat Gold Image が使用できる状態になるまで待ちます。 通常これらのイメージは、申請から 3 時間以内に利用できるようになります。
    
3. RHEL BYOS イメージに対する Azure Marketplace の使用条件に同意します。 このプロセスは、以下の Azure CLI コマンドを実行することによって完了できます。 詳細については、[Azure における RHEL BYOS Gold Image](./byos.md) に関するドキュメントを参照してください。 必ず最新バージョンの Azure CLI を実行してください。

   1. Azure CLI セッションを開き、ご利用の Azure アカウントで認証を行います。 詳細については、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)」を参照してください。

   1. 次の CLI コマンドを実行して、ご利用のサブスクリプションで RHEL BYOS イメージが使用できることを確認します。 何も結果が返されない場合は、Azure サブスクリプションの RHEL BYOS イメージがアクティブになっていることを確認します。
   
      ```
      az vm image list --offer rhel-byos --all
      ```

   1. RHEL 7.7 BYOS および RHEL 8.0 BYOS に対する Azure Marketplace の使用条件に同意します。それぞれ次のコマンドを実行します。
      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
      ``` 

      ```
      az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
      ``` 
   
ご利用のサブスクリプションで、Azure 仮想マシンに RHEL 7.7 または 8.0 BYOS をデプロイする準備はこれで完了です。

#### <a name="using-jboss-eap-with-the-byos-model"></a>BYOS モデルでの JBoss EAP の使用

Azure では、JBoss EAP が BYOS モデルでのみ提供されます。 このテンプレートをデプロイするときは、有効な EAP エンタイトルメントが付与された RHSM プール ID と共に、RHSM の資格情報を指定する必要があります。 EAP のエンタイトルメントがない場合は、先に進む前に [JBoss EAP 評価サブスクリプション](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation)を入手してください。

## <a name="deployment-options"></a>デプロイ オプション

テンプレートは、次の方法でデプロイできます。

- **PowerShell**。 次のコマンドを実行してテンプレートをデプロイします 

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
 
  Azure PowerShell のインストールと構成については、[PowerShell のドキュメント](/powershell/azure/)を参照してください。  

- **Azure CLI**。 次のコマンドを実行してテンプレートをデプロイします

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az deployment group create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

  Azure CLI のインストールと構成の詳細については、[CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

- **Azure Portal** 以下のセクションで説明する Azure クイックスタート テンプレートを使用して、Azure portal にデプロイできます。 クイックスタートに移動したら、 **[Deploy to Azure]\(Azure にデプロイ\)** または **[Browse on GitHub]\(GitHub で参照\)** ボタンを選択します。

## <a name="azure-quickstart-templates"></a>Azure クイック スタート テンプレート

デプロイの目標を満たす JBoss EAP on RHEL 用の以下のクイックスタート テンプレートを 1 つ使用して開始できます。

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP on RHEL (スタンドアロン VM)</a>。 RHEL 7.7 または 8.0 VM 上で実行される JBoss EAP 7.2 または 7.3 に、JBoss-EAP on Azure という名前の Web アプリケーションをデプロイします。

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP on RHEL (クラスター化された複数の VM)</a>。 *n* 個の RHEL 7.7 または 8.0 VM 上で実行される JBoss EAP 7.2 または 7.3 クラスターに、eap-session-replication という名前の Web アプリケーションをデプロイします。 *n* の値はユーザーが決定します。 すべての VM が、ロード バランサーのバックエンド プールに追加されます。

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP on RHEL (クラスター化された仮想マシン スケール セット)</a>。 RHEL 7.7 または 8.0 の仮想マシン スケール セット上で実行される JBoss EAP 7.2 または 7.3 クラスターに、eap-session-replication という名前の Web アプリケーションをデプロイします。

## <a name="resource-links"></a>リソース リンク

* [Azure ハイブリッド特典](../../windows/hybrid-use-benefit-licensing.md)
* [Azure App Service 向けの Java アプリを構成する](../../../app-service/configure-language-java.md)
* [JBoss EAP on Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP on Azure App Service Linux](../../../app-service/quickstart-java.md)
* [JBoss EAP on Azure App Service をデプロイする](https://github.com/JasonFreeberg/jboss-on-app-service)

## <a name="next-steps"></a>次のステップ

* [JBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/) の詳細を確認する。
* [JBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/) の詳細を確認する。
* [Red Hat Subscription Management](https://access.redhat.com/products/red-hat-subscription-management) の詳細を確認する。
* [Azure 上の Red Hat ワークロード](./overview.md)について確認する。
* [Azure Marketplace から RHEL の VM または仮想マシン スケール セットに JBoss EAP](https://aka.ms/AMP-JBoss-EAP) をデプロイする。
* [Azure クイックスタート テンプレートから RHEL の VM または仮想マシン スケール セットに JBoss EAP](https://aka.ms/Quickstart-JBoss-EAP) をデプロイする。