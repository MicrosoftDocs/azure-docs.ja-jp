---
title: SAP on Azure:Azure でサポートされている SAP ソフトウェア
description: Azure でのデプロイがサポートされている SAP ソフトウェアについて説明します
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/12/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dfd560754d92c97b435ff7643f6da4d3e62e8cfd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673541"
---
# <a name="what-sap-software-is-supported-for-azure-deployments"></a>Azure デプロイでサポートされている SAP ソフトウェア
この記事では、Azure デプロイでサポートされている SAP ソフトウェアと、必要なオペレーティング システム リリースまたは DBMS リリースを確認する方法について説明します。

現在お使いの SAP ソフトウェアがサポートされているかどうかや、Azure でお使いの SAP ソフトウェアでサポートされている OS と DBMS のリリースを評価するには、以下にアクセスする必要があります。

- SAP サポート ノート
- SAP 製品の可用性マトリックス



## <a name="general-restrictions-for-sap-workload"></a>SAP ワークロードに関する一般的な制限事項
SAP ワークロードに使用できる Azure IaaS サービスは、x86-64 または x64 ハードウェアに限定されています。 SAP ワークロードに適用される Sparc または Power CPU ベースのプランはありません。 IBM メインフレームや AS400 などのハードウェア アーキテクチャ専用のオペレーティング システムでアプリケーションを実行しているお客様の場合や、HP-UX、Solaris、AIX のオペレーティング システムが使用されている場合は、DBMS を含む SAP アプリケーションを次のいずれかのオペレーティング システムに変更する必要があります。

- x86-64 プラットフォーム用の Windows Server 64 ビット
- x86-64 プラットフォーム用の SUSE Linux 64 ビット
- x86-64 プラットフォーム用の Red Hat Linux 64 ビット
- x86-64 プラットフォーム用の Oracle Linux 64 ビット

SAP ソフトウェアとの組み合わせでは、他の OS リリースまたは Linux ディストリビューションはサポートされていません。 特定のバージョンとケースの正確な詳細については、このドキュメントで後から説明します。


