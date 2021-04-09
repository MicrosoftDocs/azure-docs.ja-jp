---
title: Wingtips アプリへようこそ
description: クラウド環境での Azure SQL Database 向けのデータベース テナント モデル、およびサンプル Wingtips SaaS アプリケーションについて説明します。
keywords: SQL データベース チュートリアル
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: a2969ce6ceda0d1b71ec991b32f5b10acf9bfa12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780378"
---
# <a name="the-wingtip-tickets-saas-application"></a>Wingtip Tickets SaaS アプリケーション
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

同じ *Wingtip Tickets* SaaS アプリケーションが、3 つのサンプルの各々に実装されています。 このアプリは、シアター、クラブなど小規模の場所を対象とする SaaS アプリを一覧表示しチケット発行する、単純なイベントです。それぞれの場所はアプリのテナントで、会場の詳細、イベント一覧、顧客、チケット注文などの独自のデータを持ちます。このアプリは、管理スクリプトとチュートリアルと共に、1 つの完全な SaaS シナリオを示します。 これには、テナントのプロビジョニング、パフォーマンスの監視と管理、スキーマ管理、およびテナント間のレポートと分析が含まれます。

## <a name="three-saas-application-and-tenancy-patterns"></a>3 つの SaaS アプリケーションとテナント パターン

このアプリには 3 つのバージョンがあります。それぞれが、Azure SQL Database 上で異なるデータベース テナント パターンを探求します。  1 つ目では、独自のデータベースを持つテナントごとのスタンドアロン アプリケーションを使用します。 2 つ目では、テナントごとにデータベースを持つマルチテナント アプリを使用します。 3 つ目のサンプルは、シャードされたマルチテナント データベースを持つマルチ テナント アプリを使用します。

![ 3 つのテナント パターン][image-three-tenancy-patterns]

 各サンプルには、アプリケーション コードに加え、設計パターンと管理パターンの範囲を紹介する管理スクリプトとチュートリアルが含まれています。  各サンプルは、5 分未満でデプロイします。  3 つすべてを並行してデプロイできるので、設計と管理の違いを比較できます。

## <a name="standalone-application-per-tenant-pattern"></a>テナントごとのスタンドアロン アプリケーション パターン

テナントごとのスタンドアロン アプリ パターンは、各テナント用に 1 つのデータベースがあるシングル テナント アプリケーションを使用します。 各テナントのアプリは、そのデータベースも含めて、別々の Azure リソース グループにデプロイされます。 リソース グループはサービス プロバイダーのサブスクリプションまたはテナントのサブスクリプション内にデプロイでき、テナントに代わってプロバイダーが管理できます。 テナントごとのスタンドアロン アプリ パターンはテナントを最大限に分離させますが、複数のテナント間でリソースを共有する機会がないため、通常は最もコストが高くなります。  このパターンは、少数のテナントにデプロイされる複雑なアプリケーションに適しています。  スタンドアロン デプロイでは、他のパターンに比べ、各テナントに合わせてアプリを簡単にカスタマイズできます。  

[チュートリアル][docs-tutorials-for-wingtip-sa]および GitHub [.../Microsoft/WingtipTicketsSaaS-StandaloneApp][github-code-for-wingtip-sa] 上のコードを確認してください。

## <a name="database-per-tenant-pattern"></a>テナントごとのデータベース パターン

テナントごとデータベースのパターンが効果的なのは、テナントの分離を考慮し、共有リソースの費用対効果の高い使用ができる一元的なサービスを実行したいサービス プロバイダーです。 場所またはテナントごとにデータベースが作成され、すべてのデータベースが一元的に管理されます。 データベースはエラスティック プールにホストされて、高い費用対効果と簡単なパフォーマンス管理を実現できますが、これはテナントの予期できないワークロード パターンを活用しています。 カタログ データベースには、テナントとそのデータベースの間のマッピングが格納されます。 このマッピングは、[エラスティック データベース クライアント ライブラリ](elastic-database-client-library.md)のシャード マップ管理機能を使用して管理されており、アプリケーションへの効率的な接続管理を提供します。

[チュートリアル][docs-tutorials-for-wingtip-dpt]および GitHub [.../Microsoft/WingtipTicketsSaaS-DbPerTenant][github-code-for-wingtip-dpt] 上のコードを確認してください。

## <a name="sharded-multi-tenant-database-pattern"></a>シャード マルチテナント データベース パターン

マルチテナント データベースは、テナントあたりのコスト削減を求めていて、テナント分離は減らしてもよい、というサービス プロバイダーに効果的です。 このパターンでは、1 つのデータベースに多数のテナントを包含することができ、テナントごとのコストを削減します。 複数のデータベース間でテナントをシャーディングすることにより、ほぼ無限のスケールが可能です。 カタログ データベースは、テナントをデータベースにマップします。  

このパターンでは *ハイブリッド* モデルも可能で、1 つのデータベース内の複数テナントでコストを最適化したり、独自のデータベース内の 1 つのテナントで分離を最適化したりすることもできます。 この選択は、テナントのプロビジョニング時あるいはその後のどちらでも、アプリケーションに影響を与えずに、テナントごとに可能です。  このモデルは、テナントのグループを異なる方法で処理する必要がある場合に効果的に使用できます。 たとえば、低コストのテナントを共有データベースに割り当て、プレミアム テナントを独自のデータベースに割り当てることができます。 

[チュートリアル][docs-tutorials-for-wingtip-mt]および GitHub [.../Microsoft/WingtipTicketsSaaS-MultiTenantDb][github-code-for-wingtip-mt] 上のコードを確認してください。

## <a name="next-steps"></a>次のステップ

#### <a name="conceptual-descriptions"></a>概念の説明

- アプリケーションのテナント パターンの詳細な説明については、「[マルチ テナント SaaS データベース テナント パターン][saas-tenancy-app-design-patterns-md]」をご覧ください。

#### <a name="tutorials-and-code"></a>チュートリアルとコード

- テナントごとのスタンドアロン アプリ:
    - [スタンドアロン アプリのチュートリアル][docs-tutorials-for-wingtip-sa]。
    - [GitHub 上のスタンドアロン アプリのコード][github-code-for-wingtip-sa]。

- テナントごとのデータベース
    - [テナントごとのデータベースのチュートリアル][docs-tutorials-for-wingtip-dpt]。
    - [GitHub 上のテナントごとのデータベースのコード][github-code-for-wingtip-dpt]。

- シャード マルチテナント
    - [シャード マルチテナントのチュートリアル][docs-tutorials-for-wingtip-mt]。
    - [GitHub 上のシャード マルチテナントのコード][github-code-for-wingtip-mt]。



<!-- Image references. -->

[image-three-tenancy-patterns]: media/saas-tenancy-welcome-wingtip-tickets-app/three-tenancy-patterns.png " 3 つのテナント パターン"

<!-- Docs.ms.com references. -->

[saas-tenancy-app-design-patterns-md]: saas-tenancy-app-design-patterns.md

<!-- WWWeb http references. -->

[docs-tutorials-for-wingtip-sa]: ./saas-standaloneapp-get-started-deploy.md
[github-code-for-wingtip-sa]: https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp

[docs-tutorials-for-wingtip-dpt]: ./saas-dbpertenant-wingtip-app-overview.md
[github-code-for-wingtip-dpt]: https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant

[docs-tutorials-for-wingtip-mt]: ./saas-multitenantdb-get-started-deploy.md
[github-code-for-wingtip-mt]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb