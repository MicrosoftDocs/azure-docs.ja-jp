---
title: "Azure への VMware VM レプリケーションのためのマルチテナント サポート (CSP プログラム) | Microsoft Docs"
description: "マルチテナント環境に Azure Site Recovery をデプロイし、Azure Portal で CSP プログラムを使用して、オンプレミスの VMware 仮想マシンの Azure へのレプリケーション、フェールオーバー、復旧を調整する方法について説明します。"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: jwhit
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2017
ms.author: manayar
translationtype: Human Translation
ms.sourcegitcommit: 3b606aa6dc3b84ed80cd3cc5452bbe1da6c79a8b
ms.openlocfilehash: ed484afc59bbf48490e3ff4389e8e28c71a5e471
ms.lasthandoff: 01/30/2017


---
# <a name="multi-tenant-support-in-azure-site-recovery-for-replicating-vmware-virtual-machines-to-azure-through-the-csp-program"></a>CSP プログラムを使用して VMware 仮想マシンを Azure にレプリケートするための Azure Site Recovery でのマルチテナントのサポート

Azure Site Recovery では、テナント サブスクリプションのマルチテナント環境をサポートします。 マルチテナントは、CSP プログラムを使用して作成され、管理されているテナント サブスクリプションでもサポートされます。 この記事では、VMware と Azure 間のマルチテナント シナリオの実装と管理に関する詳細なガイダンスを示します。 また、CSP を使用したテナント サブスクリプションの作成と管理についても詳しく説明します。

このガイダンスでは、Azure への VMware 仮想マシンのレプリケーションに関する既存のドキュメントを活用しています。 このガイダンスはその[ドキュメント](site-recovery-vmware-to-azure.md)と併用してください。

## <a name="multi-tenant-environments"></a>マルチテナント環境
主なマルチテナント モデルとして次の&3; つがあります。

1.    **共有ホスティング サービス プロバイダー (HSP)** - このモデルでは、パートナーが物理インフラストラクチャを所有し、共有リソース (vCenter、データセンター、物理記憶域など) を使用して同じインフラストラクチャ上の複数のテナントの VM をホストします。 DR 管理は、管理されたサービスとしてパートナーが提供することも、セルフサービスの DR ソリューションとしてテナントが所有することもできます。
2.    **専用ホスティング サービス プロバイダー** - このモデルでは、パートナーが物理インフラストラクチャを所有しますが、専用リソース (複数の vCenter、物理データストアなど) を使用して個別のインフラストラクチャ上の各テナントの VM をホストします。 このモデルでも、DR 管理はパートナーが提供することも、テナントによるセルフサービスにすることもできます。
3.    **管理サービス プロバイダー (MSP)** - このモデルでは、VM をホストする物理インフラストラクチャを顧客が所有し、パートナーが DR を有効にして管理します。

## <a name="shared-hosting-multi-tenant-guidance"></a>共有ホスティング マルチテナント ガイダンス
このガイダンスでは、共有ホスティング シナリオについて詳しく説明します。 他の&2; つのシナリオは共有ホスティング シナリオのサブセットであり、同じ原則を使用します。 違いについては、この共有ホスティング ガイダンスの最後に説明します。

マルチテナント シナリオの基本的な要件は、さまざまなテナントの分離です。つまり、テナントは別のテナントがホストしている内容を監視できないようにする必要があります。 この要件はセルフサービス環境では非常に重要な場合がありますが、完全にパートナー管理された環境ではセルフサービス環境ほど重要ではありません。 このガイダンスは、テナントの分離が必須であることを前提としています。

アーキテクチャは次のようになります。

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/shared-hosting-scenario.png)

**図 1: 1 つの vCenter を使用する共有ホスティング シナリオ**

上の図に示すように、各顧客は個別の管理サーバーを使用します。 これにより、テナントによるアクセスをテナント固有の VM に制限してテナントの分離を実現できます。 VMware 仮想マシンのレプリケーション シナリオでは、構成サーバーを使用して、VM の検出とエージェントのインストールに使用するアカウントを管理します。 vCenter アクセス制御による VM の検出の制限を追加することによって、マルチテナント環境でも同じ原則に従います。

