---
title: "Azure SQL Database Azure 導入事例 - GEP| Microsoft Docs"
description: "SQL Database を活用することで、GEP がどのように世界中の顧客とつながり、高い効率性を実現したのかご紹介します。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: ae8bcb10-c251-4bac-b666-10a253918583
ms.service: sql-database
ms.custom: customer implementations
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 10b40214ad4c7d7bb7999a5abce1c22100b617d8
ms.openlocfilehash: 5730556f4d5af04481c771c8a70f003d6cd2917c
ms.lasthandoff: 01/13/2017


---
# <a name="azure-gives-gep-global-reach-and-greater-efficiency"></a>GEP 社のグローバルな事業展開と効率性向上を Azure が支援
![GEP ロゴ](./media/sql-database-implementation-gep/geplogo.png)

GEP 社 (以下 GEP) は、世界中の調達責任者が、事業のオペレーション、戦略、および財務パフォーマンスへの効果を最大化するためのソフトウェアとサービスを提供しています。 コンサルティングおよびマネージド サービスに加えて、クラウド ベースの包括的な調達ソフトウェア プラットフォーム SMART by GEP® も提供しています。 しかし GEP は、SMART by GET などのソリューションを自社のオンプレミス型データセンターでサポートすることを試みて壁に突き当たりました。ばく大な投資が必要なこと、また世界各国の規制要件への対応を考慮すると、必要な投資はさらに困難なものに思えました。 現在 GEP は、クラウドへの移行によって IT リソースを確保し、IT 運用の省力化を実現するとともに、世界中の顧客に新しい価値をもたらすサービスの開発に注力できるようになりました。

## <a name="expanding-services-and-growth-by-using-azure"></a>Azure でサービスを拡大し成長を加速
SMART by GEP は、プラットフォームの機能と使いやすさが顧客に支持されています。顧客はいつでも、どこでも、あらゆるデバイス (ラップトップ、タブレット、スマートフォン) を使用して、プロセスを管理することができます。 GEP は Microsoft Azure に移行したことで、急速な成長に対応し、新しい市場への事業拡大の可能性に取り組むことができました。 GEP テクノロジ担当バイス プレジデント Dhananjay Nagalkar 氏は次のように述べています。「Microsoft Azure は、GEP の成功において鍵となる役割を担っています。 Azure のおかげでサービスを迅速に拡大できるとともに、Azure の各リージョンのデータセンターにより、世界中のお客様の規制要件を満たすことが可能になりました。」

## <a name="the-limitations-of-a-do-it-yourself-datacenter"></a>自社によるデータセンター運用管理の限界
2013 年、GEP の経営陣は、顧客基盤の拡大とともに、スケーラビリティとパフォーマンスを確保する方法が必要だと認識しました。 Nagalkar 氏は次のように述べています。「当社の既存のデータセンターで需要に対応するには、インフラストラクチャと IT リソースを大幅に拡張する必要がありました。 それは莫大な投資を要し、長期間に及ぶ恐れがありました。」 オンプレミスの物理および仮想マシンに必要な、広範な構成、管理、スケーリング、バックアップ、パッチ修正には多額の費用がかかります。 一方、クラウド ソリューションなら、簡素化と利便性を提供し、拡大を続ける大規模な IT の維持管理に手間取ることなく、開発に注力することができます。 Nagalkar 氏は、クラウドに移行することで、インフラストラクチャの購入、構成、および管理コストを削減できると考えました。

また GEP では、一部の国で市場参入の障壁となっている、規制に対応するための方法が必要でした。 規制遵守の観点から、ヨーロッパの潜在顧客の多くは地理的地域内にデータを保存する必要がありました。 しかし、そのために複数のデータセンターを設立することは GEP にとって現実的ではありません。 Nagalkar 氏は次のように語っています。「広範なインフラストラクチャへの投資、および IT 関連の人件費が、当社の利益に大きな影響を与える恐れがありました。 その結果、地域内にデータを保存する必要がある潜在顧客への対応はあきらめざるを得ませんでした。」