## <a name="you-start-here"></a>ここから始める
出発点は、[SAP サポート ノート #1928533](https://launchpad.support.sap.com/#/notes/1928533) です。 この SAP ノートを上から下に読んでいくと、サポートされるソフトウェアと VM の領域がいくつか示されています。

最初のセクションには、一般に Azure VM の SAP ソフトウェアでサポートされているオペレーティング システム リリースの最小要件の一覧が示されています。 該当する最小要件を満たさずに、これらのオペレーティング システムの以前のリリースを実行している場合は、OS リリースをその最小リリースまたはそれ以上に新しいリリースにアップグレードする必要があります。 一般に Azure がこのようなオペレーティング システムの以前のリリースをサポートすることがあるのは間違いありません。 ただし、記載されている制限または最小リリースは、実施されたテストと適格性評価に基づくものであり、これ以上さかのぼることはありません。 


> [!NOTE]
>より新しい OS リリースを必要とする特定の VM の種類、HANA L インスタンス、または SAP ワークロードがいくつかあります。 そのようなケースについては、ドキュメント全体で言及されています。 そのようなケースは、SAP ノートまたは他の SAP に関する出版物で明確に文書化されています。

次のセクションでは、サポートされているリリースでサポートされる一般的な SAP プラットフォームのほか、さらに重要なこととして、サポートされている SAP カーネルの一覧を示します。 サポートされ、かつ最小限のカーネル リリースを必要とする NetWeaver または ABAP あるいは Java スタックの一覧が示されています。 最新の ABAP スタックは Azure でサポートされていますが、最小限のカーネル リリースを必要としません。これは、最新のスタックの開発が開始されてから Azure のための変更が実装されているからです。

以下を確認する必要があります。

- 実行している SAP アプリケーションが、記載されている最小リリースの対象となっているかどうか。 なっていない場合は、新しいターゲット リリースを定義し、SAP 製品の可用性マトリックスで、新しいターゲット リリースでサポートされるオペレーティング システムのビルドと DBMS の組み合わせを確認する必要があります。 これで、適切なオペレーティング システム リリースと DBMS リリースを選択できます。
- Azure への移行時にお使いの SAP カーネルを更新する必要があるかどうか。
- SAP サポート パッケージを更新する必要があるかどうか。 特に、最新の DBMS リリースへの移行が必要な場合に求められる可能性のある基本サポート パッケージ。


次のセクションでは、Windows および Linux 向けの SAP on Azure でサポートされている他の SAP 製品と DBMS リリースについて詳しく説明します。 

> [!NOTE]
> 各種 DBMS の最小リリースは慎重に選択されており、一般にさまざまな DBMS ベンダーが Azure でサポートしているあらゆる DBMS リリースが必ずしも反映されているとは限りません。 これらの最小リリースを定義するうえで、SAP ワークロードに関連した多くの考慮事項が検討されました。 以前の DBMS リリースをテストして適格と見なす手間はありません。 

> [!NOTE]
> 記載されている最小リリースは、以前のバージョンのオペレーティング システムとデータベース リリースを表しています。 最新のオペレーティング システム リリースおよびデータベース リリースを使用することを強くお勧めします。 多くの場合、最新のオペレーティング システムおよびデータベースのリリースでは、パブリック クラウドでの実行の使用事例が考慮され、パブリック クラウド (もっと具体的に言うと Azure) で実行するために最適化されるようコードが修正されました。

## <a name="oracle-dbms-support"></a>Oracle DBMS のサポート
Azure でサポートされているオペレーティング システム、Oracle DBMS リリース、Oracle 機能については、[SAP サポート ノート #2039619](https://launchpad.support.sap.com/#/notes/2039619) で明確に説明されています。 このノートの最も重要な点は、次のようにまとめることができます。

- NetWeaver で認定されている Azure VM でサポートされる最小 Oracle リリースはOracle 11g Release 2 パッチセット 3 (11.2.0.4) です。
- ゲスト オペレーティング システムとして、Windows と Oracle Linux のみが適格とされます。 OS の正確なリリースと関連する最小 DBMS リリースは、ノートに記載されています。
- Oracle Linux のサポートは、Oracle DBMS クライアントにも拡張されます。 つまり、ABAP または Java スタックのダイアログ インスタンスなど、すべての SAP コンポーネントは Oracle Linux 上でも実行する必要があります。 Oracle DBMS に接続しないこのような SAP システム内の SAP コンポーネントのみが別の Linux オペレーティング システムを実行できます。
- Oracle RAC はサポートされていません。 
- Oracle ASM は、一部のケースでサポートされています。 詳細についてはノートに記載されています。
- Unicode 非対応の SAP システムは、Windows のゲスト OS で実行されているアプリケーション サーバーのみでサポートされます。 DBMS のゲスト オペレーティング システムには、Oracle Linux または Windows を使用できます。 この制限の理由は、SAP 製品の可用性マトリックス (PAM) を確認すると明らかです。 Oracle Linux の場合、SAP は Unicode 非対応の SAP カーネルをリリースしたことがありません。

対象となる Azure インフラストラクチャでサポートされている DBMS リリースがわかったら、SAP 製品の可用性マトリックスで、実行しようとした SAP 製品リリースで必要な OS リリースと DBMS がサポートされているかどうかを確認する必要があります。 

## <a name="oracle-linux"></a>Oracle Linux
Oracle Linux に関して最もよく寄せられる質問は、SAP によって Oracle Linux の不可欠な要素である Red Hat カーネルもサポートされているかどうかです。 詳細については、[SAP サポート ノート #1565179](https://launchpad.support.sap.com/#/notes/1565179) を参照してください。

## <a name="other-database-than-sap-hana"></a>SAP HANA 以外のその他のデータベース
SAP ワークロードに対する HANA 以外のデータベースのサポートについては、[SAP サポート ノート #1928533](https://launchpad.support.sap.com/#/notes/1928533) を参照してください。


## <a name="sap-hana-support"></a>SAP HANA のサポート
Azure には、HANA データベースの実行に使用できる 2 つのサービスがあります。

- Azure Virtual Machines
- [HANA Large Instances](./hana-overview-architecture.md)

SAP HANA を実行するために、SAP には、NetWeaver または他の SAP アプリケーションと DBMS を実行する場合よりもインフラストラクチャが満たさなければならない厳密な条件が数多くあります。 その結果、SAP HANA DBMS の実行に適格とされる Azure VM の数は少なくなります。 SAP HANA 対応のサポートされる Azure インフラストラクチャの一覧については、いわゆる [SAP HANA ハードウェア ディレクトリ](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)を参照してください。 

> [!NOTE]
> "S" という文字で始まるユニットが [HANA Large Instances](./hana-overview-architecture.md) のユニットです。 

> [!NOTE]
> SAP には、SAP HANA メジャー リリースに依存する特定の認定がありません。 一般的な意見とは対照的に、[HANA 認定 IaaS プラットフォーム](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)の **認定シナリオ** の欄には、**HANA メジャーまたはマイナー リリースの認定に関する記述がありません**。 特定のユニットの認定オペレーティング システムが HANA 1.0 リリースでもサポートされている限り、一覧にあるユニットはすべて HANA 1.0 と HANA 2.0 で使用できると想定する必要があります。 

SAP HANA の使用については、一般的な NetWeaver の場合とは異なる最小 OS リリースが適用される場合があります。 サポートされているオペレーティング システムはユニットごとに異なる場合があるため、個別に確認する必要があります。 そのためには、各ユニットをクリックします。 詳細が表示されます。 表示された詳細の 1 つとして、この特定のユニットでサポートされている各種オペレーティング システムがあります。

> [!NOTE]
> Azure HANA L インスタンスのユニットでは、Azure VM と比較して、サポートされているオペレーティング システムがより限定されています。 その一方で、Azure VM では、より新しいオペレーティング システム リリースが最小リリースとして適用される場合があります。 これは特に、Linux カーネルへの変更を必要としたより規模の大きな VM ユニットのいくつかに当てはまります。

Azure インフラストラクチャでサポートされている OS がわかったら、対象としている Azure ユニットでサポートされる正確な SAP HANA リリースとパッチ レベルを [SAP サポート ノート #2235581](https://launchpad.support.sap.com/#/notes/2235581) で確認する必要があります。 

> [!IMPORTANT]
> サポートされている正確な SAP HANA リリースとパッチ レベルを確認する手順は非常に重要です。 多くの場合、特定の OS リリースのサポートは、SAP HANA の実行可能ファイルの具体的なパッチ レベルによって異なります。

対象となる Azure インフラストラクチャで実行できる具体的な HANA リリースがわかったら、SAP 製品の可用性マトリックスを確認して、除外した HANA リリースをサポートする SAP 製品リリースに制限があるかどうかを調べる必要があります。


## <a name="certified-azure-vms-and-hana-large-instance-units-and-business-transaction-throughput"></a>認定済みの Azure VM および HANA L インスタンスのユニットとビジネス トランザクションのスループット
Azure インフラストラクチャ ユニット用にサポートされているオペレーティング システム リリース、DBMS リリース、および依存するサポート SAP ソフトウェア リリースを評価することに加えて、これらのユニットをビジネス トランザクション スループット (これは、SAP の "SAP" という単位で表現されます) を基準に適格と見なす必要があります。 SAP のサイズ設定はすべて、SAPS の計算に依存します。 既存の SAP システムを評価したら、通常はインフラストラクチャ プロバイダーの助けを受けてユニットの SAPS を計算することができます。 DBMS レイヤーの場合もアプリケーション レイヤーの場合も同じです。 新しい機能が作成される他のケースでは、SAP でのサイズ設定の演習により、アプリケーション レイヤーと DBMS レイヤーに必要な SAPS の数値を明らかにすることができます。 Microsoft は、インフラストラクチャ プロバイダーとして、NetWeaver または HANA で認定を受けた各種ユニットの SAP のスループット特性を提供する義務があります。

Azure VM の場合、これらの SAPS スループットの数値は、[SAP サポート ノート #1928533](https://launchpad.support.sap.com/#/notes/1928533) に記載されています。 Azure HANA L インスタンスのユニットの場合、SAPS スループットの数値は [SAP サポート ノート #2316233](https://launchpad.support.sap.com/#/notes/2316233) に記載されています。

[SAP サポート ノート #1928533](https://launchpad.support.sap.com/#/notes/1928533) を参照する場合は、次の注釈が適用されます。

- **M シリーズ Azure VM および Mv2 シリーズ Azure VM では、他の Azure VM の種類とは異なる最小 OS リリースが適用されます**。 最新の OS リリースの要件は、さまざまなオペレーティング システム ベンダーが、特定の Azure VM の種類で自社のオペレーティング システムが動作できるようにするかそれらの VM の種類で SAP ワークロードのパフォーマンスとスループットを最適化するために自社のオペレーティング システム リリースで行う必要があった変更に基づいています。
- 異なる VM の種類を示す表が 2 つあります。 2 番目の表は、Azure Standard Storage のみをサポートする Azure VM の種類の SAPS スループットを示しています。 ノートの 2 番目の表に示されているユニットでの DBMS デプロイはサポートされません。


## <a name="other-sap-products-supported-on-azure"></a>Azure でサポートされているその他の SAP 製品
一般に、Azure のようなハイパースケール クラウドの状態では、ほとんどの SAP ソフトウェアは Azure で機能上の問題なく動作することが想定されます。 それでもなお、プライベート クラウドの視覚化とは対照的に、SAP は、さまざまなハイパースケール クラウド プロバイダー向けに各種 SAP 製品のサポートを明示的に表明します。 その結果、各種 SAP 製品に対する Azure のサポートを示すさまざまな SAP サポート ノートがあります。 

Business Objects BI プラットフォームについては、[SAP サポート ノート #2145537](https://launchpad.support.sap.com/#/notes/2145537) に、Azure でサポートされる SAP Business Objects 製品のリストが記載されています。 コンポーネントまたはソフトウェア リリースと OS リリースの組み合わせにおいて、記載またはサポートされていないと思われるものや記載されている最小リリースよりも新しいものについてご質問がある場合は、サポートの問い合わせ対象となるコンポーネントに対して SAP サポート リクエストをオープンする必要があります。

Business Objects Data Services については、[SAP サポート ノート #22288344](https://launchpad.support.sap.com/#/notes/2288344) で、Azure 上で動作する SAP Data Services の最小サポートについて説明されています。 

> [!NOTE]
> SAP サポート ノートに示されているように、SAP PAM を確認して、Azure でサポート対象となる適切なサポート パッケージ レベルを特定する必要があります。

Azure Kubernetes Services (AKS) の SAP Datahub または Vora のサポートについては、[SAP サポート ノート #2464722](https://launchpad.support.sap.com/#/notes/2464722) で詳しく説明されています

SAP BPC 10.1 SP08 のサポートについては、[SAP サポート ノート #2451795](https://launchpad.support.sap.com/#/notes/2451795) で説明されています

Azure での SAP Hybris Commerce Platform のサポートについては、[Hybris のドキュメント](https://help.sap.com/viewer/a74589c3a81a4a95bf51d87258c0ab15/1811/en-US/8c71300f866910149b40c88dfc0de431.html)で詳しく説明されています。 SAP Hybris Commerce Platform でサポートされている DBMS の場合、次のような一覧が表示されます。

- Windows オペレーティング システム プラットフォーム上の SQL Server および Oracle。 SAP NetWeaver の場合と同じ最小リリースが適用されます。 詳細については、[SAP サポート ノート #1928533](https://launchpad.support.sap.com/#/notes/1928533) を参照してください
- Red Hat および SUSE Linux 上の SAP HANA。 [このドキュメント](#sap-hana-support)で前述したように、SAP HANA 認定の VM の種類が必要です。 SAP (Hybris) Commerce Platform は OLTP ワークロードと見なされます
- SAP (Hybris) Commerce Platform バージョン 1811 での SQL Azure DB




## <a name="next-steps"></a>次の手順
次の手順については、「[SAP NetWeaver のための Azure Virtual Machines の計画と実装](./planning-guide.md)」を参照してください
