---
title: ドローンの画像を取得する
description: この記事では、パートナーからドローンの画像を取得する方法について説明します。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: 18932d2ddb60242b4d7874dddf0349a62cd5c738
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102177608"
---
# <a name="get-drone-imagery-from-drone-partners"></a>ドローン パートナーからドローンの画像を取得する

この記事では、ドローン画像パートナーから Azure FarmBeats データ ハブにオルソモザイク データを取り込む方法について説明します。 オルソモザイクとは、ドローンによって収集されたデータから幾何学的に修正され接合された航空写真のイラスト/画像です。

現時点では、次の画像パートナーがサポートされています。

  ![FarmBeats ドローン画像パートナー](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

ドローンの画像データを Azure FarmBeats と統合すると、ファームで実施するドローン飛行から得られたオルソモザイク データをデータ ハブに取得するのに役立ちます。 データを使用できるようになったら、FarmBeats Accelerator でそれを表示できます。 データは、データ融合と機械学習モデル、おおび機械学習モデルの構築に使用できます。

## <a name="before-you-begin"></a>開始する前に

  - Azure FarmBeats がインストールされていることを確認します。 FarmBeats のインストール方法の詳細については、「[Azure FarmBeats のインストール](install-azure-farmbeats.md)」を参照してください。
  - ドローン画像を必要とする ファームがFarmBeats システムに定義されていることを確認します。

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>FarmBeats とのドローン画像の統合を有効にする

デバイス プロバイダーに次の情報を提供して、FarmBeats との統合を有効にします。
 - API エンドポイント
 - テナント ID
 - クライアント ID
 - クライアント シークレット

次の手順に従います。

1. この[スクリプト](https://aka.ms/farmbeatspartnerscript)をダウンロードし、ローカル ドライブ上に展開します。 この zip ファイルには 2 つのファイルが含まれています。
2. [Azure portal](https://portal.azure.com/) にサインインし、Azure Cloud Shell を開きます。 このオプションは、ポータルの右上隅にあるツール バー上で使用できます。

    ![ポータルの右上のバーで Azure Cloud Shell を開く](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 環境が **[PowerShell]** に設定されていることを確認します。

    ![PowerShell の設定](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. Cloud Shell インスタンス内で、(上記の手順 1 で) ダウンロードした 2 つのファイルをアップロードします。

    ![ファイルをアップロードする](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. ファイルがアップロードされたディレクトリに移動します 既定では、それらはホーム ディレクトリのユーザー名の下にアップロードされます。
6. 次のスクリプトを実行します。

    ```azurepowershell-interactive

    ./generateCredentials.ps1

    ```

7. 画面の指示に従って、API エンドポイント、テナント ID、クライアント ID、クライアント シークレット、および EventHub 接続文字列の値をキャプチャします。

    パートナーのドローン ソフト ウェアシステムに必要な資格情報を入力したら、FarmBeats システムからすべてのファームをインポートできます。 その後、ファームの詳細を使用して、飛行経路の計画とドローン画像の収集を行うことができます。

    ドローン プロバイダーのソフトウェアによって未加工画像が処理された後、接合されたオルソモザイク画像やその他の処理済み画像がドローン ソフトウェア システムによってデータ ハブにアップロードされます。

## <a name="view-drone-imagery"></a>ドローンの画像を表示する

データが FarmBeats データ ハブに送信されたら、FarmBeats データ ハブ API を使用してシーン ストアに対してクエリを実行できます。

または、 **[ファームの詳細]** ページで最新のドローン画像を表示できます。 画像を表示するには、次の手順に従います。

1. 画像がアップロードされたファームを選択します。 **ファーム** の詳細のページが表示されます。
2. **[Latest Precision Maps]\(最新のプレシジョン マップ\)** セクションまで下にスクロールします。
3. **[Drone Imagery]\(ドローンの画像\)** セクションで画像を確認できます。

    ![[Drone Imagery]\(ドローンの画像\) セクション](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>ドローンの画像をダウンロードする

[Drone Imagery]\(ドローンの画像\) セクションを選択すると、ポップアップが開き、ドローンを使った高解像度のオルソモザイク画像が表示されます。

![高解像度のオルソモザイク](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>すべてのドローン マップを表示する

ドローン プロバイダーによってアップロードされたファイルと画像は、 **[マップ]** セクションに表示されます。 **[マップ]** セクションを選択し、 **[ファーム]** でフィルター処理し、表示してダウンロードする適切なファイルを選択します。

  ![[マップ] セクション](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>次のステップ

FarmBeats データ ハブ [API](rest-api-in-azure-farmbeats.md) を使用して、ドローンの画像を取得する方法を確認します。
