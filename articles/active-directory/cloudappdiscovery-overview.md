---
title: "Azure Active Directory の Cloud App Discovery の機能強化 | Microsoft Docs"
description: "Cloud App Discovery でアプリケーションを検索および管理する利点と機能について説明します。"
services: active-directory
keywords: "Cloud App Discovery, アプリケーションの管理"
documentationcenter: 
author: curtand
manager: femila
tags: ignite
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: curtand
ms.reviewer: nigu
ms.openlocfilehash: 59af2a5de5936d15456058aaeacfc334b9b34d4c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="cloud-app-discovery-enhancements-in-azure-active-directory"></a>Azure Active Directory の Cloud App Discovery 拡張機能 
従業員の 80% 以上が、未承認の SaaS アプリを仕事に使用していることを認めています。 多くの管理されていないクラウド アプリをより簡単に特定できるように、Azure AD の Cloud App Discovery が Microsoft Cloud App Security のデータおよび分析機能と統合されました。 この新しい統合では、これまでとは異なるセットアップ手順が必要になります。

## <a name="what-can-i-do-now-with-the-improvements-to-cloud-app-discovery-in-azure-ad"></a>Azure AD の Cloud App Discovery の機能強化によって可能になったこと

* **クラウド アプリの使用状況の詳細な可視化**: Azure AD の Cloud App Discovery では、組織のすべてのネットワーク トラフィックとあらゆるデバイスから、**15,000 個以上のアプリ**を検出します。 
* **エージェントは不要**: この新しいバージョンの Cloud App Discovery では、ユーザー デバイスにエージェントをインストールする必要はありません。 代わりに、ファイアウォールとプロキシからインポートされたログ ファイルに基づいて検出が実行されます。 デバイスやオペレーティング システムに関係なく、組織のすべてのネットワーク トラフィックでアプリを検出できます。
* **継続的な分析とアラート**: Azure AD の Cloud App Discovery は、詳細なリスク分析を継続的に行うだけでなく、新しいアプリが使用されている場合にアラートを表示します。 受信トラフィック、送信トラフィック、検出されたアプリの上位ユーザーに関する情報など、組織でのクラウド アプリの使用状況について知識を深めることができます。

Active Directory Premium がない場合、詳細については、「[Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)」をご覧ください。

このリンクを使用して、[Azure AD の新しい Cloud App Discovery のエクスペリエンス](https://portal.cloudappsecurity.com)を確認します。

## <a name="next-steps"></a>次のステップ
次のリンクを使用して、Azure AD の Cloud App Discovery をセットアップします。

* [Cloud App Discovery の使用を開始する](cloudappdiscovery-get-started.md)
* [スナップショット レポートの作成](cloudappdiscovery-set-up-snapshots.md)
* [継続的なレポートの構成](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [カスタム ログ パーサーを使用する](https://docs.microsoft.comcommit/cloud-app-security/custom-log-parser)