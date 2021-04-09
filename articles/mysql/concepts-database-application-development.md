---
title: アプリケーション開発 - Azure Database for MySQL
description: 開発者が Azure Database for MySQL に接続するためのアプリケーション コードを記述するときに従う必要がある、設計上の考慮事項について説明します
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 45447a26c0455fc5945af8b8e9f7442af7facfbe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99830687"
---
# <a name="application-development-overview-for-azure-database-for-mysql"></a>Azure Database for MySQL のアプリケーション開発の概要 
この記事では、開発者が Azure Database for MySQL に接続するためのアプリケーション コードを記述するときに従う必要がある、設計上の考慮事項について説明します。 

> [!TIP]
> サーバーの作成、サーバーベースのファイアウォールの作成、サーバー プロパティの表示、データベースの作成、ワークベンチおよび mysql.exe を使用した接続とクエリの方法を示すチュートリアルについては、「[最初の Azure Database for MySQL データベースを設計する](tutorial-design-database-using-portal.md)」を参照してください

## <a name="language-and-platform"></a>言語とプラットフォーム
さまざまなプログラミング言語とプラットフォームで利用できるコード サンプルがあります。 コード サンプルへのリンクについては、[Azure Database for MySQL への接続に使用する接続ライブラリ](concepts-connection-libraries.md)に関するページをご覧ください

## <a name="tools"></a>ツール
Azure Database for MySQL では、Workbench などの MySQL の一般的管理ツールと互換性のある MySQL コミュニティ バージョン、または mysql.exe、[phpMyAdmin](https://www.phpmyadmin.net/)、[Navicat](https://www.navicat.com/products/navicat-for-mysql)、[dbForge Studio for MySQL](https://www.devart.com/dbforge/mysql/studio/) などの MySQL ユーティリティが使用されます。 Azure Portal、Azure CLI、および REST API を使用して、データベース サービスを操作することもできます。

## <a name="resource-limitations"></a>リソースの制限事項
Azure Database for MySQL では、サーバーが使用できるリソースを、次の 2 つのメカニズムを使用して管理します。 
- リソース ガバナンス。
- 制限の適用。

## <a name="security"></a>Security
Azure Database for MySQL には、MySQL データベースに対するアクセスの制限、データの保護、ユーザーとロールの構成、およびアクティビティの監視を行うためのリソースが用意されています。

## <a name="authentication"></a>認証
Azure Database for MySQL は、ユーザーとログインのサーバー認証をサポートしています。

## <a name="resiliency"></a>回復性
MySQL データベースへの接続中に一時エラーが発生した場合は、コードで呼び出しを再試行する必要があります。 再試行ロジックでは、複数のクライアントが同時に再試行することで SQL データベースに過大な負荷がかかるのを防ぐために、バックオフ ロジックを使用することをお勧めします。

- コード サンプル: 再試行ロジックを示すコード サンプルについては、[Azure Database for MySQL への接続に使用する接続ライブラリ](concepts-connection-libraries.md)に関するページで、好みの言語用のサンプルをご覧ください

## <a name="managing-connections"></a>接続の管理
データベースの接続リソースは限られているため、MySQL データベースにアクセスするときは、優れたパフォーマンスを実現できるよう適切に接続を使用することをお勧めします。
- 接続プールまたは永続的な接続を使用してデータベースにアクセスします。
- 有効期間が短い接続を使用してデータベースにアクセスします。 
- 接続を試行するときにアプリケーションで再試行ロジックを使用して、コンカレント接続が最大許容値に達することにより発生するエラーをキャッチします。 再試行ロジックで短い遅延を設定し、ランダムな時間だけ待機してから、追加の接続を試行します。
