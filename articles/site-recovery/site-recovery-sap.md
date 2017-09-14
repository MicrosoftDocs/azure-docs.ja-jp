---
title: "Azure Site Recovery を使用して多層 SAP NetWeaver アプリケーションのデプロイを保護する | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用して SAP NetWeaver アプリケーションのデプロイを保護する方法について説明します。"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: manayar
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 951980eeba61e53c983d5b23c301c81eee9528bd
ms.contentlocale: ja-jp
ms.lasthandoff: 08/08/2017

---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-using-azure-site-recovery"></a>Azure Site Recovery を使用して多層 SAP NetWeaver アプリケーションのデプロイを保護する

多くの場合、大規模および中規模の SAP デプロイには、何らかの形式のディザスター リカバリー ソリューションがあります。  SAP などのアプリケーションに移行するコア ビジネス プロセスが増えているため、堅牢でテスト可能なディザスター リカバリー ソリューションの重要性は高まっています。  Azure Site Recovery は SAP アプリケーションを使用してテストされ、統合されています。また、ほとんどのオンプレミス ディザスター リカバリー ソリューションよりも優れた機能で、競合するソリューションよりも低い総保有コスト (TCO) です。
Azure Site Recovery を使用すると、次のことができます。
* コンポーネントを Azure にレプリケートすることによって、オンプレミスで実行されている SAP NetWeaver および非 NetWeaver 運用アプリケーションの保護を有効にします。
* コンポーネントを他の Azure データセンターにレプリケートすることによって、Azure で実行されている SAP NetWeaver および非 NetWeaver 運用アプリケーションの保護を有効にします。
* Site Recovery を使用して SAP デプロイを Azure に移行することで、クラウド移行を簡略化します。
* SAP アプリケーションをテストするためのオンデマンドの運用クローンを作成することによって、SAP プロジェクトのアップグレード、テスト、および、プロトタイプ作成を簡略化します。

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して SAP NetWeaver アプリケーションのデプロイを保護する方法について説明します。 この記事では、Azure Site Recovery を使用して別の Azure データセンターにレプリケートして Azure 上の 3 層の SAP NetWeaver デプロイを保護するベスト プラクティス、サポートされるシナリオと構成、テスト フェールオーバー (ディザスター リカバリー ドリル) と実際のフェールオーバーという両方のフェールオーバーを実行する方法について説明します。


## <a name="prerequisites"></a>前提条件
開始する前に、以下を理解していることを確認してください。

1. [Azure への仮想マシンのレプリケート](azure-to-azure-walkthrough-enable-replication.md)
2. [復旧ネットワークの設計](site-recovery-azure-to-azure-networking-guidance.md)方法
3. [Azure へのテスト フェールオーバーの実行](azure-to-azure-walkthrough-test-failover.md)
4. [Azure へのフェールオーバーの実行](site-recovery-failover.md)
5. [ドメイン コントローラーのレプリケート](site-recovery-active-directory.md)方法
6. [SQL Server のレプリケート](site-recovery-sql.md)方法

