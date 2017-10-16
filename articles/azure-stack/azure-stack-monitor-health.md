---
title: "Azure Stack での正常性およびアラートの監視 | Microsoft Docs"
description: "Azure Stack で正常性およびアラートを監視する方法を説明します。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 69901c7b-4673-4bd8-acf2-8c6bdd9d1546
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: twooley
ms.openlocfilehash: b2ba7ec922341464ea7160d08e475999c941c42a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>Azure Stack での正常性およびアラートの監視

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure Stack には、ユーザーが Azure Stack リージョンの正常性とアラートを表示できるようにするインフラストラクチャ監視機能が含まれています。 この **[Region management]\(リージョン管理\)** タイルは、既定で既定プロバイダー サブスクリプションの管理者ポータルに固定されており、Azure Stack のデプロイされたすべてのリージョンを一覧表示します。 このタイルは、各リージョンのアクティブな重大アラートおよび警告アラートの数を表示し、Azure Stack の正常性とアラートの機能へのエントリ ポイントとなっています。

 ![[Region Management] (リージョン管理) タイル](media/azure-stack-monitor-health/image1.png)

 ## <a name="understand-health-in-azure-stack"></a>Azure Stack の正常性について

 正常性とアラートは、正常性リソースプロバイダーによって管理されます。 Azure Stack のインフラストラクチャ コンポーネントは、Azure Stack のデプロイおよび構成時に正常性リソース プロバイダーに登録されます。 この登録により、各コンポーネントの正常性とアラートの表示が有効になります。 Azure Stack の正常性の概念は単純です。 コンポーネントの登録済みインスタンスのアラートが存在する場合、そのコンポーネントの正常性の状態は、アクティブなアラートの最大の重大度、つまり警告または重大を反映します。
 
 ## <a name="view-and-manage-component-health-state"></a>コンポーネントの正常性状態の表示および管理
 
 Azure Stack オペレーターとして、ユーザーはコンポーネントの正常性の状態を管理者ポータルと REST API および PowerShell から表示できます。
 
ポータルで正常性の状態を表示するには、**[Region management]** (リージョン管理) タイルで表示するリージョンをクリックします。 インフラストラクチャ ロールとリソース プロバイダーの正常性の状態を表示できます。 このリリースでは、コンピューティング リソースプロバイダーは正常性の状態をレポートしません。

![インフラストラクチャ ロールのリスト](media/azure-stack-monitor-health/image2.png)

リソース プロバイダーまたはインフラストラクチャ ロールをクリックすると、詳細情報を表示できます。

> [!WARNING]
>インフラストラクチャ ロールとロール インスタンスを順にクリックすると、[Start]\(起動\)、[Restart]\(再起動\)、または [Shutdown]\(シャットダウン\) のオプションが表示されます。 統合システムに更新プログラムを適用する場合は、これらのアクションを使用しないでください。 また、Azure Stack Development Kit 環境では、これらのオプションを使用**しない**でください。 これらのオプションは、インフラストラクチャ ロールあたり複数のロール インスタンスが存在する統合システム環境専用に設計されています。 開発キットでロール インスタンス (特に AzS-Xrp01) を再起動すると、システムが不安定になります。 トラブルシューティングの支援のため、問題を [Azure Stack フォーラム](https://aka.ms/azurestackforum)に投稿してください。
>
 
## <a name="view-alerts"></a>アラートを表示する

各 Azure Stack リージョンのアクティブなアラートのリストは、**[Region management]** (リージョン管理) ブレードから直接使用できます。 既定の構成の最初のタイルは**[Alerts]** (アラート) タイルで、リージョンの重大アラートと警告アラートの概要が表示されます。 アラート タイルは、このブレードの他のタイルと同様、ダッシュボードに固定してすばやくアクセスできます。   

![警告が表示された [Alerts] (アラート) タイル](media/azure-stack-monitor-health/image3.png)

**[Alerts]\(アラート\)** タイルの最上部を選択することで、リージョンのアクティブな全アラートのリストに移動します。 タイル内の **[Critical]** (重大) または **[Warning]** (警告) 行項目のいずれかを選択した場合、フィルター処理されたアラート リスト ([Critical] (重大) または [Warning] (警告)) に移動します。 

![フィルター処理された警告アラート](media/azure-stack-monitor-health/image4.png)
  
**[Alerts]** (アラート) ブレードは、状態 ([Active] (アクティブ) または [Closed] (終了)) と重大度 ([Critical] (重大) または [Warning] (警告)) の両方でフィルター処理する機能をサポートしています。 既定のビューには、すべてのアクティブなアラートが表示されます。 終了したアラートはすべて、7 日後にシステムから削除されます。

![重大または警告の状態に基づいてフィルター処理するフィルター ウィンドウ](media/azure-stack-monitor-health/image5.png)

また、**[View API]\(APIの表示\)** アクションには、リスト ビューの生成に使用された REST API が表示されます。 このアクションにより、アラートの照会に使用できる REST API 構文をすばやく理解できます。 この API は、自動化、または既存のデータ センターの監視、レポート、およびチケット発行ソリューションとの統合に使用できます。 

![REST API を表示する [View API]\(API の表示\) オプション](media/azure-stack-monitor-health/image6.png)

特定のアラートをクリックすると、アラートの詳細を表示できます。 アラートの詳細は、アラートに関連付けられているすべてのフィールドを表示し、影響を受けるコンポーネントとアラートのソースにすばやく移動できるようにします。 たとえば、インフラストラクチャ ロール インスタンスの 1 つがオフラインになるか、アクセスできない場合、次のアラートが発生します。  

![[Alert Details]\(アラートの詳細\) ブレード](media/azure-stack-monitor-health/image7.png)

インフラストラクチャ ロール インスタンスがオンラインに戻ると、このアラートは自動的に閉じます。 根本的な問題が解決されると、多くのアラートは自動的に閉じますが、すべてのアラートがそうなるわけではありません。 修復の手順を実行した後に、**[アラートを閉じる]** を選択することをお勧めします。 問題が解決しなかった場合は、Azure Stack で新しいアラートが生成されます。 問題が解決した場合は、アラートは閉じたままとなり、その他の操作は必要ありません。

## <a name="next-steps"></a>次のステップ

[Azure Stack での更新の管理](azure-stack-updates.md)

[Azure Stack でのリージョン管理](azure-stack-region-management.md)
