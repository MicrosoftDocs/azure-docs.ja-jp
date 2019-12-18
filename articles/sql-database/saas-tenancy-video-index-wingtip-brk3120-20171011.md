---
title: SaaS SQL アプリのビデオ
description: この記事では、2017 年 10 月 11 日に開催された Ignite カンファレンスで公開された、SaaS DB テナント アプリの設計に関する 81 分間のビデオのさまざまな時点のインデックスを示します。 興味のある部分にスキップできます。 少なくとも 3 つのパターンについて説明されています。 開発と管理を簡略化する Azure の機能が説明されています。
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 0194d99a10eb34ecd8ad10fa0a1509baf8436381
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818471"
---
# <a name="video-indexed-and-annotated-for-multi-tenant-saas-app-using-azure-sql-database"></a>Azure SQL Database を使用するマルチテナント SaaS アプリに関するインデックスと注釈付きのビデオ

この記事は、SaaS テナント モデルまたはパターンに関する 81 分間のビデオの時間位置への注釈付きインデックスです。 この記事から、ビデオの後方や前方の興味のある位置にスキップできます。 ビデオでは、Azure SQL Database でのマルチテナント データベース アプリケーションに関する主要な設計オプションが説明されています。 ビデオには、デモ、管理コードのチュートリアル、および場合によってはドキュメントより詳細な経験に基づく情報が含まれます。

ビデオでは、以下のドキュメントの情報が詳しく説明されています。 
- *概念:* [マルチテナント SaaS データベース テナント パターン][saas-concept-design-patterns-563e]
- *チュートリアル:* [Wingtip Tickets SaaS アプリケーション][saas-how-welcome-wingtip-app-679t]

ビデオと記事では、クラウドの Azure SQL Database を利用してマルチテナント アプリケーションを作成するときのさまざまなフェーズについて説明されています。 Azure SQL Database の特殊な機能により、管理が容易で信頼できるパフォーマンスのマルチテナント アプリを、簡単に開発して実装できます。

ドキュメントは定期的に更新されています。 ビデオは編集または更新されないので、最終的にその詳細の多くが古くなる可能性があります。



## <a name="sequence-of-38-time-indexed-screenshots"></a>38 枚の時間インデックス付きスクリーンショットのシーケンス

このセクションでは、81 分間のビデオ全体で 38 のディスカッションの時間位置にインデックスを付けてあります。 各時間インデックスには、ビデオのスクリーンショットと、場合によっては追加情報で、注釈が付けられています。

各時間インデックスは、*h:mm:ss* の形式になっています。 たとえば、「**セッションの目標**」というラベルの付いた 2 番目のインデックス付き時間位置は、**0:03:11** あたりの時点から始まります。


### <a name="compact-links-to-video-indexed-time-locations"></a>ビデオのインデックス付き時間位置への簡易リンク

以下のタイトルは、この記事の後半にある対応する注釈付きセクションへのリンクです。

