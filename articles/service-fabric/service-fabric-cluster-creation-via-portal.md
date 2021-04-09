---
title: Azure portal で Service Fabric クラスターを作成する
description: Azure portal と Azure Key Vault を使用して Azure でセキュリティ保護された Service Fabric クラスターを設定する方法について説明します。
ms.topic: conceptual
ms.date: 09/06/2018
ms.openlocfilehash: c679a804db09b1034f31e9d8da1f7d2ad206f684
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90563728"
---
# <a name="create-a-service-fabric-cluster-in-azure-using-the-azure-portal"></a>Azure ポータルを使用して Azure で Service Fabric クラスターを作成する
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
> 
> 

これは、Azure Portal を使用して Azure で Service Fabric クラスター (Linux または Windows) のセットアップの手順を説明するステップ バイ ステップ ガイドです。 このガイドでは、次の手順について説明します。

* Azure Portal を使用して Azure でクラスターを作成します。
* 証明書を使って管理者を認証します。

> [!NOTE]
> Azure Active Directory を使用したユーザー認証、アプリケーション セキュリティ用の証明書の設定など、詳細なセキュリティ オプションについては、[Azure Resource Manager を使用したクラスターの作成][create-cluster-arm]に関する記事を参照してください。
> 
> 

## <a name="cluster-security"></a>クラスターのセキュリティ 
Service Fabric では証明書を使用して、クラスターとそのアプリケーションのさまざまな側面をセキュリティで保護するための認証および暗号化を指定します。 Service Fabric での証明書の使用方法の詳細については、「[Service Fabric クラスターのセキュリティに関するシナリオ][service-fabric-cluster-security]」を参照してください。

今回初めて Service Fabric クラスターを作成する場合、またはテスト ワークロード用のクラスターをデプロイする場合は、次のセクション ( **「Azure portal でのクラスターの作成**」) に進み、テスト ワークロードを実行するクラスターに必要な証明書をシステムで生成してください。 運用ワークロード用のクラスターを設定する場合は、引き続きこの説明を読んでください。

#### <a name="cluster-and-server-certificate-required"></a>クラスターとサーバーの証明書 (必須)
この証明書はクラスターをセキュリティで保護し、クラスターに対する未承認のアクセスを防ぐために必要です。 証明書により、クラスター セキュリティが次のような方法で提供されます。

* **クラスター認証:** クラスター フェデレーションのためのノード間通信を認証します。 この証明書で自分の ID を証明できたノードだけがクラスターに参加できます。
* **サーバー認証:** 管理クライアントに対するクラスター管理エンドポイントを認証します。これで、管理クライアントにより、実際のクラスターと通信していることが認識されるようになります。 また、この証明書は、HTTPS 管理 API および HTTPS 経由の Service Fabric Explorer に対する TLS も提供します。

この目的のため、証明書は次の要件を満たす必要があります。

* 証明書は秘密キーを含む必要があります。
* 証明書はキー交換のために作成され、Personal Information Exchange (.pfx) ファイルにエクスポートできる必要があります。
* 証明書の **サブジェクト名は、Service Fabric クラスターへのアクセスに使用されるドメインに一致する必要があります。** これは、クラスターの HTTPS 管理エンドポイントと Service Fabric Explorer 用の TLS を提供するために必要です。 証明機関 (CA) から `.cloudapp.azure.com` ドメインの TLS/SSL 証明書を取得することはできません。 クラスターのカスタム ドメイン名を取得します。 CA に証明書を要求するときは、証明書の件名がクラスターに使用するカスタム ドメイン名と一致している必要があります。

#### <a name="client-authentication-certificates"></a>クライアント認証証明書
その他のクライアント証明書は、クラスター管理タスクに対して管理者を認証します。 Service Fabric には、**admin** および **read-only user** という 2 つのアクセス レベルがあります。 管理アクセスについて、少なくとも 1 つの証明書を使用する必要があります。 追加のユーザー レベル アクセスとして、別の証明書を指定する必要があります。 アクセス ロールの詳細については、「[ロールベースのアクセス制御 (Service Fabric クライアント用)][service-fabric-cluster-security-roles]」を参照してください。

Service Fabric を操作するために、クライアント認証証明書を Key Vault にアップロードする必要はありません。 この証明書は、クラスター管理を許可されているユーザーにのみ指定する必要があります。 

