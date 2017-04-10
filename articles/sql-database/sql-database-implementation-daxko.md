---
title: "Azure SQL Database Azure 導入事例 - Daxko/CSI | Microsoft Docs"
description: "SQL Database を活用することで、Daxko/CSI がどのように開発サイクルを迅速化し、顧客サービスとパフォーマンスの強化を実現したのかご紹介します。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 00c8a713-f20c-4d6b-b8b7-0c1b9ba5f05b
ms.service: sql-database
ms.custom: customer implementations
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: b759e9936a45f989088de5e89a59371c5ea64e1c
ms.openlocfilehash: 079a05d16ccebb705f410e25f0ed5762db20ae22
ms.lasthandoff: 02/11/2017


---
# <a name="daxkocsi-used-azure-to-accelerate-its-development-cycle-and-to-enhance-its-customer-services-and-performance"></a>Daxko/CSI 社、Azure 導入で開発サイクルを迅速化し、顧客サービスとパフォーマンスを強化
![Daxko/CSI Logo](./media/sql-database-implementation-daxko/csidaxkologo25.png)

Daxko/CSI Software 社 (以下 CSI Software) は問題に直面していました。自社の包括的な企業向けソフトウェア ソリューションの成功により、フィットネス レクリエーション センターの顧客基盤が急速に拡大する一方で、顧客数の増加により、IT インフラストラクチャのニーズに応えることが難しくなっていたのです。 CSI Software は、拡大するデータベースの管理に伴う経費の増加に頭を悩ませるようになりました。 また、この問題により、ソフトウェアのモビリティ機能などの新たな取り組みに向けた開発リソースがひっ迫していました。

CSI Software 製品開発担当ディレクター David Molina 氏は、Azure を導入したことで、データベース管理の簡素化とスケーラビリティの向上、そしてソフトウェア開発に向けたリソースの確保に必要な、サービスとしてのプラットフォーム (PaaS) モデルを実現できたと語っています。 「Azure SQL Database は、当社にとって魅力的な選択肢でした。 SQL Server、フェールオーバー クラスター、およびその他のインフラストラクチャの維持管理を心配せずにすむことは、私たちがまさに望んでいたことでした。」

CSI Software は Azure への移行後、600 社の顧客のデータベース管理をわずか 2 人のスタッフで行っています。 Azure SQL Database のエラスティック プールを使用して、規模と需要に応じて顧客のデータベースを移動しています。

Molina 氏は次のように述べています。「お客様はすぐに変化を実感しました。 エラスティック プール導入前は、バースト期間中にタイムアウトなどの問題が生じていましたが、 現在では必要に応じてバーストし、問題なくソフトウェアを使用できるようになりました。」

Azure エラスティック プールにより、顧客へのパフォーマンスが向上したことに加えて、運営管理の手間を省き、新しいサービスと機能の開発に取り組むリソースを確保できるようになりました。 CSI Software は企業向けソフトウェア SpectrumNG を改善し、スポーツ クラブの会員をつなぎとめてスタッフの効率性向上に貢献するとともに、スタッフと会員が双方向タスクとリアルタイム通知に使用するモバイル アクセスを提供しています。

また、Azure の自動化オプションにより、開発および品質保証 (QA) サイクルの迅速化と向上が実現しました。 Azure 導入により、ビルド マネージャーは、ボタンのクリック操作だけでコンポーネントをパッケージにまとめることができます。 Molina 氏は、「Azure ではリリース サイクルの一環として QA をテスト環境でデプロイできるため、実稼働スタックの再現が可能になりました。 ビルドを開発環境に速やかにデプロイして、変更を精査することができます。 以前はこのようなテスト手段がありませんでしたので、これは当社にとって非常に大きなメリットです」と語っています。

## <a name="offloading-to-the-cloud"></a>Azure に任せて負荷を軽減
クラウド移行前の CSI Software は、自社のマルチテナント型インフラストラクチャを、地元テキサス州ヒューストンのデータセンターに構築していました。 事業拡大に伴って、顧客サポートに必要なハードウェアやソフトウェアの購入、提供、維持管理の負担が増してきました。 IT 担当者がこれらの業務に追われたことが原因で、新しいリソースの提供と顧客への新サービスの発表が滞る事態が生じました。