## <a name="supported-scenarios"></a>サポートされるシナリオ
Azure Site Recovery を使用すると、次のシナリオに適したディザスター リカバリー ソリューションを実装できます。
* ある Azure データ セットで実行されている SAP システムを、別の Azure データセンターにレプリケートします (Azure 間の DR)。構成方法は[こちら](https://aka.ms/asr-a2a-architecture)です。
* VMware (または物理) サーバー上で実行されている SAP システムを、Azure データセンター内の DR サイトにレプリケートします (VMware から Azure への DR)。[こちら](https://aka.ms/asr-v2a-architecture)の構成方法のように、いくつかの追加コンポーネントが必要です。
* オンプレミスの Hyper-V 上で実行されている SAP システムを、Azure データセンター内の DR サイトにレプリケートします (Hyper-V から Azure への DR)。[こちら](https://aka.ms/asr-h2a-architecture)の構成方法のように、いくつかの追加コンポーネントが必要です。

このドキュメントでは、1 つ目の Azure 間の DR の場合を使用し、Azure Site Recovery の SAP ディザスター リカバリー機能について説明します。 Azure Site Recovery のレプリケーションはアプリケーションに依存していないため、ここで説明するプロセスは、他のシナリオにも有効であると見込まれます。

### <a name="required-foundation-services"></a>必要な基礎サービス
このドキュメントでは、次の基礎サービスがすべてデプロイ済みであるというシナリオです。
* ExpressRoute またはサイト間仮想プライベート ネットワーク (VPN)
* Azure で実行されている少なくとも 1 つの Active Directory ドメイン コントローラーと DNS サーバー

上記のインフラストラクチャは、Azure Site Recovery にデプロイする前に確立することをお勧めします。


## <a name="typical-sap-application-deployment"></a>一般的な SAP アプリケーションの展開
大規模な SAP 環境の場合、通常は 6 から 20 個の SAP アプリケーションをデプロイします。  このようなアプリケーションのほとんどは、SAP NetWeaver ABAP または Java エンジンに基づいています。  これらのコア NetWeaver アプリケーションをサポートしているのは、小規模な固有の NetWeaver SAP ではない多数のスタンドアロン エンジンと、通常は SAP ではないいくつかのアプリケーションです。  

環境内で実行されているすべての SAP アプリケーションのインベントリを作成し、デプロイ モード (2 層または 3 層)、バージョン、パッチ、サイズ、チャーン レート、ディスク永続化の要件を決定することが重要です。

![デプロイ パターン](./media/site-recovery-sap/sap-typical-deployment.png)

SAP データベースの永続化層は、SQL Server AlwaysOn、Oracle DataGuard、HANA System Replication などのネイティブ DBMS ツールで保護する必要があります。 Azure Site Recovery ではクライアント層も保護されませんが、DNS への反映の遅延、セキュリティ、DR データセンターのセキュリティとリモート アクセスなど、この層に影響するトピックを検討することが重要です。

Azure Site Recovery は、(A)SCS などのアプリケーション レイヤーに推奨されるソリューションです。 NetWeaver ではない SAP アプリケーションや、SAP ではないアプリケーションなど、他のアプリケーションが SAP デプロイ全体の一部を構成しているため、Azure Site Recovery で保護する必要があります。

## <a name="replicate-virtual-machines"></a>仮想マシンのレプリケート
[このガイダンス](azure-to-azure-walkthrough-enable-replication.md)に従って、すべての SAP アプリケーション仮想マシンを Azure DR データセンターに移行します。

静的 IP を使用している場合は、[コンピューティングとネットワーク] 設定の [ネットワーク インターフェイス カード] セクションで、対象の仮想マシンの IP を指定できます。

![[ターゲット IP]](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>復旧計画の作成
復旧計画では、多層アプリケーションのさまざまな階層のフェールオーバーをシーケンス処理できるため、アプリケーションの整合性が維持されます。 [こちら](site-recovery-create-recovery-plans.md)の手順に従って、多層 Web アプリケーションの復旧計画を作成します。

### <a name="adding-scripts-to-the-recovery-plan"></a>復旧計画へのスクリプトの追加
フェールオーバーまたはテスト フェールオーバーの後にアプリケーションが正常に機能するように、Azure の仮想マシンに対して操作を実行することが必要な場合があります。 DNS エントリの更新、バインドと接続の変更などのフェールオーバー後の操作は、[この記事](site-recovery-create-recovery-plans.md#add-scripts)の説明に従って、対応するスクリプトを復旧計画に追加することで自動化できます。

### <a name="dns-update"></a>DNS の更新
動的 DNS 更新用に DNS が構成されている場合は、通常、仮想マシンの起動後に、新しい IP で DNS を更新します。 仮想マシンの新しい IP で DNS を更新する明示的な手順を追加する場合、この [DNS の IP を更新するスクリプト](https://aka.ms/asr-dns-update)を復旧計画グループの事後アクションとして追加します。  

## <a name="example-azure-to-azure-deployment"></a>Azure 間のデプロイ例
以下の図は、Azure Site Recovery の Azure 間の DR シナリオを示しています。
* プライマリ データセンターはシンガポール (Azure 南東アジア) にあり、DR データセンターは香港 (Azure 東アジア) にあります。  このシナリオでは、シンガポールに同期モードの SQL Server AlwaysOn を実行している 2 つの VM をデプロイすることで、ローカルの高可用性を実現しています。
* ファイル共有 ASCS は、SAP の単一障害点で高可用性を実現するために使用できます。 ファイル共有 ASCS の場合、クラスター共有ディスクと、SIOS などのアプリケーションは不要です。
* DBMS 層の DR 保護は、非同期レプリケーションを使用して達成されます。
* このシナリオは、"対称 DR" を示しています。対称 DR という用語は、運用環境の完全なレプリカである DR ソリューションを説明するときに使用されます。そのため、DR SQL Server ソリューションにはローカルの高可用性があります。 データベース層で対称 DR の使用は必須ではないため、多くのお客様は、クラウド デプロイの柔軟さを利用して、DR イベント後すぐにローカル 高可用ノードを構築します。
* 次の図は、Azure Site Recovery でレプリケートされる SAP NetWeaver ASCS およびアプリケーション サーバー層を示しています。

![レプリケーション シナリオ](./media/site-recovery-sap/sap-replication-scenario.png)

## <a name="doing-a-test-failover"></a>テスト フェールオーバーの実行
[このガイダンス](azure-to-azure-walkthrough-test-failover.md)に従って、テスト フェールオーバーを実行します。

1.  Azure Portal に移動し､Recovery Service コンテナーを選択します｡
2.  SAP アプリケーション用に作成した復旧計画をクリックします｡
3.  [テスト フェールオーバー] をクリックします。
4.  テスト フェールオーバー プロセスを開始する復旧ポイントと Azure 仮想ネットワークを選択します。
5.  セカンダリ環境が立ち上がったら、検証を行うことができます。
6.  検証が完了したら、[テスト フェールオーバーのクリーンアップ] をクリックし、フェールオーバー環境をクリーンアップします。

## <a name="doing-a-failover"></a>フェールオーバーの実行
[このガイダンス](site-recovery-failover.md)に従って、フェールオーバーを実行します。

1.  Azure Portal に移動し､Recovery Service コンテナーを選択します｡
2.  SAP アプリケーション用に作成した復旧計画をクリックします｡
3.  [フェールオーバー] をクリックします。
4.  フェールオーバー プロセスを開始する復旧ポイントを選択します。

## <a name="next-steps"></a>次のステップ
Azure Site Recovery を使用して、SAP NetWeaver デプロイ用のディザスター リカバリー ソリューションを構築する方法については、[こちらのホワイトペーパー](http://aka.ms/asr-sap)を参照してください。 このホワイトペーパーでは、さまざまな SAP アプリケーションに関する推奨事項、Azure 上の SAP でサポートされるアプリケーションと VM の種類、ディザスター リカバリー ソリューションに適したテスト計画についても説明しています。

[Site Recovery を使用した他のワークロードのレプリケートに関する記事](site-recovery-workload.md)を参照してください。

