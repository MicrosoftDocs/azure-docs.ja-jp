---
title: Azure で OPC Twin クラウドの依存関係をデプロイする方法 | Microsoft Docs
description: OPC Twin Azure 依存関係をデプロイする方法です。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ae9f2b05bfc6ea6315022d04c8d267d916cf282e
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/11/2019
ms.locfileid: "59491988"
---
# <a name="deploying-dependencies-for-local-development"></a>ローカル開発の依存関係をデプロイする

この記事では、ローカル環境で開発とデバッグを行うために必要な Azure Platform サービスだけをデプロイする方法について説明します。   最後には、ローカル環境での開発とデバッグに必要なすべてのものを含むリソース グループがデプロイされます。

## <a name="deploy-azure-platform-services"></a>Azure Platform サービスをデプロイする

1. PowerShell および [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-1.1.0) 拡張機能がインストールされていることを確認します。  コマンド プロンプトまたはターミナルを開き、次を実行します。

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. プロンプトに従って、デプロイのためのリソース グループに名前を割り当てます。  スクリプトでは、Azure サブスクリプションのこのリソース グループに依存関係だけがデプロイされ、マイクロサービスはデプロイされません。  また、スクリプトでは、Azure Active Directory にアプリケーションが登録されます。  これは、OAUTH ベースの認証をサポートするために必要です。  デプロイには数分かかることがあります。

3. スクリプトが完了したら、.env ファイルの保存を選択できます。  .env 環境ファイルは、開発用コンピューター上で実行するすべてのサービスとツールの構成ファイルです。  

## <a name="troubleshooting-deployment-failures"></a>デプロイ失敗のトラブルシューティング

### <a name="resource-group-name"></a>リソース グループ名

短くてシンプルなリソース グループ名を使用してください。  この名前はリソースの命名にも使用されるので、リソースの命名要件に従っている必要があります。  

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (AAD) の登録

デプロイ スクリプトでは、Azure Active Directory への AAD アプリケーションの登録が試みられます。  選択した AAD テナントに対する権限によっては、これが失敗する可能性があります。   3 つのオプションがあります。

1. テナントの一覧から AAD テナントを選択した場合は、スクリプトを再起動して別のものを一覧から選択します。
2. または、プライベート AAD テナントをデプロイし、スクリプトを再起動して、それを使用することを選択します。
3. 認証なしで続行します。  これはローカル環境でマイクロサービスを実行しているために許されることであり、運用環境では行わないでください。  

## <a name="next-steps"></a>次の手順

既存プロジェクトへの OPC Twin サービスのデプロイが正常に済んだので、次の手順が推奨されます。

> [!div class="nextstepaction"]
> [OPC Twin モジュールをデプロイする方法について学習する](howto-opc-twin-deploy-modules.md)