クラウド ソリューションがこのジレンマの解決策になると Nagalkar 氏は考えました。 世界規模で展開するクラウド プロバイダーに移行すれば、顧客の物理的な場所に近い地域でデータを保存して、顧客の規制要件とパフォーマンス要件に対応できると考えたのです。

## <a name="finding-smooth-skies-in-the-cloud"></a>クラウドに解決の道を見出す
Nagalkar 氏と彼のチームは、いくつかのクラウド サービスを検討しましたが、その多くがサービスとしてのインフラストラクチャ (IaaS) をベースとしたソリューションであり、サービスの構成と管理には、IT リソースへの多額の投資が必要でした。 Azure の提供するサービスとしてのプラットフォーム (PaaS) ソリューションが、ベストな選択であることは明らかでした。

「Azure では、データベース管理、仮想マシンの構成、パッチ修正、およびその他のインフラストラクチャ管理の手間が不要です。 代わりに、当社が最も得意とすること、つまり調達分野における専門性を生かし、お客様に成果をもたらすソフトウェアを作成することにリソースを投入できます」と Nagalkar 氏は述べています。 

実際 GEP は、Azure への移行で、IT 人員を削減しつつ、顧客に優れた機能を提供できるようになりました。

世界中に展開する Azure データセンターを利用することで、ヨーロッパおよびアジアへの事業拡大が容易になりました。 Azure の世界中のデータセンターにより、GEP はサービスを迅速に拡大し、地域内でのデータの保存に対する規制要件を満たして顧客のニーズに応えるとともに、遅延時間の短縮も実現しています。

## <a name="smart-by-gep-architecture"></a>SMART by GEP のアーキテクチャ
SMART by GEP は、Azure 上で一から構築されました。 この取り組みの大きなきっかけとなったのが、オンプレミスと比べて、Azure SQL Database では優れたスケーラビリティ、ダウンタイムの抑制、および維持管理コストの削減を実現できる点でした。 それだけにとどまらず、GEP はクラウド移行後に、顧客のニーズに対応するための迅速なプロトタイピングや無駄のないエンジニアリングなどの、新たな開発機会をクラウドで見出しました。 Azure での開発により、開発者がオンプレミスで抱えるソフトウェアのライセンスの問題を解消できます。 SMART by GEP の中枢となるのは Azure SQL Database ですが、GEP はその他さまざまな Azure サービスを使用して、SMART by GEP の改善を容易に、かつ迅速に進めています。

![SMART by GEP のアーキテクチャ](./media/sql-database-implementation-gep/figure1.png) 図 1. SMART by GEP のアーキテクチャ

## <a name="structured-data"></a>構造化データ
SMART by GEP アプリケーションの中枢となるのが、企業向け調達管理ソリューションの原動力となる Azure SQL Database インスタンスです。 GEP は SMART by GEP の設計において、アーキテクチャにとって最高レベルのデータ保護、および規制要件への適合を実現する上で、Azure SQL Database が最高の選択肢だと考えました。 GEP が利用する、Azure SQL Database の複数層のデータ保護は次の通りです。

* Transparent Data Encryption による保存データの暗号化。
* Azure SQL Database と Azure Active Directory の統合による認証のセキュリティ保護。
* 行レベルのセキュリティ機能により、データの適切なサブネットにアクセスを制限。
* ポリシーによってリアルタイムにデータをマスク。
* Azure SQL Database の監査機能が、データベースで発生するイベントを追跡。

> 「主要なコードの変更なしに、パフォーマンスへの影響を最小限に抑えつつ、これらすべてのオプションを利用することができます」と Nagalkar 氏は語っています。
> 
> 