データ分離要件では、機密性の高いすべてのインフラストラクチャ情報 (アクセス資格情報など) がテナントから開示されていることが必要となります。 そのため、管理サーバーのすべてのコンポーネント (構成サーバー (CS)、プロセス サーバー (PS)、マスター ターゲット サーバー (MT)) をパートナーの排他的制御下に置くことをお勧めします。 これにはスケールアウト PS が含まれます。

### <a name="every-cs-under-the-multi-tenant-scenario-uses-two-accounts"></a>マルチテナント シナリオのすべての CS が使用する&2; つのアカウント

- **vCenter アクセス アカウント**: このアカウントはテナントの VM を検出する際に使用されます。このアカウントには、vCenter のアクセス許可が割り当てられています (次のセクションで説明)。 パートナーは、偶発的なアクセス リークを防ぐために、構成ツールでこれらの資格情報を自分で入力することをお勧めします。
- **仮想マシン アクセス アカウント**: このアカウントは、自動プッシュによってテナントの VM にモビリティ エージェントをインストールする際に使用されます。 通常、これはドメイン アカウントであり、テナントがパートナーに提供することも、パートナーが直接管理することもできます。 テナントが詳細情報をパートナーと直接共有することを望んでいない場合、CS に時間限定でアクセスして資格情報を入力することをテナントに許可することができます。また、パートナーの支援を得て、テナントがモビリティ エージェントを手動でインストールすることもできます。

### <a name="requirements-for-vcenter-access-account"></a>vCenter アクセス アカウントの要件

前のセクションで説明したように、CS は特別なロールが割り当てられたアカウントで構成する必要があります。 このロールの割り当ては、各 vCenter オブジェクトの vCenter アクセス アカウントに対して行い、子オブジェクトには伝達されないようにする必要があります。 アクセスが伝達されると、他のオブジェクトにも誤ってアクセスする可能性があるため、このようにロールを割り当てることでテナントの分離を確保できます。

![permissions-without-propagation](./media/site-recovery-multi-tenant-support-vmware-using-csp/assign-permissions-without-propagation.png)

別の方法として、データセンター オブジェクトでユーザー アカウントとロールを割り当て、子オブジェクトに伝達します。その後、特定のテナントにアクセスできないようにする必要があるすべてのオブジェクト (他のテナントの VM など) に対する "アクセスなし" ロールを、そのアカウントに割り当てる必要があります。 作成されるすべての新しい子オブジェクトにも、親から継承されたアクセス権が自動的に付与されるので、この方法は煩雑であると同時に、予期しないアクセス制御が行われます。 そのため、最初の方法を使用することをお勧めします。

vCenter アカウントのアクセス手順は次のとおりです。

1.    定義済みの "読み取り専用" ロールを複製して新しいロールを作成し、ロールにわかりやすい名前を付けます (この例では Azure_Site_Recovery を使用)。
2.    このロールに次のアクセス許可を割り当てます。
 *    データストア -> 空間の割り当て、データストアの参照、ファイルの低レベルの操作、ファイルの削除、仮想マシン ファイルの更新
 *    ネットワーク -> ネットワークの割り当て
 *    リソース -> リソース プールへの VM の割り当て、電源がオフの VM の移行、電源がオンの VM の移行
 *    タスク -> タスクの作成、タスクの更新
 *    仮想マシン -> 構成
 *    仮想マシン -> 対話 -> 質問への回答、デバイス接続、CD メディアの構成、フロッピー メディアの構成、電源オフ、電源オン、VMware ツールのインストール
 *    仮想マシン -> インベントリ -> 作成、登録、登録解除
 *    仮想マシン -> プロビジョニング -> 仮想マシンのダウンロードの許可、仮想マシン ファイルのアップロードの許可
 *    仮想マシン -> スナップショット -> スナップショットの削除

    ![role-permissions](./media/site-recovery-multi-tenant-support-vmware-using-csp/edit-role-permissions.png)