> [!NOTE]
> クライアントのクラスター管理操作を認証するには、Azure Active Directory を使用することをお勧めします。 Azure Active Directory を使用するには、[Azure Resource Manager を使用してクラスターを作成する][create-cluster-arm]必要があります。
> 
> 

#### <a name="application-certificates-optional"></a>アプリケーション証明書 (省略可能)
アプリケーション セキュリティの目的で、任意の数の追加の証明書をクラスターにインストールできます。 クラスターを作成する前に、ノードにインストールする証明書を必要とするアプリケーション セキュリティ シナリオについて考慮します。これには次のようなものがあります。

* アプリケーション構成値の暗号化と復号化
* レプリケーション中のノード間のデータの暗号化 

[Azure Portal を使用してクラスターを作成](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-creation-via-portal.md)した場合、アプリケーション証明書を構成することはできません。 クラスターのセットアップ時にアプリケーション証明書を構成するには、[Azure Resource Manager を使用してクラスターを作成する][create-cluster-arm]必要があります。 作成後に、アプリケーション証明書をクラスターに追加することもできます。

## <a name="create-cluster-in-the-azure-portal"></a>Azure ポータルでのクラスターの作成

アプリケーションのニーズを満たす運用クラスターを作成するには、いくつかの計画を立てる必要があります。この作業に役立つ [Service Fabric クラスターの計画に関する考慮事項][service-fabric-cluster-capacity]のドキュメントを読んで理解しておくことを強くお勧めします。 

### <a name="search-for-the-service-fabric-cluster-resource"></a>Service Fabric クラスター リソースの検索

[Azure portal][azure-portal] にサインインします。
**[リソースの作成]** をクリックし、新しいリソース テンプレートを追加します。 **[Marketplace]** の **[すべて]** で、Service Fabric クラスター テンプレートを検索します。
一覧から **[Service Fabric クラスター]** を選択します。

![Azure ポータルで Service Fabric クラスター テンプレートを検索します。][SearchforServiceFabricClusterTemplate]

**[Service Fabric クラスター]** ブレードに移動し、 **[作成]** をクリックします。

**[Service Fabric クラスターの作成]** ブレードには次の 4 つの手順があります。

### <a name="1-basics"></a>1.基本
![新しいリソース グループの作成に関するスクリーンショット。][CreateRG]

[基本] ブレードでは、クラスターの基本情報を指定する必要があります。

1. クラスターの名前を入力します。
2. VM 向けリモート デスクトップの **ユーザー名** と **パスワード** を入力します。
3. クラスターをデプロイする **サブスクリプション** を選択します (特に、複数のサブスクリプションがある場合)。
4. 新しい **リソース グループ** を作成します。 クラスター名と同じにすると、後で検索が楽になります。デプロイメントを変更したり、クラスターを削除したりするときに特に便利です。
   
   > [!NOTE]
   > 既存のリソース グループを使用することもできますが、新しいリソース グループを作成することをお勧めします。 これにより、クラスターとそれが使用するすべてのリソースを簡単に削除できるようになります。
   > 
   > 
5. クラスターを作成する **場所** を選択します。 既にキー コンテナーにアップロードされている既存の証明書を使用する場合は、キー コンテナーが存在するのと同じリージョンを使用する必要があります。 

### <a name="2-cluster-configuration"></a>2.クラスター構成
![ノード タイプの作成][CreateNodeType]

クラスター ノードを構成します。 ノードのタイプには、VM のサイズ、VM の数、プロパティが定義されています。 クラスターには複数のノードのタイプを指定できますが、プライマリ ノードのタイプ (ポータルに最初に定義したノード) には、少なくとも 5 つの VM が必要です。これが Service Fabric システム サービスが配置されるノードのタイプになります。 "NodeTypeName" の既定の配置プロパティは自動的に追加されるため、 **[配置プロパティ]** を構成しないでください。

> [!NOTE]
> 複数のノードのタイプの一般的なシナリオは、フロントエンド サービスとバックエンド サービスを含むアプリケーションです。 フロントエンド サービスは、小規模の VM (D2_V2 のような VM サイズ) に配置して、インターネットへのポートを開きます。また、バックエンド サービスは、大規模な VM (D3_V2、D6_V2、D15_V2 などの VM サイズ) に配置して、インターネットに面するポートは開きません。
> 