CSI Software は、コストと負荷を削減してコード作成に注力できる選択肢としてクラウドを検討しましたが、 大手クラウド プロバイダーの多くが、管理に多数の IT 人員を必要とする、サービスとしてのインフラストラクチャ (IaaS) ソリューションしか提供していないことが明らかになりました。 CSI Software は検討を重ねた結果、Azure の PaaS ソリューションが自社のニーズに最適だと判断しました。 Molina 氏は次のように述べています。「Azure のおかげでハードウェアやシステム ソフトウェアの管理が不要になり、IT コストを削減しつつ、ソフトウェアの提供に注力できるようになりました。」

## <a name="making-the-transition-to-azure"></a>Azure への移行
PaaS ソリューションに Azure を選択した CSI Software は、自社のバックエンド インフラストラクチャおよびデータベースのクラウドへの移行を開始しました。 SpectrumNG を利用する顧客は、Azure への移行に先立ち、Windows Communication Foundation (WCF) とバックエンドで通信するクライアント アプリケーションのインストールが必要でした。 「顧客によっては、自社のデータセンターですべてをホストするケースもありましたが、私たちは製品をマルチテナント用に構築しました。 SQL Server をデータ ストアとして使用し、ヒューストンのデータセンターですべてをホストしていました。

当社が提供する製品には、顧客の Web サイトに適合するためにホワイトレーベル化するよう設計された、ASP.NET で作成した会員向け Web ポータル、およびオンライン ページとサードパーティの統合をサポートするための、SOAP API を使用して構築したサービスなどがありました」と Molina 氏は述べています。

CSI Software のアーキテクチャにとって、クラウドへの移行に時間はかかりませんでした。 Molina 氏は次のように述べています。「作業の大半は、構成ファイルの情報を読み取る方法の変更、一元化された接続文字列の変更、パッケージの自動化、アップロード、およびリリースのデプロイに関することでした。」

ビルド自動化の開発に向けて、CSI Software のエンジニアにより Azure PowerShell と REST API が使用され、毎夜リリースに向けたパッケージの作成とステージング環境へのアップロードが行われました。
CSI Software の IT チームにとって、クラウドベースの Azure 環境への移行は、全般的に迅速かつ円滑に行われました。 「プロジェクト開始から&3;、4 週間以内で、クラウドでベータ環境を構築できました。 これは私たちにとって喜ばしい驚きでした」と Molina 氏は述べています。

環境の構成およびテストの完了後に、顧客の移行が開始されました。 CSI Software のホスティング サービスを既に利用していた顧客にとって、Azure への移行はほぼシームレスでした。 一方、オンプレミス環境から移行する顧客の場合は、クラウドへの移行に若干時間を要しましたが、顧客と CSI Software 双方にとって、概ね負担はありませんでした。

CSI Software は、新規顧客に対しては次のプロセスを使用して Azure への移行を行っています。

1. Azure PowerShell スクリプトを使用して、顧客の新しいデータベースを作成します。移行のための十分なスループットが確保されるよう、最初はすべての顧客に Premium レベルが提供されます。
2. 可能な場合は、Azure SQL Database インスタンスに既存データを移動する際に、Azure SQL 移行ウィザードを使用します。
3. また、Microsoft SQL Server Integration Services (SSIS) を使用してデータ不整合の問題に対応し、必要に応じてデータのクリーンアップを行います。

現在、CSI Software の顧客の約 99% が、米国中北部、米国中南部、米国東部、および米国西部の 4 つのリージョンのデータセンターにわたって、Azure でホストされています。 各顧客の地理的リージョンにデータセンターがあるため、遅延時間が最小限に抑えられます。

## <a name="azure-elastic-pools-free-up-it-resources"></a>Azure エラスティック プールで IT リソースを確保
Azure のいくつかの機能により、インフラストラクチャの維持管理が不要になり、ソフトウェアの機能や開発に注力できるようになりました。 CSI Software に最大のメリットをもたらしているのは、エラスティック プールです。

現在 CSI Software は、顧客に約 550 のデータベースを提供しています。 エラスティック プールが導入されるまでは、これほど多数のデータベースを階層構造内で管理することは困難でした。 顧客のバースト ニーズに合わせてパフォーマンス階層を割り当てねばならず、IT リソースに多額の費用がかかっていました。 一方、エラスティック プールが導入されてからは、テナントを Premium または Standard プールに適切に割り当てて、その後、規模と需要に応じて顧客を移動させることができるようになりました。 CSI Software の顧客はエラスティック プールの効果をすぐ実感しました。以前はバースト使用期間にタイムアウトなどの問題が生じていましたが、現在では、必要に応じてアクティビティ バーストを経験しつつ、SpectrumNG の使用を問題なく続けることができるようになったのです。

