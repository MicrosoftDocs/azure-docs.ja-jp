---
title: インクルード ファイル
description: インクルード ファイル
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59630521"
---
## <a name="download-the-source-code"></a>ソース コードをダウンロードする

リモート監視ソース コード リポジトリには、マイクロサービスの Docker イメージを実行するために必要なソース コードと Docker 構成ファイルが含まれています。

複製してローカル バージョンのリポジトリを作成するには、コマンドライン環境を使用してローカル コンピューター上の適切なフォルダーに移動します。 その後、次のいずれかのコマンド セットを実行して、.NET リポジトリを複製します。

.NET マイクロサービスの実装の最新バージョンをダウンロードするには、次のコマンドを実行します。

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> これらのコマンドでは、マイクロサービスのローカル実行に使用するスクリプトだけでなく、すべてのマイクロサービスのソース コードがダウンロードされます。 Docker 内のマイクロサービスを実行するためのソース コードは必要ありませんが、後でソリューション アクセラレータを変更し、その変更をローカルにテストすることを予定している場合は、ソース コードが役に立ちます。

## <a name="deploy-the-azure-services"></a>Azure サービスをデプロイする

この記事ではマイクロサービスをローカルに実行する方法を示しますが、これらはクラウド内で実行されている Azure サービスに依存します。 Azure サービスをデプロイするには、次のスクリプトを使用します。 次のスクリプトの例では、Windows コンピューターで .NET リポジトリを使用していることを前提としています。 別の環境で作業している場合は、パス、ファイル拡張子、およびパスの区切り記号を適切に調整してください。

### <a name="create-new-azure-resources"></a>新しい Azure リソースを作成する

必要な Azure リソースをまだ作成していない場合は、次の手順のようにします。

1. コマンドライン環境で、リポジトリの複製コピーの **\services\scripts\local\launch** フォルダーに移動します。

1. 次のコマンドを実行して **pcs** CLI ツールをインストールし、Azure アカウントにサインインします。

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. **start.cmd** スクリプトを実行します。 このスクリプトでは、次の情報を入力するよう求められます。
   * ソリューション名。
   * 使用する Azure サブスクリプション。
   * 使用する Azure データセンターの場所。

     スクリプトで、指定したソリューション名のリソース グループが Azure に作成されます。 このリソース グループには、ソリューション アクセラレータが使用する Azure リソースが含まれます。 該当するリソースが不要になった場合は、このリソース グループを削除できます。

     また、このスクリプトは、プレフィックス **PCS** を持つ一連の環境変数をローカル コンピューターに追加します。 これらの環境変数では、リモート監視で Azure Key Vault リソースから読み取れるようにするための詳細が提供されます。 リモート監視では、この Key Vault リソースからその構成値が読み取られます。

     > [!TIP]
     > また、このスクリプトは、完了すると、**\<実際のホーム フォルダー\>\\.pcs\\\<ソリューション名\>.env** という名前のファイルに環境変数を保存します。 これらは、将来のソリューション アクセラレータのデプロイに使用できます。 **docker-compose** を実行すると、ローカル コンピューターに設定した環境変数が、**services\\scripts\\local\\.env** ファイル内の値をオーバーライドすることに注意してください。

1. コマンドライン環境を終了します。

### <a name="use-existing-azure-resources"></a>既存の Azure リソースを使用する

必要な Azure リソースを既に作成している場合は、ローカル コンピューターに対応する環境変数を作成します。
以下に対する環境変数を設定します。
* **PCS_KEYVAULT_NAME** - Azure Key Vault リソースの名前
* **PCS_AAD_APPID** - AAD アプリケーションの ID
* **PCS_AAD_APPSECRET** - AAD アプリケーションのシークレット

構成値はこの Azure Key Vault リソースから読み取られます。 これらの環境変数は、デプロイから **\<実際のホーム フォルダー\>\\.pcs\\\<ソリューション名\>.env** ファイルに保存される可能性があります。 **docker-compose** を実行すると、ローカル コンピューターに設定した環境変数が、**services\\scripts\\local\\.env** ファイル内の値をオーバーライドすることに注意してください。

マイクロサービスで必要な構成の一部は、初期デプロイ上に作成された **Key Vault** インスタンスに格納されます。 必要に応じて、Key Vault 内の対応する変数を変更する必要があります。