- [1. **(開始)** ようこそスライド、0:00:03](#anchor-image-wtip-min00001)
- [2.セッションの目標、0:03:11](#anchor-image-wtip-min00311)
- [3.予定、0:04:17](#anchor-image-wtip-min00417)
- [4.マルチテナント Web アプリ、0:05:05](#anchor-image-wtip-min00505)
- [5.動いているアプリ Web フォーム、0:05:55](#anchor-image-wtip-min00555)
- [6.テナントあたりのコスト (拡張、分離、復旧)、0:09:31](#anchor-image-wtip-min00931)
- [7.マルチテナントのデータベース モデル: 長所と短所、0:11:59](#anchor-image-wtip-min01159)
- [8.MT/ST のハイブリッド モデル ブレンドの利点、0:13:01](#anchor-image-wtip-min01301)
- [9.シングルテナントとマルチテナント: 長所と短所、0:16:44](#anchor-image-wtip-min01644)
- [10.予測できないワークロードに対してコスト効果の高いプール: 0:19:36](#anchor-image-wtip-min01936)
- [11.テナントごとのデータベースとハイブリッド ST/MT のデモ、0:20:08](#anchor-image-wtip-min02008)
- [12.道場のライブ アプリ フォーム、0:20:29](#anchor-image-wtip-min02029)
- [13.MYOB と DBA の廃止、0:28:54](#anchor-image-wtip-min02854)
- [14.MYOB のエラスティック プール使用例、0:29:40](#anchor-image-wtip-min02940)
- [15.MYOB および他の ISV からの教訓、0:31:36](#anchor-image-wtip-min03136)
- [16.E2E SaaS シナリオを構成するパターン、0:43:15](#anchor-image-wtip-min04315)
- [17.正規のハイブリッド マルチテナント SaaS アプリ、0:47:33](#anchor-image-wtip-min04733)
- [18.Wingtip SaaS サンプル アプリ、0:48:10](#anchor-image-wtip-min04810)
- [19.チュートリアルで調査されているシナリオとパターン、0:49:10](#anchor-image-wtip-min04910)
- [20.チュートリアルと GitHub リポジトリのデモ、0:50:18](#anchor-image-wtip-min05018)
- [21.GitHub リポジトリ Microsoft/WingtipSaaS、0:50:38](#anchor-image-wtip-min05038)
- [22.パターンの調査、0:56:20](#anchor-image-wtip-min05620)
- [23.テナントのプロビジョニングとオンボード、0:57:44](#anchor-image-wtip-min05744)
- [24.テナントとアプリケーションの接続のプロビジョニング、0:58:58](#anchor-image-wtip-min05858)
- [25.単一のテナントをプロビジョニングする管理スクリプトのデモ、0:59:43](#anchor-image-wtip-min05943)
- [26.プロビジョニングとカタログのための PowerShell、1:00:02](#anchor-image-wtip-min10002)
- [27.T-SQL SELECT * FROM TenantsExtended、1:03:30](#anchor-image-wtip-min10330)
- [28.予測不可能なテナント ワークロードの管理、1:04:36](#anchor-image-wtip-min10436)
- [29.エラスティック プールの監視、1:06:39](#anchor-image-wtip-min10639)
- [30.負荷の生成とパフォーマンスの監視、1:09:42](#anchor-image-wtip-min10942)
- [31.大規模なスキーマ管理、1:10:33](#anchor-image-wtip-min11033)
- [32.テナント データベース間の分散クエリ、1:12:21](#anchor-image-wtip-min11221)
- [33.チケット生成のデモ、1:12:32](#anchor-image-wtip-min11232)
- [34.SSMS アドホック分析、1:12:46](#anchor-image-wtip-min11246)
- [35.テナント データの SQL DW への抽出、1:16:32](#anchor-image-wtip-min11632)
- [36.日次売上の分布のグラフ、1:16:48](#anchor-image-wtip-min11648)
- [37.まとめと行動への呼び掛け、1:19:52](#anchor-image-wtip-min11952)
- [38.詳細な情報のリソース、1:20:42](#anchor-image-wtip-min12042)


&nbsp;

### <a name="annotated-index-time-locations-in-the-video"></a>ビデオの注釈付きインデックス時間位置

スクリーンショットの画像をクリックすると、ビデオ内の正確な時間位置に移動します。


&nbsp; <a name="anchor-image-wtip-min00001"/>
#### <a name="1-start-welcome-slide-00001"></a>1. *(開始)* ようこそスライド、0:00:01

*MYOB からの学習:Azure SQL Database での SaaS アプリケーション用の設計パターン - BRK3120*

[![ようこそスライド][image-wtip-min00003-brk3120-whole-welcome]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1)

- タイトル:MYOB からの学習:Azure SQL Database での SaaS アプリケーション用の設計パターン
- Bill.Gibson@microsoft.com
- 主席プログラム マネージャー、Azure SQL Database
- Microsoft Ignite セッション BRK3120、米国フロリダ州オーランド、2017 年 10 月 11 日


&nbsp; <a name="anchor-image-wtip-min00311"/>
#### <a name="2-session-objectives-00153"></a>2.セッションの目標、0:01:53
[![セッションの目標][image-wtip-min00311-session]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=113)

- マルチテナント アプリの代替モデル、長所と短所。
- 開発、管理、リソースのコストを削減する SaaS パターン。
- サンプル アプリとスクリプト。
- PaaS の機能と SaaS パターンにより、SQL Database は拡張性が高くコスト効率の優れたマルチテナント SaaS 用データ プラットフォームになります。


&nbsp; <a name="anchor-image-wtip-min00417"/>
#### <a name="3-agenda-00409"></a>3.予定、0:04:09
[![予定][image-wtip-min00417-agenda]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=249)


&nbsp; <a name="anchor-image-wtip-min00505"/>
#### <a name="4-multi-tenant-web-app-00500"></a>4.マルチテナント Web アプリ、0:05:00
[![Wingtip SaaS アプリ:マルチテナント Web アプリ][image-wtip-min00505-web-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=300)


&nbsp; <a name="anchor-image-wtip-min00555"/>
#### <a name="5-app-web-form-in-action-00539"></a>5.動いているアプリ Web フォーム、0:05:39
[![動いているアプリ Web フォーム][image-wtip-min00555-app-web-form]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=339)


&nbsp; <a name="anchor-image-wtip-min00931"/>
#### <a name="6-per-tenant-cost-scale-isolation-recovery-00658"></a>6.テナントあたりのコスト (拡張、分離、復旧)、0:06:58
[![テナントあたりのコスト (拡張、分離、復旧)][image-wtip-min00931-per-tenant-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=418)


&nbsp; <a name="anchor-image-wtip-min01159"/>
#### <a name="7-database-models-for-multi-tenant-pros-and-cons-00952"></a>7.マルチテナントのデータベース モデル: 長所と短所、0:09:52
[![マルチテナントのデータベース モデル: 長所と短所][image-wtip-min01159-db-models-pros-cons]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=592)


&nbsp; <a name="anchor-image-wtip-min01301"/>
#### <a name="8-hybrid-model-blends-benefits-of-mtst-01229"></a>8.MT/ST のハイブリッド モデル ブレンドの利点、0:12:29
[![MT/ST のハイブリッド モデル ブレンドの利点][image-wtip-min01301-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=749)


&nbsp; <a name="anchor-image-wtip-min01644"/>
#### <a name="9-single-tenant-vs-multi-tenant-pros-and-cons-01311"></a>9.シングルテナントとマルチテナント: 長所と短所、0:13:11
[![シングルテナントとマルチテナント: 長所と短所][image-wtip-min01644-st-vs-mt]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=791)


&nbsp; <a name="anchor-image-wtip-min01936"/>
#### <a name="10-pools-are-cost-effective-for-unpredictable-workloads-01749"></a>10.予測できないワークロードに対してコスト効果の高いプール: 0:17:49
[![予測できないワークロードに対してコスト効果の高いプール][image-wtip-min01936-pools-cost]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1069)


&nbsp; <a name="anchor-image-wtip-min02008"/>
#### <a name="11-demo-of-database-per-tenant-and-hybrid-stmt-01959"></a>11.テナントごとのデータベースとハイブリッド ST/MT のデモ、0:19:59
[![テナントごとのデータベースとハイブリッド ST/MT のデモ][image-wtip-min02008-demo-st-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1199)


&nbsp; <a name="anchor-image-wtip-min02029"/>
#### <a name="12-live-app-form-showing-dojo-02010"></a>12.道場のライブ アプリ フォーム、0:20:10
[![道場のライブ アプリ フォーム][image-wtip-min02029-live-app-form-dojo]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1210)

&nbsp; <a name="anchor-image-wtip-min02854"/>
#### <a name="13-myob-and-not-a-dba-in-sight-02506"></a>13.MYOB と DBA の廃止、0:25:06
[![MYOB と DBA の廃止][image-wtip-min02854-myob-no-dba]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1506)


&nbsp; <a name="anchor-image-wtip-min02940"/>
#### <a name="14-myob-elastic-pool-usage-example-02930"></a>14.MYOB のエラスティック プール使用例、0:29:30
[![MYOB のエラスティック プール使用例][image-wtip-min02940-myob-elastic]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1770)


&nbsp; <a name="anchor-image-wtip-min03136"/>
#### <a name="15-learning-from-myob-and-other-isvs-03125"></a>15.MYOB および他の ISV からの教訓、0:31:25
[![MYOB および他の ISV からの教訓][image-wtip-min03136-learning-isvs]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1885)


&nbsp; <a name="anchor-image-wtip-min04315"/>
#### <a name="16-patterns-compose-into-e2e-saas-scenario-03142"></a>16.E2E SaaS シナリオを構成するパターン、0:31:42
[![E2E SaaS シナリオを構成するパターン][image-wtip-min04315-patterns-compose]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1902)


&nbsp; <a name="anchor-image-wtip-min04733"/>
#### <a name="17-canonical-hybrid-multi-tenant-saas-app-04604"></a>17.正規のハイブリッド マルチテナント SaaS アプリ、0:46:04
[![正規のハイブリッド マルチテナント SaaS アプリ][image-wtip-min04733-canonical-hybrid]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2764)


&nbsp; <a name="anchor-image-wtip-min04810"/>
#### <a name="18-wingtip-saas-sample-app-04801"></a>18.Wingtip SaaS サンプル アプリ、0:48:01
[![Wingtip SaaS サンプル アプリ][image-wtip-min04810-wingtip-saas-app]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2881)


&nbsp; <a name="anchor-image-wtip-min04910"/>
#### <a name="19-scenarios-and-patterns-explored-in-the-tutorials-04900"></a>19.チュートリアルで調査されているシナリオとパターン、0:49:00
[![チュートリアルで調査されているシナリオとパターン][image-wtip-min04910-scenarios-tutorials]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=2940)


&nbsp; <a name="anchor-image-wtip-min05018"/>
#### <a name="20-demo-of-tutorials-and-github-repository-05012"></a>20.チュートリアルと GitHub リポジトリのデモ、0:50:12
[![チュートリアルと GitHub リポジトリのデモ][image-wtip-min05018-demo-tutorials-github]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3012)


&nbsp; <a name="anchor-image-wtip-min05038"/>
#### <a name="21-github-repo-microsoftwingtipsaas-05032"></a>21.GitHub リポジトリ Microsoft/WingtipSaaS、0:50:32
[![GitHub リポジトリ Microsoft/WingtipSaaS][image-wtip-min05038-github-wingtipsaas]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3032)


&nbsp; <a name="anchor-image-wtip-min05620"/>
#### <a name="22-exploring-the-patterns-05615"></a>22.パターンの調査、0:56:15
[![パターンの調査][image-wtip-min05620-exploring-patterns]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3375)


&nbsp; <a name="anchor-image-wtip-min05744"/>
#### <a name="23-provisioning-tenants-and-onboarding-05619"></a>23.テナントのプロビジョニングとオンボード、0:56:19
[![テナントのプロビジョニングとオンボード][image-wtip-min05744-provisioning-tenants-onboarding-1]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3379)


&nbsp; <a name="anchor-image-wtip-min05858"/>
#### <a name="24-provisioning-tenants-and-application-connection-05752"></a>24.テナントとアプリケーションの接続のプロビジョニング、0:57:52
[![テナントとアプリケーションの接続のプロビジョニング][image-wtip-min05858-provisioning-tenants-app-connection-2]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3472)


&nbsp; <a name="anchor-image-wtip-min05943"/>
#### <a name="25-demo-of-management-scripts-provisioning-a-single-tenant-05936"></a>25.単一のテナントをプロビジョニングする管理スクリプトのデモ、0:59:36
[![単一のテナントをプロビジョニングする管理スクリプトのデモ][image-wtip-min05943-demo-management-scripts-st]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3576)


&nbsp; <a name="anchor-image-wtip-min10002"/>
#### <a name="26-powershell-to-provision-and-catalog-05956"></a>26.プロビジョニングとカタログのための PowerShell、0:59:56
[![プロビジョニングとカタログのための PowerShell][image-wtip-min10002-powershell-provision-catalog]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3596)


&nbsp; <a name="anchor-image-wtip-min10330"/>
#### <a name="27-t-sql-select--from-tenantsextended-10325"></a>27.T-SQL SELECT * FROM TenantsExtended、1:03:25
[![T-SQL SELECT * FROM TenantsExtended][image-wtip-min10330-sql-select-tenantsextended]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3805)


&nbsp; <a name="anchor-image-wtip-min10436"/>
#### <a name="28-managing-unpredictable-tenant-workloads-10334"></a>28.予測不可能なテナント ワークロードの管理、1:03:34
[![予測不可能なテナント ワークロードの管理][image-wtip-min10436-managing-unpredictable-workloads]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3814)


&nbsp; <a name="anchor-image-wtip-min10639"/>
#### <a name="29-elastic-pool-monitoring-10632"></a>29.エラスティック プールの監視、1:06:32
[![エラスティック プールの監視][image-wtip-min10639-elastic-pool-monitoring]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=3992)


&nbsp; <a name="anchor-image-wtip-min10942"/>
#### <a name="30-load-generation-and-performance-monitoring-10937"></a>30.負荷の生成とパフォーマンスの監視、1:09:37
[![負荷の生成とパフォーマンスの監視][image-wtip-min10942-load-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4117)


&nbsp; <a name="anchor-image-wtip-min11033"/>
#### <a name="31-schema-management-at-scale-10940"></a>31.大規模なスキーマ管理、1:09:40
[![大規模なスキーマ管理][image-wtip-min11033-schema-management-scale]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=34120)


&nbsp; <a name="anchor-image-wtip-min11221"/>
#### <a name="32-distributed-query-across-tenant-databases-11118"></a>32.テナント データベース間の分散クエリ、1:11:18
[![テナント データベース間の分散クエリ][image-wtip-min11221-distributed-query]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4278)


&nbsp; <a name="anchor-image-wtip-min11232"/>
#### <a name="33-demo-of-ticket-generation-11228"></a>33.チケット生成のデモ、1:12:28
[![チケット生成のデモ][image-wtip-min11232-demo-ticket-generation]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4348)


&nbsp; <a name="anchor-image-wtip-min11246"/>
#### <a name="34-ssms-adhoc-analytics-11235"></a>34.SSMS アドホック分析、1:12:35
[![SSMS アドホック分析][image-wtip-min11246-ssms-adhoc-analytics]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4355)


&nbsp; <a name="anchor-image-wtip-min11632"/>
#### <a name="35-extract-tenant-data-into-sql-dw-11546"></a>35.テナント データの SQL DW への抽出、1:15:46
[![テナント データの SQL DW への抽出][image-wtip-min11632-extract-tenant-data-sql-dw]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4546)


&nbsp; <a name="anchor-image-wtip-min11648"/>
#### <a name="36-graph-of-daily-sale-distribution-11638"></a>36.日次売上の分布のグラフ、1:16:38
[![日次売上の分布のグラフ][image-wtip-min11648-graph-daily-sale-distribution]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4598)


&nbsp; <a name="anchor-image-wtip-min11952"/>
#### <a name="37-wrap-up-and-call-to-action-11743"></a>37.まとめと行動への呼び掛け、1:17:43
[![まとめと行動への呼び掛け][image-wtip-min11952-wrap-up-call-action]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4663)


&nbsp; <a name="anchor-image-wtip-min12042"/>
#### <a name="38-resources-for-more-information-12035"></a>38.詳細な情報のリソース、1:20:35
[![詳細な情報のリソース][image-wtip-min12042-resources-more-info]](https://www.youtube.com/watch?v=jjNmcKBVjrc&t=4835)

- [ブログ記事、2017 年 5 月 22 日][resource-blog-saas-patterns-app-dev-sql-db-768h]

- *概念:* [マルチテナント SaaS データベース テナント パターン][saas-concept-design-patterns-563e]

- *チュートリアル:* [Wingtip Tickets SaaS アプリケーション][saas-how-welcome-wingtip-app-679t]

- Wingtip Tickets SaaS テナント アプリケーションのさまざまなバージョンの GitHub リポジトリ:
    - [GitHub リポジトリ - スタンドアロン アプリケーション モデル用][github-wingtip-standaloneapp]。
    - [GitHub リポジトリ - テナントごとの DB モデル][github-wingtip-dbpertenant]。
    - [GitHub リポジトリ - マルチテナント DB モデル][github-wingtip-multitenantdb]。





## <a name="next-steps"></a>次の手順

- [最初のチュートリアル記事][saas-how-welcome-wingtip-app-679t]




<!-- Image link reference IDs. -->

[image-wtip-min00003-brk3120-whole-welcome]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00003-brk3120-welcome-myob-design-saas-app-sql-db.png "ようこそスライド"

[image-wtip-min00311-session]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00311-session-objectives-takeaway.png "セッションの目標。"

[image-wtip-min00417-agenda]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00417-agenda-app-management-models-patterns.png "予定。"

[image-wtip-min00505-web-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00505-wingtip-saas-app-mt-web.png "Wingtip SaaS アプリ:マルチテナント Web アプリ"

[image-wtip-min00555-app-web-form]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00555-app-form-contoso-concert-hall-night-opera.png "動いているアプリ Web フォーム"

[image-wtip-min00931-per-tenant-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min00931-saas-data-management-concerns.png "テナントあたりのコスト (拡張、分離、復旧)"

[image-wtip-min01159-db-models-pros-cons]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01159-db-models-multi-tenant-saas-apps.png "マルチテナントのデータベース モデル: 長所と短所"

[image-wtip-min01301-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01301-hybrib-model-blends-benefits-mt-st.png "MT/ST のハイブリッド モデル ブレンドの利点"

[image-wtip-min01644-st-vs-mt]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01644-st-mt-pros-cons.png "シングルテナントとマルチテナント: 長所と短所"

[image-wtip-min01936-pools-cost]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min01936-pools-cost-effective-unpredictable-workloads.png "予測できないワークロードに対してコスト効果の高いプール"

[image-wtip-min02008-demo-st-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02008-demo-st-hybrid.png "テナントごとのデータベースとハイブリッド ST/MT のデモ"

[image-wtip-min02029-live-app-form-dojo]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02029-app-form-dogwwod-dojo.png "道場のライブ アプリ フォーム"

[image-wtip-min02854-myob-no-dba]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02854-myob-no-dba.png "MYOB と DBA の廃止"

[image-wtip-min02940-myob-elastic]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min02940-myob-elastic-pool-usage-example.png "MYOB のエラスティック プール使用例"

[image-wtip-min03136-learning-isvs]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min03136-myob-isv-saas-patterns-design-scale.png "MYOB および他の ISV からの教訓"

[image-wtip-min04315-patterns-compose]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04315-patterns-compose-into-e2e-saas-scenario-st-mt.png "E2E SaaS シナリオを構成するパターン"

[image-wtip-min04733-canonical-hybrid]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04733-canonical-hybrid-mt-saas-app.png "正規のハイブリッド マルチテナント SaaS アプリ"

[image-wtip-min04810-wingtip-saas-app]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04810-saas-sample-app-descr-of-modules-links.png "Wingtip SaaS サンプル アプリ"

[image-wtip-min04910-scenarios-tutorials]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min04910-scenarios-patterns-explored-tutorials.png "チュートリアルで調査されているシナリオとパターン"

[image-wtip-min05018-demo-tutorials-github]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05018-demo-saas-tutorials-github-repo.png "チュートリアルと GitHub リポジトリのデモ"

[image-wtip-min05038-github-wingtipsaas]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05038-github-repo-wingtipsaas.png "GitHub リポジトリ Microsoft/WingtipSaaS"

[image-wtip-min05620-exploring-patterns]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05620-exploring-patterns-tutorials.png "パターンの調査"

[image-wtip-min05744-provisioning-tenants-onboarding-1]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05744-provisioning-tenants-connecting-run-time-1.png "テナントのプロビジョニングとオンボード"

[image-wtip-min05858-provisioning-tenants-app-connection-2]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05858-provisioning-tenants-connecting-run-time-2.png "テナントとアプリケーションの接続のプロビジョニング"

[image-wtip-min05943-demo-management-scripts-st]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min05943-demo-management-scripts-provisioning-st.png "単一のテナントをプロビジョニングする管理スクリプトのデモ"

[image-wtip-min10002-powershell-provision-catalog]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10002-powershell-code.png "プロビジョニングとカタログのための PowerShell"

[image-wtip-min10330-sql-select-tenantsextended]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10330-ssms-tenantcatalog.png "T-SQL SELECT * FROM TenantsExtended"

[image-wtip-min10436-managing-unpredictable-workloads]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10436-managing-unpredictable-tenant-workloads.png "予測不可能なテナント ワークロードの管理"

[image-wtip-min10639-elastic-pool-monitoring]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10639-elastic-pool-monitoring.png "エラスティック プールの監視"

[image-wtip-min10942-load-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min10942-schema-management-scale.png "負荷の生成とパフォーマンスの監視"

[image-wtip-min11033-schema-management-scale]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11033-schema-manage-1000s-dbs-one.png "大規模なスキーマ管理"

[image-wtip-min11221-distributed-query]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11221-distributed-query-all-tenants-asif-single-db.png "テナント データベース間の分散クエリ"

[image-wtip-min11232-demo-ticket-generation]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11232-demo-ticket-generation-distributed-query.png "チケット生成のデモ"

[image-wtip-min11246-ssms-adhoc-analytics]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11246-tsql-adhoc-analystics-db-elastic-query.png "SSMS アドホック分析"

[image-wtip-min11632-extract-tenant-data-sql-dw]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11632-extract-tenant-data-analytics-db-dw.png "テナント データの SQL DW への抽出"

[image-wtip-min11648-graph-daily-sale-distribution]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11648-graph-daily-sale-contoso-concert-hall.png "日次売上の分布のグラフ"

[image-wtip-min11952-wrap-up-call-action]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min11952-wrap-call-action-saasfeedback.png "まとめと行動への呼び掛け"

[image-wtip-min12042-resources-more-info]: media/saas-tenancy-video-index-wingtip-brk3120-20171011/wingtip-20171011-min12042-resources-blog-github-tutorials-get-started.png "詳細な情報のリソース"




<!-- Article link reference IDs. -->

[saas-concept-design-patterns-563e]: saas-tenancy-app-design-patterns.md

[saas-how-welcome-wingtip-app-679t]: saas-tenancy-welcome-wingtip-tickets-app.md


[video-on-youtube-com-478y]: https://www.youtube.com/watch?v=jjNmcKBVjrc&t=1

[video-on-channel9-479c]: https://channel9.msdn.com/Events/Ignite/Microsoft-Ignite-Orlando-2017/BRK3120


[resource-blog-saas-patterns-app-dev-sql-db-768h]: https://azure.microsoft.com/blog/saas-patterns-accelerate-saas-application-development-on-sql-database/


[github-wingtip-standaloneapp]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp/

[github-wingtip-dbpertenant]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/

[github-wingtip-multitenantdb]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/

