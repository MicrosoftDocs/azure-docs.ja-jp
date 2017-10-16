---
title: "Azure Database for PostgreSQL の制限事項 | Microsoft Docs"
description: "Azure Database for PostgreSQL の制限事項について説明します。"
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.custom: mvc
ms.topic: article
ms.date: 06/01/2017
ms.openlocfilehash: 38988fc5c0dc05331ea078534cd1a05e9eca2493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL の制限事項
Azure Database for PostgreSQL サービスはパブリック プレビューの段階です。 次のセクションでは、データベース サービス容量と機能の制限について説明します。

## <a name="service-tier-maximums"></a>サービス レベルの最大値
Azure Database for PostgreSQL では、サーバーを作成するときに複数のサービス レベルから選択できます。 詳細については、[各サービス レベルで使用できる内容](concepts-service-tiers.md)に関するページをご覧ください。  

サービス プレビュー中の各サービス レベルの接続数、コンピューティング ユニット数、およびストレージ数の最大値は次のとおりです。 

|                            |                   |
| :------------------------- | :---------------- |
| **最大接続数**        |                   |
| Basic: 50 コンピューティング ユニット     | 50 接続    |
| Basic 100 コンピューティング ユニット    | 100 接続   |
| Standard 100 コンピューティング ユニット | 200 接続   |
| Standard 200 コンピューティング ユニット | 300 接続   |
| Standard 400 コンピューティング ユニット | 400 接続   |
| Standard 800 コンピューティング ユニット | 500 接続   |
| **最大コンピューティング ユニット数**      |                   |
| Basic サービス レベル         | 100 コンピューティング ユニット |
| Standard サービス レベル      | 800 コンピューティング ユニット |
| **最大ストレージ**            |                   |
| Basic サービス レベル         | 1 TB (テラバイト)              |
| Standard サービス レベル      | 1 TB (テラバイト)              |

接続数が多すぎると、次のエラーが発生する可能性があります。
> FATAL:  sorry, too many clients already

## <a name="preview-functional-limitations"></a>プレビュー中の機能制限
### <a name="scale-operations"></a>スケール操作
1.  サービス レベル間でのサーバーの動的スケーリングは現在サポートされていません。 つまり、Basic サービス レベルと Standard サービス レベルの間での切り替えです。
2.  現時点では、事前に作成されたサーバーのストレージをオンデマンドで動的に増やすことはできません。
3.  サーバー ストレージを減らすことはできません。

### <a name="server-version-upgrades"></a>サーバー バージョンのアップグレード
- データベース エンジンのメジャー バージョン間での自動移行は現在サポートされていません。

### <a name="subscription-management"></a>サブスクリプション管理
- サブスクリプションとリソース グループ間での事前作成されたサーバーの動的な移動は現在サポートされていません。

### <a name="point-in-time-restore"></a>ポイントインタイム リストア
1.  別のサービス レベルやコンピューティング ユニットおよびストレージ サイズに復元することはできません。
2.  破棄されたサーバーへの復元はサポートされていません。

## <a name="next-steps"></a>次のステップ
- [各価格レベルで使用できる内容](concepts-service-tiers.md)について理解します
- [サポートされている PostgreSQL Database バージョン](concepts-supported-versions.md)について理解します
- [Azure Portal を使用して Azure Database for PostgreSQL でサーバーをバックアップして復元する方法](howto-restore-server-portal.md)を確認します