1. ノードのタイプの名前を選択します (英字と数字のみを含む 1 ～ 12 文字)。
2. プライマリ ノード タイプの最小 VM **サイズ** は、クラスターに選択した **耐久性レベル** によって決まります。 既定の耐久性レベルはブロンズです。 耐久性の詳細については、[Service Fabric クラスターの耐久性の選択方法に関する記事][service-fabric-cluster-durability]を参照してください。
3. **仮想マシンのサイズ** を選択します。 D シリーズ VM には SSD ドライブが備わっており、ステートフルなアプリケーションに最適です。 部分的なコアを持つ VM SKU や使用可能なディスク容量が 10 GB 未満の VM SKU は使用しないでください。 VM サイズの選択については、[Service Fabric クラスターの計画に関する考慮事項][service-fabric-cluster-capacity]のドキュメントを参照してください。
4.  **1 つのノード クラスターと 3 つのノード クラスター** は、テストでの使用のみを目的としています。 運用ワークロードの実行では、サポートされません。
5. ノード タイプに対する **最初の仮想マシン スケール セットの容量** を選択します。 後でノード タイプの VM 数をスケールインまたはスケールアウトできますが、プライマリ ノード タイプの場合は、運用ワークロード用に 5 つ以上の VM が必要です。 他のノード タイプには、VM 数に最小値の 1 を設定できます。 プライマリ ノード タイプの最小 VM **数** によって、クラスターの **信頼性** が決まります。  
6. **カスタム エンドポイント** を構成します。 このフィールドでは、アプリケーション用にパブリック インターネットに Azure Load Balancer 経由で公開するポートのコンマ区切りのリストを入力できます。 たとえば、Web アプリケーションをクラスターにデプロイする予定がある場合は、「80」と入力して、クラスターへのポート 80 でのトラフィックを許可します。 エンドポイントの詳細については、[アプリケーションとの通信][service-fabric-connect-and-communicate-with-services]に関する記事を参照してください。
7. **リバース プロキシを有効にします**。  [Service Fabric のリバース プロキシ](service-fabric-reverseproxy.md)は、Service Fabric クラスターで実行されているマイクロサービスが HTTP エンドポイントを持つ他のサービスを検出してそのサービスと通信するのに役立ちます。
8. **[クラスター構成]** ブレードに戻り、 **[+ オプションの設定を表示する]** で、クラスターの **診断** を構成します。 既定では、問題のトラブルシューティングのためクラスターで診断が有効になります。 診断を無効にするには、 **[ステータス]** を **[オフ]** に切り替えます。 診断をオフにすることは **推奨されません** 。 Application Insights プロジェクトが既に作成されている場合は、キーを付与し、アプリケーション トレースがそのプロジェクトにルーティングされるようにします。
9. **DNS サービスを含めます**。  [DNS サービス](service-fabric-dnsservice.md)は DNS プロトコルを使用して他のサービスを検索できるオプションのサービスです。
10. クラスターを設定する **ファブリック アップグレード モード** を選択します。 使用可能な最新バージョンをシステムで自動的に選択し、クラスターをそのバージョンにアップグレード場合は、 **[自動]** を選択します。 サポートされるバージョンを選択する場合は、 **マニュアル** モードに設定します。 ファブリックのアップグレード モードの詳細については、[Service Fabric クラスター アップグレードのドキュメント][service-fabric-cluster-upgrade]を参照してください。

> [!NOTE]
> サポートされるバージョンの Service Fabric を実行しているクラスターのみがサポート対象になります。 **マニュアル** モードを選択すると、自身の責任でクラスターをサポートされるバージョンにアップグレードする必要があります。
> 

### <a name="3-security"></a>3.セキュリティ
![Azure portal のセキュリティ構成のスクリーンショット。][BasicSecurityConfigs]

セキュア テスト クラスターを簡単に設定できるようにするために、 **[Basic]** オプションが用意されています。 既に証明書があり、[キー コンテナー](../key-vault/index.yml)にアップロードしている場合 (また、デプロイでキー コンテナーが使用可能になっている場合) は、 **[カスタム]** オプションを使用します

#### <a name="basic-option"></a>[Basic] オプション
画面の指示に従って、既存のキー コンテナーを追加または再利用し、証明書を追加します。 証明書の追加は、同期処理です。そのため、証明書が作成されるのを待機する必要があります。

この処理が完了するまで、画面から移動しないでください。

