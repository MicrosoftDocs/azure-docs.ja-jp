---
title: Site Recovery とクラウド ソリューション プロバイダー (CSP) プログラムを使用してマルチ テナント環境で Azure への VMware ディザスター リカバリーを設定する | Microsoft Docs
description: Azure Site Recovery を使用したマルチ テナント環境での VMware ディザスター リカバリーを設定する方法について説明します。
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 77b64f09b7fd1429eb23c4407c729dfc0aafdf2b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52842358"
---
# <a name="set-up-vmware-disaster-recovery-in-a-multi-tenancy-environment-with-the-cloud-solution-provider-csp-program"></a>クラウド ソリューション プロバイダー (CSP) プログラムを使用してマルチ テナント環境での VMware ディザスター リカバリーを設定する

[CSP プログラム](https://partner.microsoft.com/en-US/cloud-solution-provider)は、Office 365、Enterprise Mobility Suite、Microsoft Azure など、Microsoft クラウド サービス向けの優れた連携による事例を促進します。 CSP により、パートナーは顧客とのエンド ツー エンドの関係を築き、顧客にとって最も重要な担当者になることができます。 パートナーは顧客の Azure サブスクリプションをデプロイし、サブスクリプションをカスタマイズされた独自の付加価値サービスと組み合わせることができます。

[Azure Site Recovery](site-recovery-overview.md) を使用すると、パートナーは CSP を介して顧客のディザスター リカバリーを直接管理できます。 また、CSP を使用して Site Recovery 環境をセットアップし、顧客がセルフサービス方式で独自のディザスター リカバリーのニーズを管理できるようにすることも可能です。 どちらのシナリオでも、パートナーは Site Recovery と顧客の間の連絡役となります。 パートナーは顧客関係にサービスを提供し、Site Recovery の利用料金を顧客に請求します。

この記事では、マルチテナントの VMware レプリケーション シナリオのために、パートナーが CSP を使用してテナント サブスクリプションをどのように作成し、管理できるかについて説明します。

## <a name="prerequisites"></a>前提条件

VMware のレプリケーションをセットアップするには、次のことを行う必要があります。

- Azure サブスクリプション、Azure 仮想ネットワーク、ストレージ アカウントなどの Azure リソースを[準備します](tutorial-prepare-azure.md)。
- オンプレミス VMware サーバーと VM を[準備します](vmware-azure-tutorial-prepare-on-premises.md)。
- テナントごとに、テナントの VM や自社の vCenter サーバーと通信できる個別の管理サーバーを作成します。 この管理サーバーへのアクセス権は、パートナーだけが所有するようにしてください。 詳しくは、[マルチテナント環境](vmware-azure-multi-tenant-overview.md)に関する記事をご覧ください。

## <a name="create-a-tenant-account"></a>テナント アカウントを作成する

1. [Microsoft パートナー センター](https://partnercenter.microsoft.com/)から CSP アカウントにサインインします。
2. **[ダッシュボード]** メニューで **[顧客]** を選択します。
3. 表示されたページで、**[顧客の追加]** ボタンをクリックします。
4. **[新しい顧客]** ページで、テナントのアカウント情報の詳細をすべて入力します。

    ![[アカウント情報] ページ](./media/vmware-azure-multi-tenant-csp-disaster-recovery/customer-add-filled.png)

5. その後、**[次へ:サブスクリプション]** をクリックします。
6. サブスクリプションの選択ページで、**[Microsoft Azure]** チェック ボックスを選択します。 他のサブスクリプションは、今すぐ追加することもできますが、後でいつでも追加できます。
7. **[レビュー]** ページで、テナントの詳細を確認し、**[送信]** をクリックします。
8. テナント アカウントを作成すると確認ページが開き、そのサブスクリプションの既定のアカウントとパスワードの詳細が表示されます。 情報を保存し、必要に応じて、後で Azure Portal のサインイン ページでパスワードを変更します。

この情報はテナントと現状のまま共有できます。また、必要に応じて個別のアカウントを作成し、共有することもできます。

## <a name="access-the-tenant-account"></a>テナント アカウントにアクセスする

テナントのサブスクリプションには、Microsoft パートナー センターのダッシュボードからアクセスできます。

1. **[顧客]** ページで、テナント アカウントの名前をクリックします。
2. テナント アカウントの **[サブスクリプション]** ページで、既存のアカウント サブスクリプションを監視したり、必要に応じてサブスクリプションをさらに追加したりできます。
3. テナントのディザスター リカバリー操作を管理するには、**[すべてのリソース (Azure Portal)]** を選択します。 これにより、テナントの Azure サブスクリプションへのアクセスが許可されます。

    ![[すべてのリソース] のリンク](./media/vmware-azure-multi-tenant-csp-disaster-recovery/all-resources-select.png)  

4. Azure Portal の右上にある Azure Active Directory のリンクをクリックすると、アクセスを確認できます。

    ![Azure Active Directory のリンク](./media/vmware-azure-multi-tenant-csp-disaster-recovery/aad-admin-display.png)

これで、Azure Portal からテナントのすべての Site Recovery 操作を実行および管理できるようになりました。 管理されたディザスター リカバリーを行うために CSP を使用してテナント サブスクリプションにアクセスするには、上記のプロセスに従います。

## <a name="assign-tenant-access-to-the-subscription"></a>サブスクリプションにテナント アクセスを割り当てる

1. ディザスター リカバリー インフラストラクチャがセットアップされていることを確認します。 ディザスター リカバリーがマネージドかセルフサービスかに関係なく、パートナーは CSP ポータルからテナント サブスクリプションにアクセスします。 パートナーは自社のコンテナーをセットアップし、インフラストラクチャをテナント サブスクリプションに登録します。
2. テナントに、[作成したアカウント](#create-a-tenant-account)を提供します。
3. パートナーは CSP ポータルを通じ、テナント サブスクリプションに新しいユーザーを追加することもできます。手順は次のとおりです。

    a) テナントの CSP サブスクリプション ページに移動し、**[Users and licenses]\(ユーザーとライセンス\)** を選択します。

      ![テナントの CSP サブスクリプション ページ](./media/vmware-azure-multi-tenant-csp-disaster-recovery/users-and-licences.png)

    b) 関連する詳細を入力してアクセス許可を選択するか、CSV ファイルでユーザーのリストをアップロードして、新しいユーザーを作成します。
    
    c) 新しいユーザーを作成したら、Azure Portal に戻ります。 **[サブスクリプション]** ページで、関連するサブスクリプションを選択します。

    d) **[アクセス制御 (IAM)]** を選択した後、**[ロール割り当て]** をクリックします。

    e) **[ロールの割り当ての追加]** をクリックして、関連するアクセス レベルを持つユーザーを追加します。 CSP ポータルで作成したユーザーが、[ロール割り当て] タブに表示されます。

      ![ユーザーの追加](./media/vmware-azure-multi-tenant-csp-disaster-recovery/add-user-subscription.png)

