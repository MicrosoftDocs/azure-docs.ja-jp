---
title: Azure Database for MySQL のサポートされているバージョン
description: サポートされている Azure Database for MySQL バージョンについて説明します。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 25251b617522840412a4868331e155285f64a18c
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962586"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>サポートされている Azure Database for MySQL サーバー バージョン

Azure Database for MySQL は、InnoDB エンジンを使用して [MySQL Community Edition](https://www.mysql.com/products/community/) から開発されました。

MySQL では、X.Y.Z の名前付けスキームが使用されます。 X はメジャー バージョン、Y はマイナー バージョン、Z はバグ修正のリリースです。 スキームの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)をご覧ください。

> [!NOTE]
> サービスでは、接続をサーバー インスタンスにリダイレクトするためにゲートウェイが使用されます。 接続が確立すると、MySQL には、MySQL サーバー インスタンスで実行されている実際のバージョンではなく、ゲートウェイに設定されている MySQL のバージョンが表示されます。 MySQL サーバー インスタンスのバージョンを判断するには、MySQL プロンプトで `SELECT VERSION();` コマンドを使用します。

Azure Database for MySQL では現在、次のバージョンがサポートされています。

## <a name="mysql-version-56"></a>MySQL バージョン 5.6

バグ修正プログラムのリリース:5.6.44

このバージョンの機能強化と修正については、MySQL の[リリース ノート](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-44.html)をご覧ください。

## <a name="mysql-version-57"></a>MySQL バージョン 5.7

バグ修正プログラムのリリース:5.7.26

このバージョンの機能強化と修正については、MySQL の[リリース ノート](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-26.html)をご覧ください。

## <a name="mysql-version-80"></a>MySQL バージョン 8.0

> [!IMPORTANT]
> MySQL 8.0 は現在プレビューの段階です。

バグ修正プログラムのリリース:8.0.15

このバージョンの機能強化と修正については、MySQL の[リリース ノート](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html)をご覧ください。

## <a name="managing-updates-and-upgrades"></a>更新プログラムとアップグレードの管理
このサービスでは、バグ修正バージョンの更新プログラムの適用が自動管理されます。 たとえば、5.7.20 から 5.7.21 などです。  

現在は、マイナー バージョンとメジャー バージョンのアップグレードはサポートされていません。 たとえば、MySQL 5.6 から MySQL 5.7 へのアップグレードはサポートされません。 5\.6 から 5.7 にアップグレードする場合は、新しいエンジンのバージョンで作成されたサーバーに[ダンプを復元](./concepts-migrate-dump-restore.md)します。

## <a name="next-steps"></a>次の手順

**サービス レベル**に基づく特定のリソース クォータと制限については、[サービス レベル](./concepts-pricing-tiers.md)に関するページをご覧ください