Azure SQL Database に搭載されたフォールト トレランス機能により、オンプレミスでコストを気にしつつ設計するよりも優れた障害復旧機能が自動的に提供されます。 GEP は、Azure SQL Database のアクティブ geo レプリケーション機能、および異なる地理的リージョンにあり、オンラインのままにすることができる、読み取り可能な複数のアクティブなセカンダリ レプリカ (Always On 可用性グループ) を組み合わせて、高可用性のペアを形成しています。 SMART by GEP のデータをリージョンにわたってレプリケートすることで、特定のリージョン全体で障害が発生した場合にも、目標回復ポイント (RPO) および目標復旧時間 (RTO) を最小限に抑えつつ、顧客のデータを容易に復旧できます。

SMART by GEP を利用する各顧客には、オンライン トランザクション処理 (OLTP) 、および経費とレポート分析などの分析用に&1; つずつ、Azure SQL Database インスタンスが提供されます。 Azure SQL Database のエラスティック プールにより、GEP は世界中の数千のデータベースを容易に管理し、予測困難なデータベース リソースの需要に対応できます。 エラスティック プールが提供する手段により、プロビジョニングの過不足なく、顧客のデータベースを必要に応じて拡張/縮小するとともに、コストの制御も実現します。 また、PaaS サービスの Azure では、すべて自動アップグレードで Azure SQL Database の新機能を入手できます。

## <a name="unstructured-and-semi-structured-data"></a>非構造化データと半構造化データ
一方、SMART by GEP の顧客の中には、厳密に構造化されたストレージを必要としないケースもあります。 このような種類のデータには、Azure Blob Storage、Azure Table Storage、および Azure Redis Cache が使用されます。 Azure Blob Storage には、SMART by GEP ユーザーがアプリケーションにアップロードした添付ファイルがすべて格納されます。 またここには、SMART by GEP により静的コンテンツ (カスケード スタイル シート (CSS) や JavaScript ファイルなど) が格納されています。

顧客向けデータ以外のデータ (SMART by GEP のログ データなど) は、データのスキーマ設定なしでコスト効率に優れた無制限のストレージを提供し、取得時間の短縮を実現する Azure Table Storage に格納されます。 Azure Redis Cache はマスター キャッシュに使用されています。

## <a name="authentication-and-routing"></a>認証とルーティング
Azure Access Control Service (ACS) は、SMART by GEP ユーザーに、ソフトウェアのサインインのさまざまなオプションを提供します。 Azure ACS は、SAML (Security Assertion Markup Language) を使用して認証データをやり取りするいずれの ID プロバイダー (Active Directory Domain Services、Ping Identity、OneLogin、SiteMinder など) ともフェデレーションを行うことができます。 これは、ユーザーの資格情報の保存や顧客のパスワード ポリシーの管理を心配せずに、シングル サインオン (SSO) を実装する上で役立ちます。

顧客は&1; 度サインインすれば SMART by GEP 内の正しいビジネス リソースにアクセスできます。 顧客のモバイル端末やブラウザー セッションからの要求のリダイレクトおよび負荷分散には、Azure Traffic Manager が使用されています。

## <a name="other-azure-services"></a>他の Azure サービス
GEP では、SMART by GEP が顧客のニーズに速やかに対応できるよう、Azure のその他のサービスも多数利用しています。 アプリケーションのプレゼンテーションおよびセキュリティで保護されたビジネス ロジックのサービスのホストには、Azure クラウド サービス (Web ロールと worker ロール) が使用されています。 クラウド サービスでは、IT 部門の関与を必要とせずに、開発者がコードとしてのインフラストラクチャ (IAC) の管理を行い、SMART by GEP の新しいアプリケーションをオンプレミス型データセンターに比べ短期間でデプロイすることが可能です。 GEP の開発者はクラウド サービスのステージング環境を使用して、現在の運用環境には影響を与えずに、新しいリリースをテストできます。 テスト完了後は Azure の VIP スワップ機能を使用して、1 分以内にステージング コードを運用スロットに移動することで、デプロイのダウンタイムを削減できます。

