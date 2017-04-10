---
title: "初めての Azure IoT Gateway モジュールの作成 | Microsoft Docs"
description: "モジュールを作成し、サンプル アプリに追加してモジュールの動作をカスタマイズします。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: cd7660f4-7b8b-4091-8d71-bb8723165b0b
ms.service: iot-hub
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 027597c73d7282ef929363bab904e7d3d423d3d4
ms.openlocfilehash: db2757015cf8e821cffcfe39bcbd1559c23fc7ed
ms.lasthandoff: 02/23/2017


---
# <a name="lesson-5-create-your-first-azure-iot-gateway-module"></a>レッスン 5: 初めての Azure IoT Gateway モジュールの作成
Gateway SDK を使用すると、Java、.NET、または Node.js で記述されたモジュールを作成できますが、このチュートリアルでは、C 言語でモジュールを構築する手順を説明します。

## <a name="what-you-will-do"></a>学習内容

- Intel NUC で hello_world サンプル アプリをコンパイルして実行します。
- モジュールを作成し、Intel NUC でコンパイルします。
- 新しいモジュールを hello_world サンプル アプリに追加し、Intel NUC でサンプルを実行します。 新しいモジュールは、タイムスタンプ付きの "hello_world" メッセージを出力します。

## <a name="what-you-will-learn"></a>学習内容

- Intel NUC でサンプル アプリをコンパイルして実行する方法。
- モジュールを作成する方法。
- サンプル アプリにモジュールを追加する方法。

## <a name="what-you-need"></a>必要なもの

ホスト コンピューターにインストールされている Azure IoT Gateway SDK。

## <a name="folder-structure"></a>フォルダー構造

レッスン 1 で複製したサンプル コードのレッスン 5 用のサブフォルダーに、`module` フォルダーと `sample` フォルダーがあります。

![my_module](media/iot-hub-gateway-kit-lessons/lesson5/my_module.png)

- `module/my_module` フォルダーには、モジュールをビルドするためのソース コードとスクリプトが含まれています。
- `sample` フォルダーには、サンプル アプリをビルドするためのソース コードとスクリプトが含まれています。

## <a name="compile-and-run-the-helloworld-sample-app-on-intel-nuc"></a>Intel NUC で hello_world サンプル アプリをコンパイルして実行する

`hello_world` サンプルは、アプリに関連付けられた&2; つの事前定義モジュールを指定する `hello_world.json` ファイルに基づいてゲートウェイを作成します。 ゲートウェイは、5 秒ごとに "hello world" メッセージをファイルに記録します。 このセクションでは、既定のモジュールで `hello_world` アプリをコンパイルして実行します。

`hello_world` アプリをコンパイルして実行するには、ホスト コンピューターで次の手順を実行します。

1. 次のコマンドを実行して、構成ファイルを初期化します。

   ```bash
   cd iot-hub-c-intel-nuc-gateway-getting-started
   cd Lesson5
   npm install
   gulp init
   ```

1. ゲートウェイ構成ファイルを Intel NUC の MAC アドレスで更新します。 「[BLE サンプル アプリケーションの構成と実行][config_ble]」のレッスンを完了している場合は、この手順をスキップしてください。

   1. 次のコマンドを実行して、ゲートウェイ構成ファイルを開きます。

      ```bash
      # For Windows command prompt
      code %USERPROFILE%\.iot-hub-getting-started\config-gateway.json

      # For MacOS or Ubuntu
      code ~/.iot-hub-getting-started/config-gateway.json
      ```

   1. [Intel NUC を IoT ゲートウェイとして設定][setup_nuc]するときにゲートウェイの MAC アドレスを更新し、ファイルを保存します。

1. 次のコマンドを実行して、サンプル ソース コードをコンパイルします。

   ```bash
   gulp compile
   ```

   このコマンドを実行すると、サンプル ソース コードが Intel NUC に転送され、`build.sh` が実行されてコンパイルされます。

1. 次のコマンドを実行して、Intel NUC で `hello_world` アプリを実行します。

   ```bash
   gulp run
   ```

   このコマンドにより、`config.json` で指定された `../Tools/run-hello-world.js` が実行され、Intel NUC でサンプル アプリが開始されます。

   ![run_sample](media/iot-hub-gateway-kit-lessons/lesson5/run_sample.png)

## <a name="create-a-new-module-and-compile-it-on-intel-nuc"></a>新しいモジュールを作成して Intel NUC でコンパイルする

ここでは、新しいモジュールを作成し、Intel NUC でコンパイルする手順を示します。 このモジュールを実行すると、受信したメッセージがタイムスタンプと共に出力されます。 このセクションでは、初めてのカスタマイズされたゲートウェイ モジュールを作成します。

すべての Azure IoT Gateway SDK モジュールは、次のインターフェイスを実装する必要があります。

   ```C
   pfModule_ParseConfigurationFromJson Module_ParseConfigurationFromJson
   pfModule_FreeConfiguration Module_FreeConfiguration
   pfModule_Create Module_Create
   pfModule_Destroy Module_Destroy
   pfModule_Receive Module_Receive
   ```

必要に応じて、次のインターフェイスを実装できます。

   ```C
   pfModule_Start Module_Start
   ```

次の図は、モジュールの重要な状態パスを示しています。 四角形は、モジュールの状態が変化したときに操作を実行するために実装するメソッドを表します。 楕円形は、モジュールが遷移できる主な状態です。

![state_path](media/iot-hub-gateway-kit-lessons/lesson5/state_path.png)

それでは、テンプレートに基づいてモジュールを作成しましょう。

