---
title: Azure のデータベース セキュリティのチェックリスト | Microsoft Docs
description: Azure のデータベース セキュリティのチェックリストを使用して、クラウド コンピューティングのセキュリティに関する重要な問題に対処するようにしてください。
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: 80455b442bbfb9c8a7d40799b2ddd5fc25460578
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595585"
---
# <a name="azure-database-security-checklist"></a>Azure のデータベース セキュリティのチェックリスト

Azure データベースには、セキュリティ向上を促進するために、アクセスを制限および制御するために使用できるセキュリティ制御が多数組み込まれています。

これには以下が含まれます。

-    IP アドレスで接続を制限する[ファイアウォール規則](../../azure-sql/database/firewall-configure.md)を作成できるファイアウォール
-    Azure Portal からアクセス可能なサーバーレベルのファイアウォール
-    SSMS からアクセス可能なデータベースレベルのファイアウォール規則
-    セキュリティで保護された接続文字列を使用した、データベースへのセキュリティで保護された接続
-    アクセス管理の使用
-    データの暗号化
-    SQL Database 監査
-    SQL Database の脅威の検出

## <a name="introduction"></a>はじめに
クラウド コンピューティングには新しいセキュリティ パラダイムが必要ですが、これは多くのアプリケーション ユーザー、データベース管理者、およびプログラマにとって馴染みのないものです。 そのため、把握されているセキュリティ リスクを理由にデータ管理用のクラウド インフラストラクチャを実装することを躊躇している組織もあります。 ただし、この懸念の多くは、Microsoft Azure および Microsoft Azure SQL Database に組み込まれているセキュリティ機能への理解を深めることで緩和できます。

## <a name="checklist"></a>チェック リスト
このチェックリストを確認する前に、「[Azure Database Security Best Practices (Azure データベース セキュリティのベスト プラクティス)](../../azure-sql/database/security-best-practice.md)」の記事をお読みになることをお勧めします。 このベスト プラクティスを理解しておくことで、チェックリストを最大限に活用できます。 このチェックリストを使用して、Azure のデータベース セキュリティの重要な問題に対処しているかどうかを確認することができます。


|チェックリストのカテゴリ| 説明|
| ------------ | -------- |
|**データの保護**||
| <br> 移動/転送時の暗号化| <ul><li>[トランスポート層セキュリティ](/windows-server/security/tls/transport-layer-security-protocol)は、データがネットワークに移動するときのデータの暗号化を目的としています。</li><li>データベースは、TLS (トランスポート層セキュリティ) 経由の [TDS (表形式データ ストリーム)](/openspecs/windows_protocols/ms-tds/893fcc7e-8a39-4b3c-815a-773b7b982c50) プロトコルに基づく、クライアントからのセキュリティで保護された通信を必要とします。</li></ul> |
|<br>保存時の暗号化| <ul><li>[Transparent Data Encryption](../../azure-sql/database/transparent-data-encryption-tde-overview.md)。非アクティブなデータがデジタル形式で物理的に保存されているとき。</li></ul>|
|**アクセスの制御**||  
|<br> データベース アクセス | <ul><li>[認証](../../azure-sql/database/logins-create-manage.md) (Azure Active Directory 認証)。AD 認証は、Azure Active Directory によって管理されている ID を使用します。</li><li>[承認](../../azure-sql/database/logins-create-manage.md)によって、必要最小限の特権がユーザーに付与されます。</li></ul> |
|<br>アプリケーション アクセス| <ul><li>[行レベル セキュリティ](/sql/relational-databases/security/row-level-security) (セキュリティ ポリシーを使用。同時にユーザーの ID、ロール、または実行コンテキストに基づいて行レベルのアクセスを制限)。</li><li>[動的データ マスク](../../azure-sql/database/dynamic-data-masking-overview.md) (アクセス許可とポリシーを使用。特権のないユーザーに対して機密データをマスクすることでデータの公開を制限)</li></ul>|
|**プロアクティブな監視**||  
| <br>追跡および検出| <ul><li>[監査](../../azure-sql/database/auditing-overview.md)では、データベース イベントを追跡し、[Azure ストレージ アカウント](../../storage/common/storage-account-create.md)の監査ログ/アクティビティ ログにイベントを書き込みます。</li><li>[Azure Monitor のアクティビティ ログ](../../azure-monitor/essentials/platform-logs-overview.md)を使用して Azure データベースの正常性を追跡します。</li><li>[脅威の検出](../../azure-sql/database/threat-detection-configure.md)は、データベースに対する潜在的なセキュリティ脅威を示す異常なデータベース アクティビティを検出します。 </li></ul> |
|<br>Azure Security Center| <ul><li>[データ監視](../../security-center/security-center-remediate-recommendations.md)では、SQL などの Azure サービスの一元的なセキュリティ監視ソリューションとして Azure Security Center を使用します。</li></ul>|        

## <a name="conclusion"></a>まとめ
Azure データベースは、多くの組織や規制のコンプライアンス要件を満たすセキュリティ機能を豊富に備えた堅牢なデータベース プラットフォームです。 データへの物理的アクセスの制御と、Transparent Data Encryption、セルレベルの暗号化、行レベルのセキュリティによるファイル、列、または行レベルのデータ セキュリティの各種オプションの使用によって、データを簡単に保護することができます。 Always Encrypted によって、暗号化されたデータに対する操作も可能になり、アプリケーションの更新プロセスが簡略化されます。 そして、SQL Database のアクティビティの監査ログにアクセスして必要な情報を入手し、データがいつどのようにアクセスされているかを把握することができます。

## <a name="next-steps"></a>次のステップ
いくつかの簡単な手順に従うだけで、悪意のあるユーザーや未承認のアクセスからデータベースを今まで以上に強力に保護できるようになります。 このチュートリアルで学習する内容は次のとおりです。

- サーバーまたはデータベースの[ファイアウォール規則](../../azure-sql/database/firewall-configure.md)を設定する
- [暗号化](/sql/relational-databases/security/encryption/sql-server-encryption)でデータを保護する
- [SQL Database の監査](../../azure-sql/database/auditing-overview.md)を有効にする