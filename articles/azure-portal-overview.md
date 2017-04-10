---
title: "Microsoft Azure ポータルの概要"
description: "Microsoft Azure ポータルの使用方法を説明します。"
services: 
documentationcenter: 
author: davidwrede
manager: erikre
editor: jimbe
ms.assetid: 53cb9df1-c96a-4f4e-b022-18336cd3d697
ms.service: na
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/16/2015
ms.author: dwrede
translationtype: Human Translation
ms.sourcegitcommit: 6ab02779d65dfc1519b457486678ace5fafc9fb5
ms.openlocfilehash: 60733f973e7b88389816c1ea7cc15532ba967693
ms.lasthandoff: 02/06/2017


---
# <a name="microsoft-azure-portal-overview"></a>Microsoft Azure ポータルの概要
Azure リソースのプロビジョニングと管理は、Microsoft Azure ポータルを使って&1; か所で行うことができます。  このチュートリアルは、ポータルについて説明し、次のような主要な機能を使用する方法を示します。

* **包括的なマーケットプレイス** : マイクロソフトや他のベンダーから購入またはプロビジョニングできる数千に及ぶアイテムを参照できます。
* **統合されたスケーラブルな参照機能** : 関心のあるリソースの検索やさまざまな管理操作の簡単な実行を可能にします。
* **一貫した管理ページ** (またはブレード): 設定、アクション、課金情報、稼働状況/使用状況データのほか、多くのことを一貫した方法で公開することで、Azure の広範なサービスを管理できます。
* **個人的なエクスペリエンス** : ログイン時に表示したい情報を示すカスタム スタート画面を作成できます。  タイルが含まれた管理ブレードもカスタマイズできます。
  
  ![Azure ポータル UI の説明][UIOrientation]

## <a name="before-you-get-started"></a>開始する前に
このチュートリアルを使用するには、有効な Azure サブスクリプションが必要です。  お持ちでない場合は、今すぐ [無料試用版にサインアップ](https://azure.microsoft.com/pricing/free-trial/) してください。  サブスクリプションの取得後すぐに、[https://portal.azure.com] にアクセスできるようになります。

## <a name="how-to-create-a-resource"></a>リソースの作成方法
Azure には、1 か所で作成できる何千という項目を備えたマーケットプレースが用意されています。  たとえば、Windows Server 2012 VM を新規作成する必要があると仮定します。  +NEW ハブは、マーケットプレースの選りすぐりのカテゴリ セットにアクセスするためのエントリ ポイントです。  各カテゴリには、一部のお勧め項目と、すべてのカテゴリおよび検索結果を表示するマーケットプレース全体へのリンクがあります。 新しい Windows Server 2012 VM を作成するには、次の操作を実行します。  

1. Windows Server 2012 が推奨されているため、[コンピューティング] カテゴリからこれを選択します。  
2. フォームに基本的な情報を入力します。
3. [作成] をクリックすると、VM ですぐにプロビジョニングが開始されます。

リソースが作成されたことが通知ハブから通知され、管理ブレードが開きます (リソースは後でいつでも参照できます)。

![ポータルのカテゴリ][PortalCategories]

## <a name="how-to-find-your-resources"></a>リソースを検索する方法
頻繁にアクセスするリソースはスタート画面にピン留めできますが、頻繁にアクセスしないリソースを参照する必要がある場合があります。  次に示す参照ハブでは、すべてのリソースにアクセスできます。  サブスクリプションでフィルター、列を選択またはサイズ変更、個々の項目をクリックして管理ブレードに移動することもできます。

![参照ハブ][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>リソースへのアクセスを管理および委任する方法
このブレードから、リモート デスクトップを使って仮想マシンに接続し、主要パフォーマンス メトリックを監視し、ロール ベースのアクセス (RBAC) を使用してこの VM へのアクセスを制御し、VM を構成し、また、その他の重要な管理タスクを実行できます。  ロールに基づいたアクセスの委任は、規模に応じた管理をするうえで重要です。  詳細については、[こちら](active-directory/role-based-access-control-configure.md)をクリックしてください。 リソースへのアクセスを委任するには、次の手順を実行します。

1. リソースを参照します。
2. [Essentials] セクションで、[すべての設定] をクリックします。
3. 設定の一覧で、[ユーザー] をクリックします。
4. コマンド バーで、[追加] をクリックします。
5. ユーザーとロールを選択します。

![リソースの管理][ManageResource]

## <a name="how-to-get-help"></a>ヘルプを表示する方法
問題が発生した場合は、心配は要りません。  ポータルは、操作を支援するヘルプとサポート ページを備えています。  また、 [サポート プラン](https://azure.microsoft.com/support/plans/)によっては、ポータルで直接サポート チケットを作成できます。  サポート チケットを作成したら、ポータル内からチケットのライフサイクルを管理できます。 ヘルプとサポート ページにアクセスするには、[参照]、[ヘルプ] または [サポート] の順に移動します。  

![ヘルプとサポート][HelpSupport]

## <a name="summary"></a>概要
このチュートリアルでは次のことについて説明しました。

* サインアップ方法、サブスクリプションの取得方法、およびポータルの参照方法
* ポータル UI の詳細と、リソースを作成および参照する方法
* リソースを作成および参照する方法
* 構造または管理ブレードの詳細と、各種リソースを一貫して管理する方法
* 必要な情報が前面の中央に表示されるようにポータルをカスタマイズする方法
* ロール ベース アクセス (RBAC) を使用してリソースへのアクセスを制御する方法
* ヘルプとサポートを利用する方法

Microsoft Azure ポータルならクラウドでのアプリケーションの構築や管理を大幅に簡素化できます。  最新の状態を保つために[管理ブログ](https://azure.microsoft.com/blog/topics/management/)をご覧ください。定期的に[フィードバックを確認](https://feedback.azure.com/forums/223579-azure-preview-portal/)し、改善を行っています。  [ScottGu のブログ](http://weblogs.asp.net/scottgu)も Azure の更新情報を確認する場合に最適です。

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png