![セキュリティ ページのスクリーンショット。[キー コンテナー] ウィンドウと [キー コンテナーの作成] ウィンドウで [Basic] が選択されています。][CreateKeyVault]

キー コンテナーが作成されたので、お使いのキー コンテナーのアクセス ポリシーを編集します。 

![[Service Fabric クラスターの作成] ウィンドウのスクリーンショット。オプション 3 の [セキュリティ] が選択されています。キー コンテナーが有効になっていないという説明があります。][CreateKeyVault2]

**[Edit access policies]\(アクセス ポリシーの編集\)** をクリックし、**高度なアクセス ポリシーを表示して**、デプロイのための Azure Virtual Machines へのアクセスを有効にします。 テンプレートのデプロイも有効にすることをお勧めします。 選択を行ったら、忘れずに **[保存]** ボタンをクリックし、 **[アクセス ポリシー]** ウィンドウを終了します。

![[Service Fabric クラスターの作成] ウィンドウのスクリーンショット。[セキュリティ] ウィンドウと [アクセス ポリシー] ウィンドウが開いています。][CreateKeyVault3]

証明書の名前を入力して **[OK]** をクリックします。

![[Service Fabric クラスターの作成] ウィンドウのスクリーンショット。前と同じで [セキュリティ] が選択されていますが、キー コンテナーが有効になっていないという説明がありません。][CreateKeyVault4]

#### <a name="custom-option"></a>カスタム オプション
**[Basic]** オプションの手順を既に実行している場合は、このセクションをスキップします。

![[セキュリティ] のスクリーンショット。クラスターのセキュリティ設定を構成するダイアログ ボックスを確認できます。][SecurityCustomOption]

[セキュリティ] ページを完了するには、[ソース キー コンテナー]、[証明書 URL]、および [証明書の拇印] の情報が必要です。 これらが手元にない場合は、別のブラウザー ウィンドウを開き、Azure portal で次を実行します。

1. キー コンテナー サービスに移動します。
2. [プロパティ] タブを選択し、[リソース ID] を他のブラウザー ウィンドウの [ソース キー コンテナー] にコピーします。 

    ![キー コンテナーの [プロパティ] ウィンドウのスクリーンショット。][CertInfo0]

3. [証明書] タブを選択します。
4. 証明書の拇印をクリックすると、[バージョン] ページに移動します。
5. 現在のバージョンの下に表示されている GUID をクリックします。

    ![キー コンテナーの [証明書] ウィンドウのスクリーンショット][CertInfo1]

6. 以下のような画面が表示されます。 16 進の SHA-1 拇印を他のブラウザー ウィンドウの [証明書の拇印] にコピーします。
7. [シークレット識別子] を他のブラウザー ウィンドウの [証明書 URL] にコピーします。

    ![[証明書バージョン] ダイアログ ボックスのスクリーンショット。証明書 ID をコピーするためのオプションがあります。][CertInfo2]

**[詳細設定の構成]** ボックスをオンにして、**管理用クライアント** と **読み取り専用クライアント** のクライアント証明書を入植します。 該当する場合は、これらのフィールドに管理用クライアント証明書の拇印と、読み取り専用ユーザー クライアント証明書の拇印を入力します。 管理者がクラスターに接続しようとした場合、ここに入力した拇印値に一致する拇印を持つ証明書がある場合にのみアクセスが許可されます。  

### <a name="4-summary"></a>4.まとめ

以上でクラスターをデプロイする準備が整いました。 クラスターをデプロイする前に、大きな青い情報ボックスの中を確認し、リンクを探して、証明書をダウンロードします。 証明書は、必ず安全な場所に保管してください。 証明書がないと、クラスターに接続できません。 ダウンロードした証明書には、パスワードが設定されていないので、パスワードを追加することをお勧めします。

クラスターの作成を完了するには、 **[作成]** をクリックします。 必要に応じて、テンプレートをダウンロードすることもできます。

![[Service Fabric クラスターの作成] の [概要] ページのスクリーンショット。証明書を表示し、ダウンロードするためのリンクがあります。][Summary]

通知には作成の進行状況が表示されます (画面の右上にあるステータス バーの近くの "ベル" アイコンをクリックします)。クラスターの作成中に **[スタート画面にピン留めする]** をクリックした場合、 **[Deploying Service Fabric Cluster (Service Fabric クラスターのデプロイ)]** が **[スタート]** 画面にピン留めされます。 このプロセスには少し時間がかかります。 