1. 次のコマンドを実行してテンプレート フォルダーを開きます。

   ```bash
   code module/my_module
   ```

   ![code_module](media/iot-hub-gateway-kit-lessons/lesson5/code_module.png)

   - `src/my_module.c` は、モジュールの作成を容易にするテンプレートとして機能します。 テンプレートでは、インターフェイスを宣言します。 必要な操作は、`MyModule_Receive` 関数にロジックを追加することだけです。
   - `build.sh` は、Intel NUC でモジュールをコンパイルするためのビルド スクリプトです。
1. `src/my_module.c` ファイルを開き、2 つのヘッダー ファイルをインクルードします。

   ```C
   #include <stdio.h>
   #include "azure_c_shared_utility/xlogging.h"
   ```

1. 次のコードを `MyModule_Receive` 関数に追加します。

   ```C
   if (message == NULL)
   {
      LogError("invalid arg message");
   }
   else
   {
      // get the message content
      const CONSTBUFFER * content = Message_GetContent(message);
      // get the local time and format it
      time_t temp = time(NULL);
      if (temp == (time_t)-1)
      {
          LogError("time function failed");
      }
      else
      {
          struct tm* t = localtime(&temp);
          if (t == NULL)
          {
              LogError("localtime failed");
          }
          else
          {
              char timetemp[80] = { 0 };
              if (strftime(timetemp, sizeof(timetemp) / sizeof(timetemp[0]), "%c", t) == 0)
              {
                  LogError("unable to strftime");
              }
              else
              {
                  printf("[%s] %.*s\r\n", timetemp, (int)content->size, content->buffer);
              }
          }
      }
   }
   ```

1. `config.json` ファイルを更新して、ホスト コンピューターの `workspace` フォルダーと Intel NUC のデプロイ パスを指定します。 コンパイル中、`workspace` フォルダー内のファイルがデプロイ パスに転送されます。

   1. 次のコマンドを実行して、`config.json` ファイルを開きます。

      ```bash
      code config.json
      ```

   1. `config.json` を次の構成で更新します。

      ```json
      "workspace": "./module/my_module",
      "deploy_path": "module/my_module"
      ```

      ![config_json](media/iot-hub-gateway-kit-lessons/lesson5/config_json.png)

1. 次のコマンドを実行して、モジュールをコンパイルします。

   ```bash
   gulp compile
   ```

   このコマンドを実行すると、ソース コードが Intel NUC に転送され、`build.sh` が実行されてモジュールがコンパイルされます。

## <a name="add-the-module-to-the-helloworld-sample-app-and-run-the-app-on-intel-nuc"></a>モジュールを hello_world サンプル アプリに追加し、Intel NUC でアプリを実行する

このタスクを実行するには、次の手順に従います。

1. 次のコマンドを実行して、Intel NUC のすべての利用可能なモジュールのバイナリ (.so ファイル) の一覧を取得します。

   ```bash
   gulp modules --list
   ```

   コンパイルした `my_module` のバイナリ パスが次のように表示されます。

   ```path
   /root/gateway_sample/module/my_module/build/libmy_module.so
   ```

   `config-gateway.json` で既定のログイン ユーザー名を変更する場合、バイナリ パスは `root` ではなく `home/<your username>` で始まります。

1. `my_module` を `hello_world` サンプル アプリに追加します。

   1. 次のコマンドを実行して、`hello_world.json` ファイルを開きます。

      ```bash
      code sample/hello_world/src/hello_world.json
      ```

   1. 次のコードを `modules` セクションに追加します。

      ```json
      {
       "name": "my_module",
       "loader": {
       "name": "native",
       "entrypoint": {
       "module.path": "/root/gateway_sample/module/my_module/build/libmy_module.so"
         }
        },
       "args": null
      }
      ```

      `module.path` の値は `/root/gateway_sample/module/my_module/build/libmy_module.so` にする必要があります。 このコードでは、`my_module` とゲートウェイとの関連付けと、`module.path` で指定されたモジュール バイナリの場所を宣言しています。
   1. 次のコードを `links` セクションに追加します。

      ```json
      {
        "source": "hello_world",
        "sink": "my_module"
      }
      ```

      このコードでは、メッセージを `hello_world` モジュールから `my_module` に転送することを指定しています。

      ![hello_world_json](media/iot-hub-gateway-kit-lessons/lesson5/hello_world_json.png)

1. 次のコマンドを実行して、`hello_world` サンプル アプリを実行します。

   ```bash
   gulp run --config sample/hello_world/src/hello_world.json
   ```

   `--config` パラメーターを使い、`hello_world.json` ファイルを使用して `run-hello-world.js` スクリプトを実行することを指定しています。

   ![hello_world_new](media/iot-hub-gateway-kit-lessons/lesson5/hello_world_new.png)

おめでとうございます。 これで、この新しいモジュールの動作を確認できました。このモジュールでは、単純に "hello world" メッセージがタイムスタンプ付きで出力され、元の "hello_world" モジュールとは異なる結果となります。

## <a name="next-steps"></a>次のステップ

ここでは、新しいモジュールを作成して hello_world サンプルに追加し、ゲートウェイ上の新しいモジュールでサンプル アプリが実行されるように設定しました。 Azure IoT Gateway モジュールについてさらに学習したい方のために、[https://github.com/Azure/azure-iot-gateway-sdk/tree/master/modules](https://github.com/Azure/azure-iot-gateway-sdk/tree/master/modules) にさらに多くのモジュール サンプルが用意されています。

<!-- Images and links -->

[config_ble]: iot-hub-gateway-kit-c-lesson3-configure-ble-app.md
[setup_nuc]: iot-hub-gateway-kit-c-lesson1-set-up-nuc.md

