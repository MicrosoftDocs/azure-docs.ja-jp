---
title: "Azure PowerShell を使ったドメイン参加済み HDInsight クラスターの構成 | Microsoft Docs"
description: "Azure PowerShell を使ったドメイン参加済み HDInsight クラスターのセットアップと構成の方法について説明します。"
services: hdinsight
documentationcenter: 
author: saurinsh
manager: jhubbard
editor: cgronlun
tags: 
ms.assetid: a13b2f7a-612d-4800-bc92-7fc0524f3e89
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/02/2016
ms.author: saurinsh
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 916722d64f191bc482b50ad4338f6a1da1045b77
ms.lasthandoff: 03/25/2017


---
# <a name="configure-domain-joined-hdinsight-clusters-preview-using-azure-powershell"></a>Azure PowerShell を使用したドメイン参加済み HDInsight クラスターの構成 (プレビュー)
Azure PowerShell を使って、Azure HDInsight クラスター、Azure Active Directory (Azure AD)、および [Apache Ranger](http://hortonworks.com/apache/ranger/) をセットアップする方法について説明します。 迅速な構成とエラーの抑制のために、Azure PowerShell スクリプトが提供されます。 ドメイン参加済み HDInsight は、Linux ベースのクラスターのみで構成できます。 詳細については、[ドメイン参加済み HDInsight クラスターの概要](hdinsight-domain-joined-introduction.md)に関するページを参照してください。

ドメイン参加済み HDInsight クラスターの一般的な構成には、次の手順が含まれます。

1. Azure AD 用に Azure クラシック VNet を作成します。  
2. Azure AD と Azure AD DS を作成および構成します。
3. 組織単位を作成するために、クラシック VNet に VM を追加します。 
4. Azure AD DS 用の組織単位を作成します。
5. Azure リソース管理モードで HDInsight VNet を作成します。
6. Azure AD DS 用の DNS 逆引きゾーンをセットアップします。
7. 2 つの VNet をピアリングします。
8. HDInsight クラスターを作成します。

手順 3 から手順 7 は、提供する PowerShell スクリプトによって実行されます。 手順 1 と手順 2 は手動で行う必要があります。  Azure PowerShell を使用しない場合は、「[ドメイン参加済み HDInsight クラスターの構成](hdinsight-domain-joined-configure.md)」を参照してください。 

最終的なトポロジの例は、次のようになります。

![ドメイン参加済み HDInsight のトポロジ](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-topology.png)

現在、Azure AD では従来の仮想ネットワーク (VNet) のみがサポートされていて、Linux ベースの HDInsight クラスターでは Azure Resource Manager ベースの VNet のみがサポートされているため、HDInsight Azure AD 統合には 2 つの VNet とそれらの間のピアリングが必要です。 2 つのデプロイメント モデルの比較情報については、「[Azure Resource Manager とクラシック デプロイ: デプロイ モデルとリソースの状態について](../azure-resource-manager/resource-manager-deployment-model.md)」を参照してください。 2 つの VNet は、Azure AD DS と同じリージョンにある必要があります。

> [!NOTE]
> このチュートリアルでは、Azure AD がないことを前提としています。 ある場合は、手順 2. を省略できます。
> 
> 

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、次の項目を用意する必要があります。

* [Azure AD ドメイン サービス](https://azure.microsoft.com/services/active-directory-ds/)とその[料金](https://azure.microsoft.com/pricing/details/active-directory-ds/)体系を理解している。
* サブスクリプションが、このパブリック プレビューのホワイトリストに登録されている。 登録するには、サブスクリプション ID を明記して、hdipreview@microsoft.com に電子メールを送信します。
* ドメインの署名機関によって署名された SSL 証明書がある。 セキュリティで保護された LDAP を構成するために、証明書が必要です。 自己署名証明書を使用することはできません。
* Azure PowerShell。  「[Azure PowerShell のインストールと構成の方法](/powershell/azureps-cmdlets-docs)」を参照してください。

## <a name="create-an-azure-classic-vnet-for-your-azure-ad"></a>Azure AD 用に Azure クラシック VNet を作成します。
手順については、[こちら](hdinsight-domain-joined-configure.md#create-an-azure-classic-vnet)を参照してください。

## <a name="create-and-configure-azure-ad-and-azure-ad-ds"></a>Azure AD と Azure AD DS を作成および構成します。
手順については、[こちら](hdinsight-domain-joined-configure.md#create-and-configure-azure-ad-ds-for-your-azure-ad)を参照してください。

## <a name="run-the-powershell-script"></a>PowerShell スクリプトの実行
PowerShell スクリプトは [GitHub](https://github.com/hdinsight/DomainJoinedHDInsight) からダウンロードできます。 zip ファイルを解凍し、ファイルをローカルに保存します。

**PowerShell スクリプトを編集するには**

1. Windows PowerShell ISE または任意のテキスト エディターを使用して、run.ps1 を開きます。
2. 次の変数の値を入力します。
   
   * **$SubscriptionName** – HDInsight クラスターを作成する Azure サブスクリプションの名前。 このサブスクリプションにはクラシック Virtual Network が既に作成されていますが、今回は HDInsight クラスター用の Azure Resource Manager の仮想ネットワークを作成します。
   * **$ClassicVNetName** - Azure AD DS を含む従来の仮想ネットワーク。 この仮想ネットワークは、上で指定したサブスクリプションと同じサブスクリプション内に存在する必要があります。 この仮想ネットワークは、クラシック ポータルではなく Azure Portal を使用して作成する必要があります。 「[ドメイン参加済み HDInsight クラスターの構成 (プレビュー)](hdinsight-domain-joined-configure.md#create-an-azure-classic-vnet)」の手順に従う場合、既定の名前は contosoaadvnet です。
   * **$ClassicResourceGroupName** – 上記で説明した、従来の仮想ネットワークの Resource Manager グループ名。 例： contosoaadrg 
   * **$ArmResourceGroupName** – HDInsight クラスターを作成するリソース グループ名。 $ArmResourceGroupName と同じリソース グループを使用することができます。  リソース グループが存在しない場合は、スクリプトによってリソース グループが作成されます。
   * **$ArmVNetName** - HDInsight クラスターを作成する Resource Manager の仮想ネットワーク名。 この仮想ネットワークは $ArmResourceGroupName に配置されます。  VNet が存在しない場合は、PowerShell スクリプトによって VNet が作成されます。 存在する場合は、上で指定したリソース グループの一部である必要があります。
   * **$AddressVnetAddressSpace** – Resource Manager の仮想ネットワークのネットワーク アドレス空間。 このアドレス空間にアクセスできることを確認します。 このアドレス空間は、従来の仮想ネットワークのアドレス空間と重複できません。 例: 10.1.0.0/16
   * **$ArmVnetSubnetName** - HDInsight クラスターの VM を配置する、Resource Manager の仮想ネットワーク サブネット名。 サブネットが存在しない場合は、PowerShell スクリプトによってサブネットが作成されます。 存在する場合は、上で指定した仮想ネットワークの一部である必要があります。
   * **$AddressSubnetAddressSpace** – Resource Manager の仮想ネットワーク サブネットのネットワーク アドレス範囲。 HDInsight クラスターの VM の IP アドレスは、このサブネットのアドレス範囲から指定します。 例：10.1.0.0/24
   * **$ActiveDirectoryDomainName** – HDInsight クラスターの VM が参加する、Azure AD ドメイン名。 例：contoso.onmicrosoft.com
   * **$ClusterUsersGroups** – HDInsight クラスターを同期する AD の、セキュリティ グループの共通名。 このセキュリティ グループ内のユーザーは、Active Directory ドメインの資格情報を使用して、クラスターのダッシュボードにログオンできるようになります。 これらのセキュリティ グループは、Active Directory に存在する必要があります。 例：hiveusers または clusteroperatorusers
   * **$OrganizationalUnitName** - HDInsight クラスターの VM、およびクラスターが使用するサービス プリンシパルを配置する、ドメイン内の組織単位。 組織単位 (OU) が存在しない場合は、PowerShell スクリプトによって OU が作成されます。 例：HDInsightOU
3. 変更を保存します。

**スクリプトを実行するには**

1. **Windows PowerShell** を管理者として実行します。
2. run.ps1 のフォルダを参照します。 
3. ファイル名を入力してスクリプトを実行し、**Enter** キーを押します。  3 つのサインイン ダイアログ ボックスが表示されます。
   
   1. **Azure クラシック ポータルへのサインイン** – Azure クラシック ポータルへのサインインに使用する資格情報を入力します。 これらの資格情報を使用して、Azure AD と Azure AD DS を事前に作成しておく必要があります。
   2. **Azure Resource Manager ポータルへのサインイン** – Azure Resource Manager ポータルへのサインインに使用する資格情報を入力します。
   3. **ドメイン ユーザー名** – HDInsight クラスターの管理者にするドメイン ユーザー名の資格情報を入力します。 Azure AD をゼロから作成した場合は、このドキュメントを使用して、このユーザーを作成しておく必要があります。 
      
      > [!IMPORTANT]
      > 次の形式でユーザー名を入力します。 
      > 
      > Domainname\username (例： contoso.onmicrosoft.com\clusteradmin)
      > 
      > 
      
      このユーザーには、指定した Active Directory ドメインへのコンピューターの参加、指定した組織単位内でのサービス プリンシパルとコンピューター オブジェクトの作成、および DNS の逆引きプロキシ ルールの追加という、3 つの特権があることが必要です。

DNS 逆引きゾーンの作成時に、スクリプトにより、ネットワーク ID の入力が求められます。 このネットワーク ID は、Resource Manager の仮想ネットワークのアドレス プレフィクスにする必要があります。 たとえば、Resource Manager の仮想ネットワーク サブネットのアドレス空間が 10.2.0.0/24 の場合、ネットワーク ID の入力を求められたら「10.2.0.0/24」を入力します。 

## <a name="create-hdinsight-cluster"></a>HDInsight クラスターの作成
このセクションでは、Azure Portal または [Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-template-deploy.md)を利用して、HDInsight で Linux ベースの Hadoop クラスターを作成します。 その他のクラスター作成方法と設定の詳細については、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。 Resource Manager テンプレートを利用して HDInsight で Hadoop クラスターを作成する方法の詳細については、[Resource Manager テンプレートを使用した HDInsight での Hadoop クラスターの作成](hdinsight-hadoop-create-windows-clusters-arm-templates.md)に関するページを参照してください。

**Azure Portal を使用して、ドメイン参加済み HDInsight クラスターを作成するには**

1. [Azure ポータル](https://portal.azure.com)にサインオンします。
2. **[新規]**、**[インテリジェンス + 分析]**、**[HDInsight]** の順にクリックします。
3. **[新しい HDInsight クラスター]** ブレードで、次の値を入力または選択します。
   
   * **クラスター名**: ドメイン参加済み HDInsight クラスターの新しいクラスター名を入力します。
   * **サブスクリプション**: このクラスターの作成に使用する Azure サブスクリプションを選択します。
   * **クラスター構成**:
     
     * **クラスターの種類**: Hadoop。 ドメイン参加済み HDInsight は現在、Hadoop クラスターのみでサポートされています。
     * **オペレーティング システム**: Linux。  ドメイン参加済み HDInsight は、Linux ベースの HDInsight クラスターのみでサポートされています。
     * **バージョン**: Hadoop 2.7.3 (HDI 3.5)。 ドメイン参加済み HDInsight は、HDInsight クラスター バージョン 3.5 のみでサポートされています。
     * **クラスターの種類**: PREMIUM
       
       **[選択]** をクリックして変更を保存します。
   * **資格情報**: クラスター ユーザーと SSH ユーザーの両方の資格情報を構成します。
   * **データ ソース**: HDInsight クラスターの既定のストレージ アカウントとして、新しいストレージ アカウントを作成するか、既存のストレージ アカウントを使用します。 場所は、2 つの VNet と同じである必要があります。  また、場所は HDInsight クラスターの場所でもあります。
   * **価格**: クラスターのワーカー ノードの数を選択します。
   * **Advanced configurations (詳細な構成)**: 
     
     * **Domain-joining & Vnet/Subnet (ドメイン参加と Vnet/サブネット)**: 
       
       * **ドメイン設定**: 
         
         * **ドメイン名**: contoso.onmicrosoft.com
         * **ドメイン ユーザー名**: ドメイン ユーザー名を入力します。 このドメインは、次の特権を持つ必要があります。コンピューターをドメインに参加させ、前に構成した組織単位に配置する特権。前に構成した組織単位内でサービス プリンシパルを作成する特権。および、逆引き DNS エントリを作成する特権です。 このドメイン ユーザーは、このドメイン参加済み HDInsight クラスターの管理者になります。
         * **ドメイン パスワード**: ドメイン ユーザーのパスワードを入力します。
         * **組織単位**: 前に構成した OU の識別名を入力します。 例: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
         * **LDAPS URL**: ldaps://contoso.onmicrosoft.com:636
         * **アクセス ユーザー グループ**: クラスターに同期させるユーザーが属しているセキュリティ グループを指定します。 たとえば、HiveUsers です。
           
           **[選択]** をクリックして変更を保存します。
           
           ![ドメイン参加済み HDInsight のポータルでのドメイン設定の構成](./media/hdinsight-domain-joined-configure/hdinsight-domain-joined-portal-domain-setting.png)
       * **仮想ネットワーク**: contosohdivnet
       * **サブネット**: Subnet1
         
         **[選択]** をクリックして変更を保存します。        
         **[選択]** をクリックして変更を保存します。
   * **リソース グループ**: HDInsight VNet (contosohdirg) 用に使用されるリソース グループを選択します。
4. **[作成]**をクリックします。  

ドメイン参加済み HDInsight クラスターを作成するためのもう 1 つのオプションは、Azure Resource Manager テンプレートを使用する方法です。 次の手順は、その方法を示しています。

**Resource Manager テンプレートを使用してドメイン参加済み HDInsight クラスターを作成するには**

1. 次の画像をクリックして、Azure Portal で Resource Manager テンプレートを開きます。 Resource Manager テンプレートは、パブリック BLOB コンテナー内にあります。 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-domain-joined-hdinsight-cluster.json" target="_blank"><img src="./media/hdinsight-domain-joined-configure-use-powershell/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. **[パラメーター]** ブレードで、次の各項目を入力します。
   
   * **サブスクリプション**: (Azure サブスクリプションを選択します)。
   * **リソース グループ**: **[既存のものを使用]** をクリックして、使用してきた同じリソース グループを指定します。  たとえば contosohdirg です。 
   * **場所**: リソース グループの場所を指定します。
   * **クラスター名**: 作成する Hadoop クラスターの名前を入力します。 たとえば contosohdicluster です。
   * **クラスターの種類**: クラスターの種類を選択します。  既定値は **hadoop** です。
   * **場所**: クラスターの場所を選択します。  既定のストレージ アカウントは、同じ場所を使用します。
   * **Cluster Worker Node count (クラスターのワーカー ノードの数)**: ワーカー ノードの数を選択します。
   * **クラスターのログイン名とパスワード**: 既定のログイン名は **admin** です。
   * **SSH ユーザー名とパスワード**: 既定のユーザー名は **sshuser** です。  この名前は変更できます。 
   * **仮想ネットワーク ID**: /subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>
   * **Virtual Network Subnet (仮想ネットワークのサブネット)**: /subscriptions/&lt;SubscriptionID>/resourceGroups/&lt;ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/&lt;VNetName>/subnets/Subnet1
   * **ドメイン名**: contoso.onmicrosoft.com
   * **Organization Unit DN (組織単位 DN)**: OU=HDInsightOU,DC=contoso,DC=onmicrosoft,DC=com
   * **Cluster Users Group D Ns (クラスター ユーザー グループの DN)**: "\"CN=HiveUsers,OU=AADDC Users,DC=<DomainName>,DC=onmicrosoft,DC=com\""
   * **LDAPUrls**: ["ldaps://contoso.onmicrosoft.com:636"]
   * **DomainAdminUserName**: (ドメイン管理者のユーザー名を入力します)
   * **DomainAdminPassword**: (ドメイン管理者のユーザー パスワードを入力します)
   * **上記の使用条件に同意する**: (オン)
   * **ダッシュボードにピン留めする**: (オン)
3. **[購入]** をクリックします。 " **テンプレートのデプロイのデプロイ中**" という新しいタイルが表示されます。 クラスターの作成には約 20 分かかります。 クラスターが作成されたら、ポータルのクラスター ブレードをクリックして開きます。

チュートリアルを完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。 クラスターの削除手順については、「[Azure Portal を使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-management-portal.md#delete-clusters)」を参照してください。

## <a name="next-steps"></a>次のステップ

* Hive ポリシーの構成と Hive クエリの実行については、[ドメイン参加済み HDInsight クラスターの Hive ポリシーの構成](hdinsight-domain-joined-run-hive.md)に関する記事をご覧ください。
* SSH を使用してドメイン参加済み HDInsight クラスターに接続する方法については、[HDInsight での SSH キーの使用](hdinsight-hadoop-linux-use-ssh-unix.md#domainjoined)に関するページを参照してください。


