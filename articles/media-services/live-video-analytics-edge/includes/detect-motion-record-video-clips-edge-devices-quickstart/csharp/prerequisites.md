---
ms.openlocfilehash: d3a65122772a7cb89de6d3f7f27c2f49d3f74c98
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633465"
---
* アクティブなサブスクリプションが含まれる Azure アカウント。 まだお持ちでない場合は、[無料のアカウントを作成してください](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
  > [!NOTE]
  > サービス プリンシパルを作成するためのアクセス許可を与えられた Azure サブスクリプションが必要です (**owner role** には、そのアクセス許可があります)。 適切なアクセス許可がない場合は、適切なアクセス許可をアカウント管理者に申請してください。 
* [Visual Studio Code](https://code.visualstudio.com/) と次の拡張機能。
    * [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
        > [!TIP]
        > Azure IoT Tools をインストールするときに、Docker のインストールを求められる場合があります。 このメッセージは無視してかまいません。
    * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
* [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
* クイックスタート「[モーションの検出とイベントの生成](../../../detect-motion-emit-events-quickstart.md)」を完了していない場合は、これらの手順を実行してください。
     1. [Azure リソースの設定](../../../detect-motion-emit-events-quickstart.md#set-up-azure-resources)
     1. [開発環境を設定する](../../../detect-motion-emit-events-quickstart.md#set-up-your-development-environment)
     1. [IoT Edge の配置マニフェストを生成してデプロイする](../../../detect-motion-emit-events-quickstart.md#generate-and-deploy-the-deployment-manifest)
     1. [イベントの監視の準備をする](../../../detect-motion-emit-events-quickstart.md#prepare-to-monitor-events)

> [!TIP]
> 作成された Azure リソースで問題が発生した場合は、 **[トラブルシューティング ガイド](../../../troubleshoot-how-to.md#common-error-resolutions)** を参照して、よく発生する問題を解決してください。