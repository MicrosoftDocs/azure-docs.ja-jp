---
title: IoT プラグ アンド プレイ プレビュー デバイスの認定 | Microsoft Docs
description: このチュートリアルでは、Azure Certified for IoT デバイス カタログに対象の製品の情報を追加し、デバイスを Azure IoT 認定サービスに接続して、IoT プラグ アンド プレイ認定テストを実行する方法について説明します。
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 06/21/2019
ms.openlocfilehash: 1516a132372a81d06d82de2409c48220f27b8d87
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878733"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>チュートリアル:IoT プラグ アンド プレイ プレビュー デバイスの認定

IoT プラグ アンド プレイ プレビュー デバイスを [Azure Certified for IoT デバイス カタログ](https://aka.ms/iotdevcat)で公開するには、一連の認定テストに合格する必要があります。 認定を受けるデバイスを送信するには、[Azure Certified for IoT](https://aka.ms/ACFI) ポータルを使用します。 認定テストは、[Azure IoT 認定サービス](https://aka.ms/azure-iot-aics)によって実行されます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * IoT プラグ アンド プレイ認定の要件。
> * 対象の製品名と情報をポータルに追加する方法。
> * 接続して IoT プラグ アンド プレイ インターフェイスを検出する方法。
> * IoT プラグ アンド プレイ インターフェイスを確認し、認定テストを実行する方法。
> * 認定された IoT プラグ アンド プレイ デバイスをカタログに公開する方法。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

* Microsoft パートナー センター アカウント。
* Microsoft Partner Network ID。

詳細については、[Azure Certified for IoT ポータルにオンボードする方法](howto-onboard-portal.md)に関するページを参照してください。

## <a name="certification-requirements"></a>認定要件

IoT プラグ アンド プレイ デバイスを認定するには、デバイスが次の要件を満たしている必要があります。

* IoT プラグ アンド プレイ デバイス コードがデバイスにインストールされていること。
* IoT プラグ アンド プレイ デバイスのコードが Azure IoT SDK を使用して構築されていること。
* デバイス コードで [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) がサポートされること。
* デバイス コードで[デバイス情報インターフェイス](concepts-common-interfaces.md)が実装されていること。
* 機能モデルとデバイス コードが IoT Central で動作すること。

現在カタログにあるすべてのデバイスは、事前に認定された IoT プラグ アンド プレイ デバイスと見なされます。 IoT プラグ アンド プレイ ソフトウェア コンポーネントの最終製品 (SDK やデジタル ツイン定義言語など) の品質とコンプライアンスが保証されるわけではありません。

事前に認定されたすべての IoT プラグ アンド プレイ デバイスは、Microsoft が提供する認定要件とソフトウェア コンポーネントの最終バージョンに基づいて、IoT プラグ アンド プレイの一般提供時に再認定を受ける必要があります。

## <a name="add-product-name"></a>製品名の追加

**製品**は、顧客が購入できる製品モデルのフレンドリ名です。 製品を追加するには:

1. [Azure Certified for IoT](https://aka.ms/ACFI) ポータルにサインインします。
1. ポータルの左側のメニューから **[製品]** ページに移動し、 **[+ 新規]** を選択します。
1. わかりやすい製品名を入力し、 **[作成]** を選択します。 ここで入力する名前は、デバイス カタログに表示される名前とは異なります。

## <a name="add-product-information"></a>製品情報の追加

ポータルで製品が正常に作成されると、その製品が **[製品]** ページに表示されます。 製品情報を追加するには:

1. **[製品]** ページの製品列にある、作成された製品のリンクを選択します。 状態は、ドラフト状態になっています。
1. **[製品情報]** の見出しの横にある **[編集]** リンクを選択します。 製品情報ページで、対象の製品に関する情報を入力します。必ずすべての必須フィールドに入力してください。
1. **[保存]** を選択します。 **[保存]** ボタンは、すべての必須フィールドが入力されているときにのみ表示されます。 フィールドが不完全な場合は、 **[Save and finish later]\(保存して後で完了\)** ボタンが表示されます。
1. 入力した内容を確認します。 デバイスをデバイス カタログに公開するために必要なすべてのフィールドに入力します。 **[製品情報]** の見出しの横にある **[編集]** リンクをクリックすることで、いつでも前に戻って製品詳細ページの製品情報を編集できます。

## <a name="connect-and-discover-interfaces"></a>接続とインターフェイスの検出

認定テストを実行するには、ポータルで使用できる [Azure IoT 認定サービス](https://aka.ms/azure-iot-aics) (AICS) にデバイスを接続します。

これらの手順は、認定テストを実行するための 1 回限りの手順であり、製品のデバイス コードを変更する必要はありません。 最初に、次の手順に従って AICS に接続します。

1. パートナー センター アカウントを使用してポータルにサインインします。
1. **[Connect + test]\(接続 + テスト\)** をクリックして、認定フローを開始します。
1. [[認証方法]](../iot-dps/concepts-security.md#attestation-mechanism) を選択し、[Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) を使用して対象のデバイスを AICS にプロビジョニングします。
   * [X.509 証明書](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites)を使用している場合は、生成された X.509 証明書をアップロードします。 X.509 証明書の使用方法を示すサンプル コードを確認できます: [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c)、[C#](../iot-hub/iot-hub-security-x509-get-started.md)。
   * [対称キー](../iot-dps/concepts-symmetric-key-attestation.md)を使用している場合は、対称キーをコピーしてデバイス コードに貼り付けます。
   * 現時点では、TPM 認証方法はサポートされていません。
1. 次の生成された ID をコピーし、デバイス コードに貼り付けます。
   * [登録 ID](../iot-dps/use-hsm-with-sdk.md)
   * [DPS ID](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [DPS エンドポイント](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. デバイス コードが構築されてデバイスにデプロイされたら、 **[接続]** を選択して IoT プラグ アンド プレイ インターフェイスを検出します。
1. AICS への接続に成功したら、 **[次へ]** を選択して、検出された IoT プラグ アンド プレイ インターフェイスを確認します。

## <a name="run-tests-and-publish-the-device"></a>テストの実行とデバイスの公開

レビュー ページでは、検出された IoT プラグ アンド プレイ インターフェイスを確認できます。 このページを使用して、インターフェイスに実装されているプリミティブが正しく表示されることを確認します。 また、インターフェイスの場所を確認することもできます。

1. 必須フィールドにペイロードの入力が行われていることを確認します。 これらのフィールドには、指定されたインターフェイスのコマンド プリミティブのペイロード情報が含まれます。
1. 必要な情報をすべて入力したら、 **[次へ]** を選択します。
1. 実装された IoT プラグ アンド プレイ インターフェイスのテストを実行するには、 **[テストの実行]** を選択します。
1. すべてのテストが自動的に実行されます。 テストが失敗した場合は、 **[ログの表示]** を選択すると、AICS からのエラー メッセージと、Azure IoT Hub に送信された生のテレメトリを確認できます。
1. 認定テストを完了するには、 **[完了]** を選択します。
1. 認定された IoT プラグ アンド プレイ デバイスをカタログに公開します。 認定されたデバイスをカタログに追加するには、ツール バーの **[Add to catalog]\(カタログに追加\)** を選択します。 **[Add to catalog]\(カタログに追加\)** がグレーで表示されている場合は、製品情報が不完全であるか、テストが失敗したことを意味します。 
1. [CERTIFIED AND IN THE CATALOG]\(認定およびカタログへの登録済み\) リンクを選択して、デバイス カタログに公開されているデバイスを表示します。

## <a name="next-steps"></a>次の手順

ここでは、IoT プラグ アンド プレイ デバイスの認定について学習しました。次に推奨される手順は、機能モデルの管理についてさらに学習することです。

> [!div class="nextstepaction"]
> [モデルを管理する](./howto-manage-models.md)
