---
title: Azure Traffic Manager を使用したパフォーマンスによるトラフィック ルーティング方法の構成 | Microsoft Docs
description: この記事では、最も短い待機時間でエンドポイントにトラフィックをルーティングする Traffic Manager を構成する方法について説明します
services: traffic-manager
manager: twooley
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: duau
ms.openlocfilehash: 46d71a38b79f449084f7353527f2dfb05d5b92a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95994912"
---
# <a name="configure-the-performance-traffic-routing-method"></a>パフォーマンスによるトラフィック ルーティング方法の構成

パフォーマンスによるトラフィック ルーティング方法では、クライアントのネットワークからの待機時間が最も短いエンドポイントにトラフィックを転送できます。 通常、待機時間が最も短いデータセンターは、地理的に最も近いデータセンターです。 このトラフィック ルーティング方法では、ネットワーク構成または負荷のリアルタイムの変化に対応できません。

##  <a name="to-configure-performance-routing-method"></a>パフォーマンスによるトラフィック ルーティング方法を構成するには

1. ブラウザーから [Azure Portal](https://portal.azure.com) にサインインします。 まだアカウントを持っていない場合は、[1 か月間の無料試用版](https://azure.microsoft.com/free/)にサインアップできます。 
2. ポータルの検索バーで、**Traffic Manager プロファイル** を検索し、ルーティング方法を構成するプロファイル名をクリックします。
3. **[Traffic Manager プロファイル]** ブレードで、構成に追加するクラウド サービスと Web サイトの両方があることを確認します。
4. **[設定]** セクションで **[構成]** をクリックして、**[構成]** ブレードで次のように実行します。
    1. **[traffic routing method settings (トラフィック ルーティング方法の設定)]** で、**[ルーティング方法]** に **[パフォーマンス]** を選択します。
    2. 次のように **[エンドポイント モニターの設定]** をこのプロファイル内のすべてのエンドポイントに対して同じに設定します。
        1. 適切な **プロトコル** を選択し、**ポート** 番号を指定します。 
        2. **[パス]** にはスラッシュ ("*/*") を入力します。 エンドポイントを監視するには、パスとファイル名を指定する必要があります。 スラッシュ "/" は、相対パスの有効な入力値で、ファイルがルート ディレクトリ (既定のディレクトリ) にあることを意味します。
        3. ページの上部にある **[保存]** をクリックします。
5.  次のように構成の変更をテストします。
    1.  ポータルの検索バーで、Traffic Manager プロファイル名を検索し、表示された結果内で Traffic Manager プロファイルをクリックします。
    2.  **[Traffic Manager プロファイル]** ブレードで、**[概要]** をクリックします。
    3.  **[Traffic Manager プロファイル]** ブレードに、新しく作成した Traffic Manager プロファイルの DNS 名が表示されます。 これを任意のクライアントで使用して (たとえば、Web ブラウザーを使用して移動します)、ルーティングの種類によって決まる適切なエンドポイントにルーティングすることができます。 この場合、クライアントのネットワークからの待機時間が最も短いエンドポイントにすべての要求がルーティングされます。
6. Traffic Manager プロファイルが機能したら、権限のある DNS サーバー上の DNS レコードを編集して、会社のドメイン名が Traffic Manager ドメイン名を参照するようにします。

![Traffic Manager を使用したパフォーマンスによるトラフィック ルーティング方法の構成][1]

## <a name="next-steps"></a>次のステップ

- [重み付けによるトラフィック ルーティング方法](traffic-manager-configure-weighted-routing-method.md)について学習します。
- [優先順位によるルーティング方法](traffic-manager-configure-priority-routing-method.md)について学習します。
- [地理的なルーティング方法](traffic-manager-configure-geographic-routing-method.md)について学習します。
- [Traffic Manager の設定のテスト](traffic-manager-testing-settings.md)方法について学習します。

<!--Image references-->
[1]: ./media/traffic-manager-performance-routing-method/traffic-manager-performance-routing-method.png