- ほとんどの管理操作には、"*共同作成者*" ロールで十分です。 このアクセス レベルのユーザーは、サブスクリプションに対して、アクセス レベルの変更 ("*所有者*" レベルのアクセス権が必要) を除くすべての操作を実行できます。
- Site Recovery には、必要に応じてアクセス レベルをさらに制限するために使用できる、3 つの[定義済みユーザー ロール](site-recovery-role-based-linked-access-control.md)が用意されています。

## <a name="multi-tenant-environments"></a>マルチテナント環境

主なマルチテナント モデルとして次の 3 つがあります。

* **共有ホスティング サービス プロバイダー (HSP)**:パートナーが物理インフラストラクチャを所有し、共有リソース (vCenter、データセンター、物理記憶領域など) を使用して、複数のテナント VM を同じインフラストラクチャ上でホストします。 パートナーは管理サービスとしてディザスター リカバリーの管理を提供できます。また、テナントはセルフサービス ソリューションとしてディザスター リカバリーを所有できます。

* **専用ホスティング サービス プロバイダー**:パートナーが物理インフラストラクチャを所有しますが、専用リソース (複数の vCenter、物理データストアなど) を使用して個別のインフラストラクチャ上の各テナントの VM をホストします。 パートナーは管理サービスとしてディザスター リカバリーの管理を提供できます。また、テナントはセルフサービス ソリューションとしてディザスター リカバリーを所有できます。

* **管理サービス プロバイダー (MSP)**:VM をホストする物理インフラストラクチャを顧客が所有し、パートナーがディザスター リカバリーを有効にして管理します。

この記事で説明したようにテナント サブスクリプションを設定することにより、任意の関連するマルチテナント モデルで顧客の有効化をすばやく開始できます。 さまざまなマルチテナント モデルおよびオンプレミスのアクセス制御の有効化について詳しくは、[こちら](vmware-azure-multi-tenant-overview.md)をご覧ください。

## <a name="next-steps"></a>次の手順
- Azure Site Recovery デプロイを管理するための[ロールベースのアクセス制御](site-recovery-role-based-linked-access-control.md)に関する詳細を学習する。
- VMware から Azure への[レプリケーション アーキテクチャ](vmware-azure-architecture.md)について学習する。
- VMware VM を Azure にレプリケートするための[チュートリアルを確認する](vmware-azure-tutorial.md)。
VMware VM を Azure にレプリケートするための[マルチテナント環境](vmware-azure-multi-tenant-overview.md)について詳しく学習する。
