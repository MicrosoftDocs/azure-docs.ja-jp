---
title: データ サイエンス仮想マシンに基づくチーム環境の概要 - Azure | Microsoft Docs
description: エンタープライズ チーム環境でデータ サイエンス VM を展開するためのパターンについて説明します。
keywords: ディープ ラーニング, AI, データ サイエンス ツール, データ サイエンス仮想マシン, 地理空間分析, チーム データ サイエンス プロセス
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 2e17ab5cfe51f3772148cc730c982671d602a79a
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2019
ms.locfileid: "56340480"
---
# <a name="data-science-virtual-machine-based-team-analytics-and-ai-environment"></a>データ サイエンス仮想マシンに基づくチーム分析と AI 環境 
[データ サイエンス仮想マシン](overview.md) (DSVM) は、人工知能 (AI) およびデータ分析用にあらかじめ構築されているソフトウェアで、Azure プラットフォーム上のリッチな環境を提供します。 

従来、個々の分析デスクトップとして、DSVM が使用されています。 個々のデータ科学者は、構築済みの分析環境の、この共有される概念によって生産性を向上させます。 大規模な分析チームが、データ サイエンティストや AI 開発者向けに分析環境を計画するときに繰り返されるテーマの 1 つは、開発および実験のための共有分析インフラストラクチャです。 このインフラストラクチャは、データ サインイン/分析チーム間のコラボレーションと一貫性を促進するエンタープライズ IT ポリシーに従って管理されます。 

共有インフラストラクチャにより、分析環境をいっそう有効に活用することもできます。 一部の組織は、チームベースのデータサイエンス/分析インフラストラクチャを "分析サンドボックス" と呼びます。 これにより、データ サイエンティストが、さまざまなデータ資産に迅速にアクセスし、データを理解して、実験を実行し、仮説を検証して、実稼働環境に影響を与えずに予測モデルを構築することができます。 

DSVM は、Azure インフラストラクチャ レベルで動作するため、IT 管理者は、企業の IT ポリシーに準拠して動作する DSVM を簡単に構成できます。 DSVM は、制御された方法で会社のデータ資産にアクセスできるさまざまな共有アーキテクチャを実装する際に十分な柔軟性を提供します。 

このセクションでは、チーム ベースのデータ サイエンス インフラストラクチャとして DSVM を展開するために使用できるパターンとガイドラインについて説明します。 これらのパターンの構築ブロックは、サービスとしての Azure インフラストラクチャ (IaaS) から取得されるので、任意の Azure VM に適用されます。 この一連の記事の目的は、Azure インフラストラクチャのこれらの標準的な機能をデータ サイエンス VM に適用することです。 

エンタープライズ チーム分析環境の主要な構成要素を次に示します。

* [自動的にスケーリングされるデータ サイエンス仮想マシンのプール](dsvm-pools.md)
* [共通の ID およびプール内の任意の DSVM からのワークスペースへのアクセス](dsvm-common-identity.md)
* [データ ソースへのセキュリティで保護されたアクセス](dsvm-secure-access-keys.md)


この一連の記事では、前述の各項目に対するガイダンスと指針を示します。 大規模なエンタープライズ構成で DSVM を展開する際のすべての考慮事項とニーズを網羅していません。 社内で DSVM インスタンスを実装するときに使用できるその他の Azure のドキュメントを次に示します。 

* [ネットワークのセキュリティ](https://docs.microsoft.com/azure/security/azure-network-security)
* [監視](https://docs.microsoft.com/azure/virtual-machines/windows/monitor)と[管理](https://docs.microsoft.com/azure/virtual-machines/windows/maintenance-and-updates)
* [ログ記録と監査](https://docs.microsoft.com/azure/security/azure-log-audit)
* [ロールベースのアクセス制御](https://docs.microsoft.com/azure/role-based-access-control/overview)
* [ポリシーの設定と適用](../../governance/policy/overview.md)
* [マルウェア対策](https://docs.microsoft.com/azure/security/azure-security-antimalware)
* [暗号化](https://docs.microsoft.com/azure/virtual-machines/windows/encrypt-disks)
* [データの探索とガバナンス](https://docs.microsoft.com/azure/data-catalog/)

[Azure アーキテクチャ センター](https://docs.microsoft.com/azure/architecture/)では、クラウド ベースの分析インフラストラクチャを構築して管理するための詳細なエンド ツー エンド アーキテクチャとパターンが提供されています。 
