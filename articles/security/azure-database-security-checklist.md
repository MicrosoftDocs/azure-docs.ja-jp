---
title: Azure のデータベース セキュリティのチェックリスト | Microsoft Docs
description: この記事では、Azure のデータベース セキュリティのチェックリスト一式を提供します。
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 424453e70e5b62e408f408cd5ae8169cddb14dd7
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56109930"
---
# <a name="azure-database-security-checklist"></a>Azure のデータベース セキュリティのチェックリスト

Azure データベースには、セキュリティ向上を促進するために、アクセスを制限および制御するために使用できるセキュリティ制御が多数組み込まれています。

チェックの内容は次のとおりです

-   IP アドレスで接続を制限する[ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)を作成できるファイアウォール
-   Azure Portal からアクセス可能なサーバーレベルのファイアウォール
-   SSMS からアクセス可能なデータベースレベルのファイアウォール規則
-   セキュリティで保護された接続文字列を使用した、データベースへのセキュリティで保護された接続
-   アクセス管理の使用
-   データの暗号化
-   SQL Database 監査
-   SQL Database の脅威の検出

## <a name="introduction"></a>はじめに
クラウド コンピューティングには新しいセキュリティ パラダイムが必要ですが、これは多くのアプリケーション ユーザー、データベース管理者、およびプログラマにとって馴染みのないものです。 そのため、把握されているセキュリティ リスクを理由にデータ管理用のクラウド インフラストラクチャを実装することを躊躇している組織もあります。 ただし、この懸念の多くは、Microsoft Azure および Microsoft Azure SQL Database に組み込まれているセキュリティ機能への理解を深めることで緩和できます。

## <a name="checklist"></a>チェック リスト
このチェックリストを確認する前に、「[Azure Database Security Best Practices (Azure データベース セキュリティのベスト プラクティス)](https://docs.microsoft.com/azure/security/azure-database-security-best-practices)」の記事をお読みになることをお勧めします。 このベスト プラクティスを理解しておくことで、チェックリストを最大限に活用できます。 このチェックリストを使用して、Azure のデータベース セキュリティの重要な問題に対処しているかどうかを確認することができます。


|チェックリストのカテゴリ| 説明|
| ------------ | -------- |
|**データの保護**||
| <br> 移動/転送時の暗号化| <ul><li>[トランスポート層セキュリティ](https://docs.microsoft.com/windows-server/security/tls/transport-layer-security-protocol)は、データがネットワークに移動するときのデータの暗号化を目的としています。</li><li>データベースは、TLS (トランスポート層セキュリティ) 経由の [TDS (表形式データ ストリーム)](https://msdn.microsoft.com/library/dd357628.aspx) プロトコルに基づく、クライアントからのセキュリティで保護された通信を必要とします。</li></ul> |
|<br>保存時の暗号化| <ul><li>[Transparent Data Encryption](https://go.microsoft.com/fwlink/?LinkId=526242)。非アクティブなデータがデジタル形式で物理的に保存されているとき。</li></ul>|
|**アクセスの制御**||  
|<br> データベース アクセス | <ul><li>[認証](https://docs.microsoft.com/azure/sql-database/sql-database-control-access) (Azure Active Directory 認証)。AD 認証は、Azure Active Directory によって管理されている ID を使用します。</li><li>[承認](https://docs.microsoft.com/azure/sql-database/sql-database-control-access)によって、必要最小限の特権がユーザーに付与されます。</li></ul> |
|<br>アプリケーション アクセス| <ul><li>[行レベル セキュリティ](https://msdn.microsoft.com/library/dn765131) (セキュリティ ポリシーを使用。同時にユーザーの ID、ロール、または実行コンテキストに基づいて行レベルのアクセスを制限)。</li><li>[動的データ マスク](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) (アクセス許可とポリシーを使用。特権のないユーザーに対して機密データをマスクすることでデータの公開を制限)</li></ul>|
|**プロアクティブな監視**||  
| <br>追跡および検出| <ul><li>[監査](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)では、データベース イベントを追跡し、[Azure ストレージ アカウント](https://docs.microsoft.com/azure/storage/storage-create-storage-account)の監査ログ/アクティビティ ログにイベントを書き込みます。</li><li>[Azure Monitor のアクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs)を使用して Azure データベースの正常性を追跡します。</li><li>[脅威の検出](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection)は、データベースに対する潜在的なセキュリティ脅威を示す異常なデータベース アクティビティを検出します。 </li></ul> |
|<br>Azure Security Center| <ul><li>[データ監視](https://docs.microsoft.com/azure/security-center/security-center-enable-auditing-on-sql-databases)では、SQL などの Azure サービスの一元的なセキュリティ監視ソリューションとして Azure Security Center を使用します。</li></ul>|       

## <a name="conclusion"></a>まとめ
Azure データベースは、多くの組織や規制のコンプライアンス要件を満たすセキュリティ機能を豊富に備えた堅牢なデータベース プラットフォームです。 データへの物理的アクセスの制御と、Transparent Data Encryption、セルレベルの暗号化、行レベルのセキュリティによるファイル、列、または行レベルのデータ セキュリティの各種オプションの使用によって、データを簡単に保護することができます。 Always Encrypted によって、暗号化されたデータに対する操作も可能になり、アプリケーションの更新プロセスが簡略化されます。 そして、SQL Database のアクティビティの監査ログにアクセスして必要な情報を入手し、データがいつどのようにアクセスされているかを把握することができます。

## <a name="next-steps"></a>次の手順
いくつかの簡単な手順に従うだけで、悪意のあるユーザーや未承認のアクセスからデータベースを今まで以上に強力に保護できるようになります。 このチュートリアルで学習する内容は次のとおりです。

- サーバーまたはデータベースの[ファイアウォール規則](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)を設定する
- [暗号化](https://docs.microsoft.com/sql/relational-databases/security/encryption/sql-server-encryption)でデータを保護する
- [SQL Database の監査](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)を有効にする

