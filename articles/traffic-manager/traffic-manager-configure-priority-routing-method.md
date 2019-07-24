---
title: Azure Traffic Manager を使用した優先順位によるトラフィック ルーティング方法の構成 | Microsoft Docs
description: この記事では、Traffic Manager で優先順位によるトラフィック ルーティング方法を構成する方法について説明します。
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: kumud
ms.openlocfilehash: 66c5bd9390d6fe0f26af66e18aed22c07a7da3e4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58003113"
---
# <a name="configure-priority-traffic-routing-method-in-traffic-manager"></a>Traffic Manager での優先順位によるトラフィック ルーティング方法の構成

Azure Websites では、Web サイトのモードにかかわらず、データセンター ("リージョン" と呼びます) 内の Web サイト用に、フェールオーバー機能があらかじめ用意されています。 Traffic Manager は、さまざまなデータセンター内の Web サイトにフェールオーバーを提供します。

サービスのフェールオーバーの一般的なパターンでは、トラフィックをプライマリ サービスに送信する一方で、フェールオーバー用に同じ一連のバックアップ サービスを提供します。 次の手順では、Azure のクラウド サービスと Web サイトを使用してこの優先フェールオーバーを構成する方法について説明します。

## <a name="to-configure-the-priority-traffic-routing-method"></a>優先順位によるトラフィック ルーティング方法を構成するには

1. ブラウザーから [Azure Portal](https://portal.azure.com) にサインインします。 まだアカウントを持っていない場合は、[1 か月間の無料試用版](https://azure.microsoft.com/free/)にサインアップできます。 
2. ポータルの検索バーで、**Traffic Manager プロファイル**を検索し、ルーティング方法を構成するプロファイル名をクリックします。
3. **[Traffic Manager プロファイル]** ブレードで、構成に追加するクラウド サービスと Web サイトの両方があることを確認します。
4. **[設定]** セクションで **[構成]** をクリックして、**[構成]** ブレードで次のように実行します。
    1. **トラフィック ルーティング方法の設定**では、トラフィック ルーティング方法が **[優先順位]** に設定されていることを確認します。 他の方法に設定されている場合は、ドロップダウン リストから **[優先順位]** をクリックします。
    2. 次のように **[エンドポイント モニターの設定]** をこのプロファイル内のすべてのエンドポイントに対して同じに設定します。
        1. 適切な**プロトコル**を選択し、**ポート**番号を指定します。 
        2. **[パス]** にはスラッシュ ("*/*") を入力します。 エンドポイントを監視するには、パスとファイル名を指定する必要があります。 スラッシュ "/" は、相対パスの有効な入力値で、ファイルがルート ディレクトリ (既定のディレクトリ) にあることを意味します。
        3. ページの上部にある **[保存]** をクリックします。
5. **[設定]** セクションで **[エンドポイント]** をクリックします。
6. **[エンドポイント]** ブレードで、エンドポイントの優先順位を確認します。 **優先順位**によるトラフィック ルーティング方法を選択する場合、選択したエンドポイントの順序が重要です。 エンドポイントの優先順位を確認します。  最上位のエンドポイントがプライマリ エンドポイントになります。 表示されたら、順序を再確認してください。 すべての要求が最初のエンドポイントにルーティングされますが、Traffic Manager によって異常状態が検出された場合は、トラフィックが自動的に次のエンドポイントにフェールオーバーされます。 
7. エンドポイントの優先順位を変更するには、エンドポイントをクリックし、表示された **[エンドポイント]** ブレードで **[編集]** をクリックして、必要に応じて**優先順位**の値を変更します。 
8. **[保存]** をクリックして、エンドポイントの設定の変更を保存します。
9. 構成の変更が完了したら、ページの下部にある **[保存]** をクリックします。
10. 次の手順で構成の変更をテストします。
    1.  ポータルの検索バーで、Traffic Manager プロファイル名を検索し、表示された結果内で Traffic Manager プロファイルをクリックします。
    2.  **[Traffic Manager プロファイル]** ブレードで、**[概要]** をクリックします。
    3.  **[Traffic Manager プロファイル]** ブレードに、新しく作成した Traffic Manager プロファイルの DNS 名が表示されます。 これを任意のクライアントで使用して (たとえば、Web ブラウザーを使用して移動します)、ルーティングの種類によって決まる適切なエンドポイントにルーティングすることができます。 この場合、すべての要求が最初のエンドポイントにルーティングされますが、Traffic Manager によって異常状態が検出された場合は、トラフィックが自動的に次のエンドポイントにフェールオーバーされます。
11. Traffic Manager プロファイルが機能したら、権限のある DNS サーバー上の DNS レコードを編集して、会社のドメイン名が Traffic Manager ドメイン名を参照するようにします。

![Traffic Manager を使用した優先順位によるトラフィック ルーティング方法の構成][1]

## <a name="next-steps"></a>次の手順


- [重み付けによるトラフィック ルーティング方法](traffic-manager-configure-weighted-routing-method.md)について学習します。
- [パフォーマンスによるトラフィック ルーティング方法](traffic-manager-configure-performance-routing-method.md)について学習します。
- [地理的なルーティング方法](traffic-manager-configure-geographic-routing-method.md)について学習します。
- [Traffic Manager の設定のテスト](traffic-manager-testing-settings.md)方法について学習します。

<!--Image references-->
[1]: ./media/traffic-manager-priority-routing-method/traffic-manager-priority-routing-method.png