PowerShell または CLI を使用してクラスターで管理操作を実行するには、クラスターに接続する必要があります。詳細については、[クラスターへの接続に関する記事](service-fabric-connect-to-secure-cluster.md)を参照してください。

## <a name="view-your-cluster-status"></a>クラスターの状態を表示する
![ダッシュボードのクラスターの詳細のスクリーンショット。][ClusterDashboard]

クラスターの作成後、ポータルでクラスターを検査できます。

1. **[参照]** に移動し、 **[Service Fabric クラスター]** をクリックします。
2. クラスターを探してクリックします。
3. これにより、クラスターのパブリック エンドポイント、Service Fabric Explorer へのリンクなどのクラスターに関する詳細が、ダッシュボードに表示されます。

クラスターのダッシュボード ブレードの **[ノード モニター]** セクションには、正常な VM 数と正常ではない VM 数が表示されます。 クラスターの正常性の詳細については、[Service Fabric の正常性モデルの概要][service-fabric-health-introduction]に関するページを参照してください。

> [!NOTE]
> Service Fabric クラスターが常に稼働状態になって可用性を維持し、その状態を保持するには、一定数のノードが常にアップしている必要があります。これは、「維持クォーラム」と呼ばれます。 そのため、最初に[状態の完全バックアップ][service-fabric-reliable-services-backup-restore]を実行していない限り、クラスター内のすべてのコンピューターをシャットダウンするのは一般に安全ではありません。
> 
> 

## <a name="remote-connect-to-a-virtual-machine-scale-set-instance-or-a-cluster-node"></a>仮想マシン スケール セット インスタンスまたはクラスター ノードにリモート接続する
クラスターで指定する NodeType ごとに、仮想マシン スケール セットがセットアップされます。 <!--See [Remote connect to a Virtual Machine Scale Set instance][remote-connect-to-a-vm-scale-set] for details. -->

## <a name="next-steps"></a>次のステップ
この時点で、管理の認証に証明書を使用したセキュリティで保護されたクラスターがあります。 次に、[クラスターに接続](service-fabric-connect-to-secure-cluster.md)して、[アプリケーション シークレットを管理](service-fabric-application-secret-management.md)する方法を説明します。  また、[Service Fabric のサポート オプション](service-fabric-support.md)について学びます。

<!-- Links -->
[azure-powershell]: /powershell/azure/
[azure-portal]: https://portal.azure.com/
[key-vault-get-started]: ../key-vault/general/overview.md
[create-cluster-arm]: service-fabric-cluster-creation-via-arm.md
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[service-fabric-cluster-security-roles]: service-fabric-cluster-security-roles.md
[service-fabric-cluster-capacity]: service-fabric-cluster-capacity.md
[service-fabric-cluster-durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[service-fabric-connect-and-communicate-with-services]: service-fabric-connect-and-communicate-with-services.md
[service-fabric-health-introduction]: service-fabric-health-introduction.md
[service-fabric-reliable-services-backup-restore]: service-fabric-reliable-services-backup-restore.md
[remote-connect-to-a-vm-scale-set]: service-fabric-cluster-nodetypes.md
[service-fabric-cluster-upgrade]: service-fabric-cluster-upgrade.md

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[BasicSecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/BasicSecurityConfigs.PNG
[CreateKeyVault]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault.PNG
[CreateKeyVault2]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault2.PNG
[CreateKeyVault3]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault3.PNG
[CreateKeyVault4]: ./media/service-fabric-cluster-creation-via-portal/CreateKeyVault4.PNG
[CertInfo0]: ./media/service-fabric-cluster-creation-via-portal/CertInfo0.PNG
[CertInfo1]: ./media/service-fabric-cluster-creation-via-portal/CertInfo1.PNG
[CertInfo2]: ./media/service-fabric-cluster-creation-via-portal/CertInfo2.PNG
[SecurityCustomOption]: ./media/service-fabric-cluster-creation-via-portal/SecurityCustomOption.PNG
[DownloadCert]: ./media/service-fabric-cluster-creation-via-portal/DownloadCert.PNG
[Summary]: ./media/service-fabric-cluster-creation-via-portal/Summary.PNG
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[cluster-security-cert-installation]: ./media/service-fabric-cluster-creation-via-arm/cluster-security-cert-installation.png
