---
author: russell-cooks
ms.service: media-services
ms.topic: include
ms.date: 10/05/2020
ms.author: russellcooks
ms.openlocfilehash: 359c5f93516ea6f0561865bd86e4f51dedb4c3a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94358285"
---
1. Visual Studio Code で src/edge に移動します。 作成した .env ファイルと、いくつかのデプロイ テンプレート ファイルを確認できます。

    展開テンプレートでは、いくつかのプレースホルダー値で、エッジ デバイスの配置マニフェストが参照されています。 それらの変数の値が .env ファイルに格納されています。
1. 次に、src/cloud-to-device-console-app フォルダーに移動します。 ここでは、作成した appsettings.json ファイルと、他のいくつかのファイルを確認できます。

    * c2d-console-app.csproj: これは Visual Studio Code 用のプロジェクト ファイルです。
    * operations.json: このファイルには、このプログラムで実行するさまざまな操作のリストが記載されています。
    * Program.cs:このサンプル プログラム コードでは次を実行します。

        * アプリ設定を読み込みます。
        * Live Video Analytics on IoT Edge モジュールのダイレクト メソッドを呼び出して、トポロジの作成、グラフのインスタンス作成、グラフのアクティブ化を実行します。
        * **[ターミナル]** ウィンドウでグラフ出力を、 **[出力]** ウィンドウで IoT ハブに送信されたイベントを調べるために一時停止します。
        * グラフ インスタンスを非アクティブ化し、グラフ インスタンスを削除して、グラフ トポロジを削除します。
