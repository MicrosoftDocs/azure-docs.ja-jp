---
title: "SQL Database の障害復旧訓練 | Microsoft Docs"
description: "Azure SQL Database を使用して、ミッション クリティカルなビジネス アプリケーションがエラーと障害に対して回復力を保持するための障害復旧訓練を実行するためのガイダンスとベスト プラクティスについて説明します。"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: b44a269c-fe2a-404f-b013-290030860bd1
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 07/31/2016
ms.author: sashan
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 821be267a109bdcb1a1d22107f0ab4c469e6d6aa
ms.lasthandoff: 03/10/2017


---
# <a name="performing-disaster-recovery-drill"></a>障害復旧訓練の実行
復旧ワークフローのためのアプリケーションの対応状況の検証は、定期的に実行することをお勧めします。 アプリケーションの動作、データの損失の影響やフェールオーバーによる中断を検証することをお勧めします。 これは、ビジネス継続性の証明として、ほとんどの業界標準で必要条件ともなっています。

障害復旧の訓練は以下のもので構成されています。

* データ層の停止シミュレーション
* 復旧
* 復旧後のアプリケーションの整合性の検証

[ビジネス継続性のためにアプリケーションをどのように設計](sql-database-business-continuity.md)したかによって、実行する訓練のワークフローは異なります。 下記に、Azure SQL Database のコンテキストで障害復旧の訓練を行う際のベスト プラクティスについて説明します。

## <a name="geo-restore"></a>地理リストア
障害復旧の訓練を実施する際のデータ損失の可能性を回避するために、運用環境のコピーから作成したテスト環境を使用して訓練を実行し、アプリケーションのフェールオーバーのワークフローの確認にもそれを使用することをお勧めします。

#### <a name="outage-simulation"></a>障害のシミュレーション
障害をシミュレートするために、ソース データベースを削除したり、名前を変更したりすることができます。 これによって、アプリケーションの接続エラーが発生します。

#### <a name="recovery"></a>復旧
* [こちら](sql-database-disaster-recovery.md)の説明に従って、データベースの地理リストアを別のサーバーに実行します。
* アプリケーションの構成を変更して、復旧したデータベースに接続し、「 [復旧後のデータベースの構成](sql-database-disaster-recovery.md) 」のガイドに従って、復旧を完了します。

#### <a name="validation"></a>検証
* 復旧後に、アプリケーションの整合性を検証して訓練を完了します (接続文字列、ログイン、基本的な機能のテスト、その他の標準的なアプリケーションのサインオフのプロシージャの検証など)。

## <a name="geo-replication"></a>地理レプリケーション
地理レプリケーションを使用して保護されたデータベースの場合、訓練には、セカンダリ データベースへの計画フェールオーバーが含まれます。 計画フェールオーバーでは、ロールが切り替わったときに、プライマリ データベースとセカンダリ データベースの同期を維持するようにします。 計画外フェールオーバーとは異なり、この操作ではデータは失われないため、訓練を運用環境で実行できます。

#### <a name="outage-simulation"></a>障害のシミュレーション
障害をシミュレートするために、データベースに接続されている Web アプリケーションまたは仮想マシンを無効にできます。 これにより、Web クライアントの接続エラーが発生します。

#### <a name="recovery"></a>復旧
* 障害復旧リージョンのアプリケーション構成が (完全にアクセス可能な新しいプライマリになる) 前のセカンダリをポイントしていることを確認します。
* [計画フェールオーバー](scripts/sql-database-setup-geodr-and-failover-database-powershell.md) を実行して、セカンダリ データベースを新しいプライマリにします。
* 「 [復旧後のデータベースの構成](sql-database-disaster-recovery.md) 」のガイドに従って、復旧を完了します。

#### <a name="validation"></a>検証
* 復旧後に、アプリケーションの整合性を検証して訓練を完了します (接続文字列、ログイン、基本的な機能のテスト、その他の標準的なアプリケーションのサインオフのプロシージャの検証など)。

## <a name="next-steps"></a>次のステップ
* ビジネス継続性の設計および復旧シナリオについては、 [継続性のシナリオ](sql-database-business-continuity.md)
* Azure SQL Database 自動バックアップの詳細については、「 SQL Database 自動バックアップ](sql-database-automated-backups.md)
* 自動バックアップを使用して復旧する方法については、 [サービス主導のバックアップからのデータベース復元](sql-database-recovery-using-backups.md)
* より迅速な復旧オプションについては、 [アクティブ地理レプリケーション](sql-database-geo-replication-overview.md)  

