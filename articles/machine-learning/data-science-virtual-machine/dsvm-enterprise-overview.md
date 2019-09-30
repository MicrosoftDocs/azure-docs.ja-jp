---
title: チーム分析と AI 環境
titleSuffix: Azure Data Science Virtual Machine
description: エンタープライズ チーム環境でデータ サイエンス VM を展開するためのパターンについて説明します。
keywords: ディープ ラーニング, AI, データ サイエンス ツール, データ サイエンス仮想マシン, 地理空間分析, チーム データ サイエンス プロセス
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 05/08/2018
ms.openlocfilehash: 06d05d6d410af13bfbe85f3cb66523c1d48cb77c
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195654"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>データ サイエンス仮想マシンに基づくチーム分析と AI 環境 
[Data Science Virtual Machine](overview.md) (DSVM) では、人工知能 (AI) およびデータ分析用にあらかじめ構築されているソフトウェアで、Azure プラットフォーム上にリッチな環境を提供します。

従来、個々の分析デスクトップとして、DSVM が使用されています。 個々のデータ サイエンティストは、この共有された構築済みの分析環境を使用して、生産性を向上させます。 大規模な分析チームが、データ サイエンティストや AI 開発者向けの環境を計画するときに繰り返されるテーマの 1 つは、開発および実験のための共有分析インフラストラクチャです。 このインフラストラクチャは、データ サイエンス チームと分析チームとの間のコラボレーションと一貫性の促進も行う、エンタープライズ IT ポリシーに従って管理されます。

共有インフラストラクチャにより、分析環境で IT をいっそう有効に活用することができます。 一部の組織は、チームベースのデータ サイエンス/分析インフラストラクチャを "*分析サンドボックス*" と呼びます。 これにより、データ サイエンティストはさまざまなデータ資産にアクセスし、データを迅速に理解できるようになります。 このサンドボックス環境では、データ サイエンティストが実験を実行し、仮説を検証して、実稼働環境に影響を与えずに予測モデルを構築することもできます。

DSVM は、Azure インフラストラクチャ レベルで動作するため、IT 管理者は、企業の IT ポリシーに準拠して動作する DSVM を簡単に構成できます。 DSVM では、制御された方法で会社のデータ資産へのアクセスを提供しながら、さまざまな共有アーキテクチャを実装する際に十分な柔軟性を提供します。

このセクションでは、チーム ベースのデータ サイエンス インフラストラクチャとして DSVM を展開するために使用できるパターンとガイドラインについて説明します。 これらのパターンの構成要素は、サービスとしての Azure インフラストラクチャ (IaaS) から取得されるため、任意の Azure VM に適用されます。 この一連の記事では、Azure インフラストラクチャのこれらの標準的な機能を DSVM に適用することに注目します。

エンタープライズ チーム分析環境の主要な構成要素には、次が含まれます。

* [DSVM の自動スケーリングされたプール](dsvm-pools.md)
* [共通の ID およびプール内の任意の DSVM からのワークスペースへのアクセス](dsvm-common-identity.md)
* [データ ソースへのセキュリティで保護されたアクセス](dsvm-secure-access-keys.md)


このシリーズでは、前述の各トピックに対するガイダンスと指針を示します。 大規模なエンタープライズ構成で DSVM を展開する際の考慮事項と要件がすべて含まれているわけではありません。 企業で DSVM インスタンスを実装するときに使用できる、いくつかのその他の Azure リソースを次に示します。

* [ネットワークのセキュリティ](https://docs.microsoft.com/azure/security/fundamentals/network-security)
* [監視](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)と[管理](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [ログ記録と監査](https://docs.microsoft.com/azure/security/fundamentals/log-audit)
* [ロールベースのアクセス制御](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [ポリシーの設定と適用](../../governance/policy/overview.md)
* [マルウェア対策](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
* [暗号化](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [データの探索とガバナンス](https://docs.microsoft.com/azure/data-catalog/)

最後に、[Azure アーキテクチャ センター](https://docs.microsoft.com/azure/architecture/)では、クラウド ベースの分析インフラストラクチャを構築して管理するための詳細なエンドツーエンド アーキテクチャとモデルが提供されています。