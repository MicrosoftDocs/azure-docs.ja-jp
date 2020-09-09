---
title: Windows コンテナーでセルフホステッド統合ランタイムを実行する方法
description: Windows コンテナーでセルフホステッド統合ランタイムを実行する方法について説明します。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: d6f292ff89a70de90e6b86f19f73de26963d997f
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927534"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Windows コンテナーでセルフホステッド統合ランタイムを実行する方法

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

この記事では、Windows コンテナーでセルフホステッド統合ランタイムを実行する方法について説明します。
Azure Data Factory では、セルフホステッド統合ランタイムの公式な Windows コンテナー サポートを提供しています。 Docker ビルドのソース コードをダウンロードし、独自の継続的デリバリー パイプラインにビルドと実行プロセスを組み合わせることができます。 

## <a name="prerequisites"></a>前提条件 
- [Windows コンテナーの要件](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker バージョン 2.3 以降 
- セルフホステッド統合ランタイム バージョン4.11.7512.1 以降 
## <a name="get-started"></a>作業開始 
1.  Docker をインストールして Windows コンテナーを有効にします 
2.  https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container からソース コードをダウンロードします｡
3.  ‘SHIR’ フォルダーにある最新バージョンの SHIR をダウンロードします 
4.  シェルでフォルダーを開きます。 
```console
cd "yourFolderPath"
```

5.  Windows Docker イメージをビルドします。 
```console
docker build . -t "yourDockerImageName" 
```
6.  Docker コンテナーを実行します。 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> このコマンドでは AUTH_KEY が必須です。 NODE_NAME、ENABLE_HA、および HA_PORT は省略可能です。 この値を設定しない場合、コマンドでは既定値が使用されます。 既定値は、ENABLE_HA では false で、HA_PORT では 8060 です。

## <a name="container-health-check"></a>コンテナーの正常性チェック 
120 秒間の起動期間の後、正常性チェックは 30 秒ごとに定期的に実行されます。 これにより、IR の正常性状態がコンテナー エンジンに提供されます。 

## <a name="limitations"></a>制限事項
現在、Windows コンテナーでセルフホステッド統合ランタイムを実行する場合、以下の機能はサポートされていません。
- HTTP プロキシ 
- 暗号化された TLS/SSL 証明書を使用したノード間通信 
- バックアップの生成とインポート 
- デーモン サービス 
- 自動更新 

### <a name="next-steps"></a>次のステップ
- [Azure Data Factory の統合ランタイムの概念](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime)を確認します。
- [Azure portal 上でセルフホステッド統合ランタイムを作成する](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)方法を確認します。