3.    次のように、さまざまなオブジェクトの vCenter アカウント (テナントの CS で使用) にアクセス レベルを割り当てます。

| **Object** | **ロール** | **解説** |
| --- | --- | --- |
| vCenter | 読み取り専用 | これが必要となるのは、さまざまなオブジェクトを管理するために vCenter によるアクセスを許可する場合だけです。 このアカウントをテナントに提供することも、vCenter での管理操作に使用することもない場合は、このアクセス許可を削除できます。 |
| データセンター | Azure_Site_Recovery |  |
| ホストとホスト クラスター | Azure_Site_Recovery | アクセスが確実にオブジェクト レベルになるので、フェールオーバー前とフェールバック後にテナントの VM を保持することになるこれらのホストだけがアクセスできます。 |
| データストアとデータストア クラスター | Azure_Site_Recovery | 同上 |
| ネットワーク | Azure_Site_Recovery |  |
| 管理サーバー | Azure_Site_Recovery | これには、すべてのコンポーネント (CS、PS、MT) へのアクセスが含まれます (いずれかが CS コンピューターの外部にある場合)。 |
| テナント VM | Azure_Site_Recovery | 特定のテナントのすべての新しいテナント VM もこのアクセスを確実に取得します。そうしないと、Azure Portal から検出できなくなります。 |

これで vCenter アカウントのアクセスが完了しました。 これは、フェールバック操作を完了するための最小限のアクセス許可要件を満たしています。 これらのアクセス許可を既存のポリシーで使用することもできます。 設定された既存のアクセス許可を変更して、2. で説明したロールのアクセス許可を追加するだけです。

フェールバック機能なしに、フェールオーバー状態になるまで DR 操作を制限するには、上記の手順に従いますが、vCenter アクセス アカウントに "Azure_Site_Recovery" ロールを割り当てるのではなく、"読み取り専用" ロールだけをこのアカウントに割り当てます。 このアクセス許可セットでは、VM のレプリケーションとフェールオーバーは許可されますが、フェールバックは許可されません。 上記のプロセスの他のすべてのものはそのままになります。 テナントの分離を確保し、VM の検出を制限するために、すべてのアクセス許可が引き続きオブジェクト レベルでのみ割り当てられ、子オブジェクトには伝達されません。

## <a name="other-multi-tenant-environments"></a>その他のマルチテナント環境

上記のガイダンスでは、共有ホスティング ソリューションのマルチテナント環境のセットアップ方法について詳しく説明しました。 他の&2; つの主要ソリューションは、専用ホスティングと管理されたサービスです。 これらのアーキテクチャを以下に示します。

### <a name="dedicated-hosting-solution"></a>専用ホスティング ソリューション

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/dedicated-hosting-scenario.png)

**図 2: 複数の vCenter を使用する専用ホスティング シナリオ**

ここでのアーキテクチャの違いは、各テナントのインフラストラクチャがそのテナント専用にプロビジョニングされることです。 この場合も、ホスティング プロバイダーは共有ホスティングで詳述した CSP の手順に従う必要がありますが、テナントは個別の vCenter によって分離されているため、テナントの分離を気にする必要はありません。 CSP のプロビジョニングに変わりはありません。

### <a name="managed-service-solution"></a>管理されたサービス ソリューション

![architecture-shared-hsp](./media/site-recovery-multi-tenant-support-vmware-using-csp/managed-service-scenario.png)

**図 3: 複数の vCenter を使用する管理されたサービス シナリオ**

ここでのアーキテクチャの違いは、各テナントのインフラストラクチャが他のテナントから物理的にも分離されていることです。 通常、このシナリオは、テナントがインフラストラクチャを所有し、ソリューション プロバイダーに DR を管理してもらう場合に発生します。 この場合も、パートナーは共有ホスティングで詳述した CSP の手順に従う必要がありますが、テナントは異なるインフラストラクチャによって物理的に分離されているため、テナントの分離を気にする必要はありません。 CSP のプロビジョニングに変わりはありません。


