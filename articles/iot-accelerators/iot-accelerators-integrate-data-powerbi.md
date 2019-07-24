---
title: Power BI を使用してリモート監視データを視覚化する - Azure | Microsoft Docs
description: このチュートリアルでは、Power BI Desktop と Cosmos DB を使って、リモート監視ソリューションからのデータを、カスタマイズした視覚エフェクトに統合します。 これにより、ユーザーは独自のカスタム ダッシュボードを作成し、ソリューションを使っていないユーザーと共有することができます。
author: asdonald
manager: hegate
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/01/2018
ms.topic: conceptual
ms.openlocfilehash: 3398c6d318e0e3c51d3f6cfe8af651a6e3f55c9c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094822"
---
# <a name="visualize-remote-monitoring-data-using-power-bi"></a>Power BI を使用してリモート監視データを視覚化する

このチュートリアルでは、リモート監視ソリューションのデータを Cosmos DB から Power BI に取り込む方法を説明します。 この接続を確立すると、独自のカスタム ダッシュボードを作成し、それをリモート監視ソリューションのダッシュボードに追加することができます。 このワークストリームでは、既定のグラフに加えて、より専門的なグラフを作成することができます。 その後、このチュートリアルを使って、他のデータ ストリームと統合したり、リモート監視ソリューションの外部で使用されるカスタム ダッシュボードを作成したりできます。 Power BI でダッシュボードを作成すると、ユーザーの選択に応じて、パネルが相互に対話できることも意味します。 たとえば、シミュレートされたトラックに関する情報だけを表示するフィルターを作成すると、ダッシュボードの各要素は相互作用してシミュレートされたトラックだけを表示します。 Power BI 以外のツールを使いたい場合は、選んだ視覚化ツールを使うようにこの手順を拡張し、Cosmos Database やカスタム データベース (セットアップしてある場合) にフックできます。 

## <a name="prerequisites"></a>前提条件

- 現在リモート監視ソリューションを実行している必要があります
- [Azure portal](https://portal.azure.com) および IoT Hub とソリューションが実行しているサブスクリプションに、アクセスできる必要があります
- [Power BI Desktop](https://powerbi.microsoft.com) がインストールされている必要があります (任意のバージョン)


## <a name="information-needed-from-azure-portal"></a>Azure portal からの必要な情報

1. [Azure portal](https://portal.azure.com) に移動し、必要な場合はサインインします

2. 左側のパネルの [リソース グループ] をクリックします

    ![サイド パネル ナビゲーション](./media/iot-accelerators-integrate-data-powerbi/side_panel.png)

3. Iot ソリューションが実行しているリソース グループに移動し、クリックしてそのリソース グループの概要ページに移動します。 

4. その概要ページで、[Azure Cosmos DB アカウント] という種類の項目をクリックすると、その IoT ソリューションの Cosmos DB ストリームの概要ページに移動します。

    ![リソース グループ](./media/iot-accelerators-integrate-data-powerbi/resource_groups.png)

5. 左側のパネルで [キー] セクションをクリックし、Power BI で使う以下の値をメモします。

   - URI
   - 主キー

     ![キー](./media/iot-accelerators-integrate-data-powerbi/keys.png)

## <a name="setting-up-the-stream-in-power-bi"></a>Power BI でのストリームの設定
  
1. Power BI Desktop アプリを開き、左上隅の [データの取得] をクリックします。 

    ![データの取得](./media/iot-accelerators-integrate-data-powerbi/get_data.png)

2. データの入力を要求されたら、"Azure Cosmos DB" を検索して、このコネクタを選びます。 このコネクタは、基本的に、Azure IoT ソリューションの Cosmos Database から直接データを取り出します
  
    ![Cosmos DB](./media/iot-accelerators-integrate-data-powerbi/cosmos_db.png)
  
3. 上で記録した情報を入力します。

    * URI
    * 主キー

4. Power BI にインポートするすべてのテーブルを選びます。 これにより、データの読み込みが開始しします。 ソリューションの実行時間が長いほど、データの読み込みにかかる時間も長くなります (最大数時間)。 

    ![テーブルをインポートする](./media/iot-accelerators-integrate-data-powerbi/import_tables.png)

5. データの読み込みが完了したら、Power BI の上部にある [クエリを編集] をクリックし、各テーブルの黄色いバーにある矢印をクリックしてすべてのテーブルを展開します。 これにより、基本的にすべての列が表示さます。 湿度、速度、時間などのデータが適切な型ではないことがわかります。

    ![新しい列](./media/iot-accelerators-integrate-data-powerbi/new_column.png)
  
    たとえば、コネクタを介して Power BI に取り込まれたデータは、UNIX 時間に変更されています。 この変換を調整するには、新しい列を作成し、次の式を使って日付/時刻形式にします。 

    ```text
    #datetime(1970, 1, 1, 0, 0, 0) + #duration(0, 0, 0, [Document.device.msg.received]/1000)
    ```

    ![更新されたテーブル](./media/iot-accelerators-integrate-data-powerbi/updated_table.png)
  
    Document.device.msg.received は UNIX 形式の列の 1 つであり、変換が必要な他の形式に置き換えることができます。 
  
    String 型に変換された他のデータ ポイントは、必要に応じて、上記と同じ手順で Double または Int に変更できます。

## <a name="creating-a-dashboard"></a>ダッシュボードの作成

ストリームを接続した後は、カスタマイズしたダッシュボードを作成できます。 次のダッシュボードは、シミュレートされたデバイスによって出力される利用統計情報を取得し、次のような異なるピボットを表示する例です。 

* マップ上のデバイスの場所 (右)
* 状態と重大度を含むデバイス。 (左上)
* 規則が適用されているデバイス、およびそれに対してオフになるアラートがあるかどうか (左下)

![PowerBI の視覚エフェクト](./media/iot-accelerators-integrate-data-powerbi/visual_data.png)

## <a name="publishing-the-dashboard-and-refreshing-the-data"></a>ダッシュボードの公開とデータの更新

ダッシュボードを正常に作成した後、[Power BI ダッシュボードを公開](https://docs.microsoft.com/power-bi/desktop-upload-desktop-files)して他のユーザーと共有することをお勧めします。

また、公開されたダッシュボードで[データを更新](https://docs.microsoft.com/power-bi/refresh-data)し、最新のデータ セットを保証することもできます。

## <a name="next-steps"></a>次の手順

この記事では、Power BI を使ってリモート監視データを視覚化する方法について学習しました

リモート監視ソリューションのカスタマイズの詳細については次を参照してください。

* [リモート監視ソリューションの UI のカスタマイズ](iot-accelerators-remote-monitoring-customize.md)。
* [開発者向けリファレンス ガイド](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [開発者向けトラブルシューティング ガイド](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

