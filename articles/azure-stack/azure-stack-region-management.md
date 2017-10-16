---
title: "Azure Stack でのリージョン管理 | Microsoft Docs"
description: "Azure Stack でのリージョン管理の概要。"
services: azure-stack
documentationcenter: 
author: efemmano
manager: dsavage
editor: 
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: efemmano
ms.openlocfilehash: d1310f0cb9a820366ab8712a782785e955a24134
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="region-management-in-azure-stack"></a>Azure Stack でのリージョン管理

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack にはリージョンの概念があります。リージョンは、Azure Stack インフラストラクチャを構成するハードウェア リソースから成る論理エンティティです。 リージョン管理内部では、Azure Stack インフラストラクチャのライフ サイクルを正常に運用するために必要なすべてのリソースを見つけることができます。

1 つの統合システム デプロイ (*Azure Stack クラウド*  と呼ばれます) が 1 つのリージョンを構成します。 各 Azure Stack 開発キットには、**local** という名前の 1 つのリージョンがあります。 2 番目の Azure Stack 統合システムをデプロイするか、または別のハードウェア上に開発キットの別のインスタンスを設定すると、この Azure Stack クラウドは別のリージョンになります。

## <a name="information-available-through-the-region-management-tile"></a>[リージョンの管理] タイルから使用可能な情報
Azure Stack には、**[Region management]** (リージョン管理) タイルで使用できる一連のリージョン管理機能があります。 このタイルは、管理者ポータルの既定のダッシュボードで Azure Stack オペレーターが使用できます。 このタイルを使用して、Azure Stack リージョンと、リージョン固有のそのコンポーネントを監視および更新できます。

 ![[Region Management] (リージョン管理) タイル](media/azure-stack-manage-region/image1.png)

 [Region management] (リージョン管理) タイルでリージョンをクリックすると、次の情報にアクセスできます。

  ![[Region management] (リージョン管理) ブレードのウィンドウの説明](media/azure-stack-manage-region/image2.png)

1. **リソース メニュー**。 ここでは、特定のインフラストラクチャ管理領域にアクセスし、ストレージ アカウントや仮想ネットワークなどのユーザー リソースを表示および管理できます。

2. **[Alerts]** (アラート)。 このタイルは、システム全体のアラートを一覧表示し、それらのアラートごとの詳細を示します。

3. **[Updates]** (更新)。 このタイルでは、Azure Stack インフラストラクチャの現在のバージョンを表示できます。

4. **リソース プロバイダー**。 リソース プロバイダーは、Azure Stack の実行に必要なコンポーネントによって提供されるテナント機能を管理する場所です。 リソース プロバイダーごとに管理エクスペリエンスが異なります。 このエクスペリエンスには、プロバイダー固有のアラート、メトリック、およびリソース プロバイダー固有のその他の管理機能が含まれます。
 
5. **インフラストラクチャ ロール**。 インフラストラクチャ ロールは、Azure Stack の実行に必要なコンポーネントです。 アラートをレポートするインフラストラクチャ ロールのみが一覧表示されます。 ロールをクリックすると、特定のロールおよびそのロールが実行されているロール インスタンスに関連付けられているアラートを表示できます。 インフラストラクチャ ロール インスタンスを起動、再起動、またはシャット ダウンする機能はありますが、開発キット環境ではこれらの操作を実行**しないでください**。 これらのオプションは、インフラストラクチャ ロールあたり複数のロール インスタンスが存在するマルチノード環境専用に設計されています。 開発キットでロール インスタンス (特に AzS-Xrp01) を再起動すると、システムが不安定になります。

## <a name="next-steps"></a>次のステップ
[Azure Stack での正常性およびアラートの監視](azure-stack-monitor-health.md)

[Azure Stack での更新の管理](azure-stack-updates.md)






