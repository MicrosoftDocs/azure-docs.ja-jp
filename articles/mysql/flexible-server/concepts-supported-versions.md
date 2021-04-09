---
title: サポートされるバージョン - Azure Database for MySQL フレキシブル サーバー
description: Azure Database for MySQL フレキシブル サーバーでサポートされている MySQL サーバーのバージョンについて説明します
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 7ad6a576262b8e722b16c81af544a9370c2b49b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93242264"
---
# <a name="supported-versions-for-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - フレキシブル サーバーでサポートされているバージョン


> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーは現在、パブリック プレビュー段階にあります。


Azure Database for MySQL フレキシブル サーバーでは、InnoDB エンジンを使用して [MySQL Community Edition](https://www.mysql.com/products/community/) が利用されています。

MySQL では、X.Y.Z の名前付けスキームが使用されます。 X はメジャー バージョン、Y はマイナー バージョン、Z はバグ修正のリリースです。 スキームの詳細については、[MySQL のドキュメント](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)をご覧ください。

> [!NOTE]
> MySQL サーバー インスタンスのバージョンを判断するには、MySQL プロンプトで `SELECT VERSION();` コマンドを使用します。

Azure Database for MySQL では現在、次のバージョンがサポートされています。

## <a name="mysql-version-57"></a>MySQL バージョン 5.7

バグ修正プログラムのリリース:5.7.29

このサービスでは、基になるハードウェア、OS、およびデータベース エンジンの自動修正が実行されます。 パッチには、セキュリティとソフトウェアの更新プログラムが含まれています。 MySQL エンジンの場合、マイナー バージョンのアップグレードも、計画メンテナンス リリースの一部として含まれています。 ユーザーは、パッチ適用のスケジュールをシステム管理として構成することも、カスタム スケジュールを定義することもできます。 メンテナンス スケジュールの間に、パッチが適用され、パッチ適用プロセスの一環として更新を完了するためにサーバーの再起動が必要になる場合があります。 カスタム スケジュールを使用すると、ユーザーはパッチ適用のサイクルを予測可能にし、ビジネスへの影響が最小限のメンテナンス期間を選択できます。 一般に、サービスは、継続的インテグレーションとリリースの一環として、毎月のリリース スケジュールに従います。

このバージョンの機能強化と修正については、MySQL の[リリース ノート](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)をご覧ください。

## <a name="managing-updates-and-upgrades"></a>更新プログラムとアップグレードの管理
このサービスでは、バグ修正バージョンの更新プログラムの適用が自動管理されます。 たとえば、5.7.29 から 5.7.30 などです。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[MySQL を使用して Windows で PHP アプリを構築する](../../app-service/tutorial-php-mysql-app.md)<br/>
>[MySQL を使用して Linux で PHP アプリを構築する](../../app-service/tutorial-php-mysql-app.md?pivots=platform-linux%253fpivots%253dplatform-linux)<br/>
>[MySQL を使用して Java ベースの Spring アプリを構築する](/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash)<br/>