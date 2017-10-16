---
title: "Azure Traffic Manager を使用した重み付けラウンドロビンによるトラフィック ルーティング方法の構成 | Microsoft Docs"
description: "この記事では、Traffic Manager でラウンドロビン方法を使用してトラフィックの負荷分散を行う方法について説明します"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 6dca6de1-18f7-4962-bd98-6055771fab22
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 7aa4c9120d44ff1b3e59a57090ea04e3f8021fc4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-weighted-traffic-routing-method-in-traffic-manager"></a>Traffic Manager の重み付けトラフィック ルーティング方法の構成

トラフィック ルーティング方法の一般的なパターンは、クラウド サービスや Web サイトを含む同一のエンドポイントのセットを提供し、ラウンド ロビン方式で各エンドポイントにトラフィックを送信することです。 次の手順では、この種類のトラフィック ルーティング方法を構成する方法について説明します。

> [!NOTE]
> Azure Websites では、データセンター (リージョンとも呼ばれます) 内の Web サイトに対するラウンド ロビン負荷分散の機能が既に用意されています。 Traffic Manager を使用すると、異なるデータセンター内の Web サイトに対して、ラウンド ロビンによるトラフィック ルーティング方法を指定できます。

## <a name="to-configure-the-weighted-traffic-routing-method"></a>重み付けによるトラフィック ルーティング方法を構成するには

1. ブラウザーから [Azure Portal](http://portal.azure.com) にサインインします。 まだアカウントを持っていない場合は、[1 か月間の無料試用版](https://azure.microsoft.com/free/)にサインアップできます。 
2. ポータルの検索バーで、**Traffic Manager プロファイル**を検索し、ルーティング方法を構成するプロファイル名をクリックします。
3. **[Traffic Manager プロファイル]** ブレードで、構成に追加するクラウド サービスと Web サイトの両方があることを確認します。
4. **[設定]** セクションで **[構成]** をクリックして、**[構成]** ブレードで次のように実行します。
    1. **トラフィック ルーティング方法の設定**では、トラフィック ルーティング方法が **[重み付け]** に設定されていることを確認します。 他の方法に設定されている場合は、ドロップダウン リストから **[重み付け]** をクリックします。
    2. 次のように **[エンドポイント モニターの設定]** をこのプロファイル内のすべてのエンドポイントに対して同じに設定します。
        1. 適切な**プロトコル**を選択し、**ポート**番号を指定します。 
        2. **[パス]** にはスラッシュ ("*/*") を入力します。 エンドポイントを監視するには、パスとファイル名を指定する必要があります。 スラッシュ "/" は、相対パスの有効な入力値で、ファイルがルート ディレクトリ (既定のディレクトリ) にあることを意味します。
        3. ページの上部にある **[保存]** をクリックします。
5. 次のように構成の変更をテストします。
    1.  ポータルの検索バーで、Traffic Manager プロファイル名を検索し、表示された結果内で Traffic Manager プロファイルをクリックします。
    2.  **[Traffic Manager プロファイル]** ブレードで、**[概要]** をクリックします。
    3.  **[Traffic Manager プロファイル]** ブレードに、新しく作成した Traffic Manager プロファイルの DNS 名が表示されます。 これを任意のクライアントで使用して (たとえば、Web ブラウザーを使用して移動します)、ルーティングの種類によって決まる適切なエンドポイントにルーティングすることができます。 この場合、すべての要求がラウンド ロビン方式で各エンドポイントにルーティングされます。
6. Traffic Manager プロファイルが機能したら、権限のある DNS サーバー上の DNS レコードを編集して、会社のドメイン名が Traffic Manager ドメイン名を参照するようにします。

![Traffic Manager を使用した重み付けによるトラフィック ルーティング方法の構成][1]

## <a name="next-steps"></a>次のステップ

- [優先順位によるトラフィック ルーティング方法](traffic-manager-configure-priority-routing-method.md)について学習します。
- [パフォーマンスによるトラフィック ルーティング方法](traffic-manager-configure-performance-routing-method.md)について学習します。
- [地理的なルーティング方法](traffic-manager-configure-geographic-routing-method.md)について学習します。
- [Traffic Manager の設定のテスト](traffic-manager-testing-settings.md)方法について学習します。

<!--Image references-->
[1]: ./media/traffic-manager-weighted-routing-method/traffic-manager-weighted-routing-method.png