また、GEP ではアプリケーションの遅延時間の抑制に、Azure Content Delivery Network (CDN) を使用して、Azure Blob Storage に格納されている静的コンテンツ (CSS および JavaScript ファイルなど) をユーザーに近いエッジ サーバーに配置しています。 発行/購読パターンから部分的なコマンド クエリ責務分離 (CQRS)、疎結合および非同期通信のレイヤー化アーキテクチャに至るまで、アプリケーションのアーキテクチャ パターンのサポートには Azure Service Bus が使用されています。 さらに GEP では、顧客サポート サービスの向上に Azure Media Services を使用しています。 Azure Media Services では、ユーザー サポート動画を簡単に投稿することができます。 サポート動画でユーザーからよく寄せられる質問に答えることで、SMART by GEP ユーザーの満足度向上に貢献すると共に、GEP の顧客サポート スタッフの負荷軽減にも役立っています。

SMART by GEP が日々生成する数千ものトランザクション メールの送信には、Azure との統合に SendGrid .NET API が使用されています。 Azure で使用するアドオン SendGrid は Azure Marketplace で直接入手できるため、簡単に使用できます。 GEP の開発者は、SendGrid NuGet パッケージを Microsoft Visual Studio で使用して SMART by GEP の構成を行うことができました。IT 担当者はソフトウェアの SendGrid メール トラフィックを Azure から直接監視できます。

最後に SMART by GEP では、Azure の IaaS サービスである Azure 仮想マシンを使用して不要になったアプリケーションやサービスをホストし、エンジニアリングの際に、これらをサービスとしてのソフトウェア (SaaS) または PaaS ソリューションに置き換えています。 たとえば GEP は、顧客のオンプレミス型エンタープライズ リソース プラニング (ERP) システム (SAP、Oracle、PeopleSoft、JD Edwards、Microsoft Dynamics GP、Lawson など) 、および顧客の SaaS ソリューションの企業間 (B2B) の統合に向けて、仮想マシンで API 統合サービスをホストし、請求書などの調達ドキュメントのやり取りの効率化をサポートしています。

> 「Microsoft Azure で SMART by GEP を構築することで、当社だけでなくお客様の調達オペレーションにおいても、オンプレミスの IT が不要になりました。」 
> 
> — テクノロジ ソリューション担当バイス プレジデント Dhananjay Nagalkar
> 
> 

## <a name="expand-customer-satisfaction-without-expanding-it"></a>顧客満足度を向上し、IT の拡大が不要
オンプレミス型データセンターから Azure に移行し、SMART by GEP を Azure プラットフォーム上で一から構築することで、GEP はインフラストラクチャや IT 人員を拡大する必要がなく、スケーラビリティと柔軟性を向上させることができました。 実際、GEP では、IT リソースの追加を&5; 年以上行っていません。 Azure の利便性に優れた PaaS モデルにより、GEP はベンダー サポートおよび運用管理のコストを削減することができました。 その結果、ソフトウェア開発にリソースを投入でき、さらに、クラウドでの開発により、GEP の開発者は IT 部門との調整に時間を費やしたり、オンプレミスでのライセンス要件に悩まされることなく、新しいアイデアを速やかにテストすることが可能になりました。 Azure SQL Database は、GEP が顧客に常に最高のサービスとパフォーマンスを提供する上で大きく貢献しています。

## <a name="more-information"></a>詳細情報
* GEP ホーム ページ: [GEP](http://www.gep.com)
* Smart by GEP: [Smart by GEP](http://www.smartbygep.com)

## <a name="gep-contributors"></a>GEP 共同作成者
* GEP アーキテクト アソシエイト ディレクター Huzaifa Matawala
* GEP エンジニアリング マネージャー Sathyan Narasingh
* GEP データベース アーキテクト Deepa Velukutty