## <a name="azure-active-geo-replication-accelerates-reporting"></a>Azure アクティブ geo レプリケーションがレポートを迅速化
CSI Software の顧客の中には、Azure アクティブ geo レプリケーションを活用するケースも見られます。 アクティブ geo レプリケーションにより、同一または異なるデータ センター リージョンに最大&4; つの読み取り可能なセカンダリ データベースを構成できます。 CSI Software ではアクティブ geo レプリケーションを&2; 通りに利用しています。データセンターでの障害発生時やプライマリ データベースに接続できない場合にセカンダリ データベースを使用するほか、読み取り可能なセカンダリ データベースを、ジョブの報告などの読み取り専用のワークロードの負荷の軽減に利用しています。 CSI Software の顧客はこのメリットを生かして、レポートのワークフローを迅速化させています。

## <a name="csi-software-application-logic-and-architecture"></a>CSI Software のアプリケーション ロジックとアーキテクチャ
SpectrumNG では Web ロールを使用します。 マルチテナント型アプリケーションであるため、顧客からの最初の接続要求の処理には WCF サービスが使用されます。 Molina 氏は次のように述べています。 「要求により各顧客が識別されるため、顧客のデータベースに接続文字列を作成して、必要な対応をすべて行うことができます。」

このサービスの Web 層には、日時に基づく Azure 自動スケールが活用されています。 各リージョンのデータセンターのタイム ゾーンに従って、使用可能なリソースが自動的に増加され、業務時間中の使用率上昇に対応します。 一方、顧客の需要が低くなる週末には、リソースをスケールダウンする設定になっています。

![Daxko/CSI Architecture](./media/sql-database-implementation-daxko/figure1.png)

図 1. クラウド サービスの worker ロールにより、Azure SQL Database から構造化データが、また Table Storage からは半構造化データが引き出されます。 SpectrumNG ユーザーはクラウド サービスの Web ロールを通じて、これらのデータとやり取りを行います。

## <a name="using-web-apps-and-a-web-plan-tier-for-mobile-apps"></a>モバイル アプリ用 Web アプリと Web プラン レベルの利用
CSI Software は Azure SQL Database を使用することで、Azure の Web アプリでホストされるカスタム API をベースとした包括的なモバイル プラットフォームを含む、新しい取り組みに向けたリソースを確保することができました。 このプラットフォームでは、スポーツ クラブの会員とスタッフがモバイル端末を使用して、スケジュールの確認、クラスの予約、およびメッセージの受信を行うことができます。

プラットフォームはサービス指向アーキテクチャ (SOA) を使用して、残りのコンポーネントはすべて元の Web プランに残しつつ、販売時点管理システム (POS) や販売システムなどの&1; つのコンポーネントを、別の Web プランに即時移動させます。 この機能は CSI Software に大きな柔軟性をもたらすとともに、コスト抑制に役立っています。

## <a name="azure-lets-csi-software-developers-focus-on-apps-and-services"></a>Azure により、CSI Software の開発者はアプリとサービスに集中
Azure SQL Database は、俊敏で信頼性の高いサービスを受ける SpectrumNG ユーザーに恩恵を与えるとともに、CSI Software の IT 担当者と開発者にも大きなメリットをもたらしています。 クラウド上で Azure に維持管理を任せることにより、負荷を軽減してリソースやインフラストラクチャの費用を削減するとともに、開発サイクルを大幅に迅速化しました。また、データベースを細かく管理してテナントごとにパフォーマンスを最適化する手間は一切なくなりました。

## <a name="more-information"></a>詳細情報
* Azure エラスティック プールの詳細については、[エラスティック プール](sql-database-elastic-pool.md)に関する記事をご覧ください。
* データベース ツールおよびエラスティック スケーリングの詳細については、 [エラスティック データベース ツールおよびエラスティック スケーリング](sql-database-elastic-scale-get-started.md)に関する記事をご覧ください。
* SQL Server データベースの移行について、詳しくは、[SQL Server データベースの Azure への移行](sql-database-cloud-migrate.md)に関する記事をご覧ください。
* アクティブ geo レプリケーションの詳細については、 [アクティブ geo レプリケーション](sql-database-geo-replication-overview.md)に関する記事をご覧ください。
* Web ロールと worker ロールの詳細については、 [worker ロール](../fundamentals-introduction-to-azure.md#compute)に関する記事をご覧ください。    
* Azure Service Bus の詳細については、「 [Azure Service Bus](https://azure.microsoft.com/services/service-bus/)」をご覧ください。
* 自動スケールの詳細については、 [クラウド サービスのスケーリング](../cloud-services/cloud-services-how-to-scale.md)に関する記事をご覧ください。


