---
title: 既知の問題と制限事項 - Azure Database for PostgreSQL - シングル サーバーとフレキシブル サーバー (プレビュー)
description: お客様に認識していただく必要がある既知の問題の一覧を示します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/05/2020
ms.openlocfilehash: 3c26368e6281ad74d617891ba15c980117b25a6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100105411"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Azure Database for PostgreSQL - 既知の問題と制限事項

このページでは、お使いのアプリケーションに影響を与える可能性がある Azure Database for PostgreSQL の既知の問題の一覧を示します。 また、問題を回避するための軽減策と推奨事項も記載しています。

## <a name="intelligent-performance---query-store"></a>インテリジェントなパフォーマンス - クエリ ストア

Azure Database for PostgreSQL - シングル サーバーに適用されます。

| 適用できます | 原因 | 修復|
| ----- | ------ | ---- | 
| PostgreSQL 9.6、10、11 | サーバー パラメーター `pg_qs.replace_parameter_placeholders` をオンにすると、一部のまれなシナリオでサーバーがシャットダウンすることがあります。 | Azure portal の [サーバー パラメーター] セクションで、パラメーター `pg_qs.replace_parameter_placeholders` 値を `OFF` にして保存します。   | 

## <a name="server-parameters"></a>サーバー パラメーター

Azure Database for PostgreSQL - シングル サーバーとフレキシブル サーバーに適用されます

| 適用できます | 原因 | 修復| 
| ----- | ------ | ---- | 
| PostgreSQL 9.6、10、11 | サーバー パラメーター `max_locks_per_transaction` を[推奨値](https://www.postgresql.org/docs/11/kernel-resources.html)よりも高い値に変更すると、再起動後にサーバーが始動できなくなる可能性があります。 | 既定値 (32 または 64) のままにするか、PostgreSQL [ドキュメント](https://www.postgresql.org/docs/11/kernel-resources.html)に従って適切な値に変更してください。 <br> <br> サービス側では、これについては、SKU に基づいて高い値を制限するように対処しています。  | 

## <a name="next-steps"></a>次のステップ
- クエリ ストアの[ベスト プラクティス](./concepts-query-store-best-practices.md)に関する記事を参照してください。