## <a name="csp-program-overview"></a>CSP プログラムの概要
Microsoft のクラウド ソリューション プロバイダー (CSP) [プログラム](https://partner.microsoft.com/en-US/cloud-solution-provider)は、O365、EMS、Microsoft Azure など、すべての Microsoft クラウド サービスを提供するために、パートナーとの連携による優れた成果の事例を促進します。 このプログラムにより、パートナーは顧客とのエンド ツー エンドの関係を築き、顧客にとって最も重要な担当者になることができます。 CSP により、パートナーは顧客の Azure サブスクリプションをデプロイし、これらのサブスクリプションをカスタマイズされた独自の付加価値サービスと組み合わせることができます。

Azure Site Recovery の場合、パートナーは CSP によって顧客の完全な障害復旧ソリューションを直接管理できます。また、CSP を使用して Azure Site Recovery 環境をセットアップし、顧客がセルフサービス方式で独自の DR のニーズに対処できるようにすることも可能です。 どちらのシナリオでも、パートナーは Azure Site Recovery と最終的な顧客間の連絡係となります。パートナーは顧客にサービスを提供し、Azure Site Recovery の利用料金を顧客に請求します。

## <a name="creating-and-managing-tenant-accounts"></a>テナント アカウントの作成と管理

### <a name="step-0-prerequisite-check"></a>手順 0: 前提条件を確認する

VM の前提条件は、Azure Site Recovery の[ドキュメント](site-recovery-vmware-to-azure.md)に記載されているものと同じです。 これらの前提条件に加え、CSP によるテナント管理を進める前に前述のアクセス制御を設定する必要があります。 テナントの VM やパートナーの vCenter と通信できるように、テナントごとに個別の管理サーバーを作成します。 パートナーだけがこのサーバーへのアクセス権を持ちます。

### <a name="step-1-create-tenant-account"></a>手順 1: テナント アカウントを作成する

1.    [パートナー センター](https://partnercenter.microsoft.com/)から CSP アカウントにログインします。 左側の [ダッシュボード] メニューで [顧客] を選択します。

    ![csp-dashboard](./media/site-recovery-multi-tenant-support-vmware-using-csp/csp-dashboard-display.png)

2.    表示されたページで、[顧客の追加] をクリックします。

    ![add-customer](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-new-customer.png)

3.    [新しい顧客] ページで、テナントのアカウント情報の詳細をすべて入力し、[次へ: サブスクリプション] をクリックします。

    ![fill-details](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-add-filled.png)

4.    サブスクリプションの選択ページで、下にスクロールして [Microsoft Azure] サブスクリプションを追加します。 他のサブスクリプションは、今すぐ追加することもできますが、後でいつでも追加できます。

    ![add-subscription](./media/site-recovery-multi-tenant-support-vmware-using-csp/azure-subscription-selection.png)

5.    操作を続行し、次のページで入力済みのテナントの詳細をすべて確認し、[送信] をクリックします。

    ![customer-summary](./media/site-recovery-multi-tenant-support-vmware-using-csp/customer-summary-page.png)

6.    顧客が作成されると、そのサブスクリプションの既定のアカウントとパスワードの詳細が含まれた確認ページが表示されます。 情報を保存し、必要に応じて、後で Azure Portal にログインしてパスワードを変更します。 この情報はテナントと現状のまま共有できます。また、必要に応じて別のアカウントを作成し、共有することもできます。

### <a name="step-2-access-tenant-account"></a>手順 2: テナント アカウントにアクセスする

1.    テナントのサブスクリプションには、手順 1 で説明したダッシュボードの [顧客] ページからアクセスできます。 このページに移動し、先ほど作成したテナント アカウントの名前をクリックします。
2.    テナント アカウントの [サブスクリプション] セクションが表示されます。ここでは、アカウントの既存のサブスクリプションを監視したり、必要に応じてサブスクリプションをさらに追加したりできます。 テナントの DR 操作を管理するには、ページの右側にある [すべてのリソース (Azure Portal)] を選択します。

    ![all-resources](./media/site-recovery-multi-tenant-support-vmware-using-csp/all-resources-select.png)

3.    [すべてのリソース] をクリックすると、テナントの Azure サブスクリプションへのアクセスが許可されます。Azure Portal の右上隅に表示されている AAD を確認すると、同じ内容を確認できます。

    ![aad-admin](./media/site-recovery-multi-tenant-support-vmware-using-csp/aad-admin-display.png)

これで、Azure Portal からテナントに対して Site Recovery のすべての操作を実行し、DR 操作を管理できるようになりました。 管理された DR を実行するために CSP を使用してテナント サブスクリプションにアクセスするたびに、上記のプロセスに従う必要があります。

### <a name="step-3-deploy-resources-to-tenant-subscription"></a>手順 3: テナント サブスクリプションにリソースをデプロイする
1.    Azure Portal でリソース グループを作成し、通常のプロセスごとに Recovery Services コンテナーをデプロイします。 コンテナー登録キーをダウンロードします。
2.    コンテナー登録キーを使用して、テナントの CS を登録します。
3.    vCenter アクセス アカウントと VM アクセス アカウントの&2; つのアカウントの資格情報を入力します。

    ![config-accounts](./media/site-recovery-multi-tenant-support-vmware-using-csp/config-server-account-display.png)

### <a name="step-4-register-site-recovery-infrastructure-to-recovery-services-vault"></a>手順 4: Site Recovery インフラストラクチャを Recovery Services コンテナーに登録する
1.    Azure Portal を開き、作成済みのコンテナーで、前の手順で登録した CS に vCenter サーバーを登録します。 この操作には、vCenter アクセス アカウントを使用します。
2.    通常のプロセスごとに、Site Recovery の "インフラストラクチャの準備" プロセスを完了します。
3.    これで VM をレプリケートする準備ができました。 VM 選択ブレードの [レプリケート] に、目的のテナントの VM だけが表示されていることを確認します。

    ![tenant-vms](./media/site-recovery-multi-tenant-support-vmware-using-csp/tenant-vm-display.png)

### <a name="step-5-assign-tenant-access-to-subscription"></a>手順 5: サブスクリプションにテナント アクセスを割り当てる

セルフサービスの DR の場合、手順 1 の項目 6. で説明したアカウントの詳細をテナントに提供する必要があります。 これは、パートナーが DR インフラストラクチャをセットアップした後に実行します。 DR の種類 (管理またはセルフサービス) に関係なく、パートナーは CSP ポータルからのみテナント サブスクリプションにアクセスし、コンテナーをセットアップして、パートナーが所有するインフラストラクチャをテナント サブスクリプションに登録する必要があります。

次のように、パートナーは CSP ポータルで新しいユーザーをテナント サブスクリプションに追加することもできます。

1.    特定のテナントの CSP サブスクリプション ページに移動し、[Users and licenses (ユーザーとライセンス)] を選択します。

    ![user-licenses](./media/site-recovery-multi-tenant-support-vmware-using-csp/users-and-licences.png)

    適切な詳細を入力し、アクセス許可を選択するか、CSV ファイルを使用してユーザーのリストをアップロードすることで、新しいユーザーを作成できます。
2.    ユーザーが作成されたら、Azure Portal に戻り、サブスクリプション ブレードで該当するサブスクリプションを選択します。
3.    表示された新しいブレードで、[アクセス制御 (IAM)] を選択し、[+追加] をクリックして適切なアクセスレベルでユーザーを追加します。 CSP ポータルで作成されたユーザーは、アクセス レベルをクリックした後に表示されるブレードに自動的に表示されます。

    ![user-subscription](./media/site-recovery-multi-tenant-support-vmware-using-csp/add-user-subscription.png)

    ほとんどの管理操作には、共同作成者ロールで十分です。 このアクセス レベルのユーザーは、サブスクリプションに対して、アクセス レベルの変更 (所有者レベルのアクセス権が必要) を除くすべての操作を実行できます。 必要に応じて、アクセス レベルを微調整することもできます。

