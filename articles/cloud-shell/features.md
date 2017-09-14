---
title: "Azure Cloud Shell (プレビュー) の機能 | Microsoft Docs"
description: "Azure Cloud Shell の機能の概要"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: juluk
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 67f03d5857e37b253ac57536e289b5468d69e9b5
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---

# <a name="features-and-tools-for-azure-cloud-shell"></a>Azure Cloud Shell の機能とツール
Azure Cloud Shell は、Azure リソースを開発および管理するための、ブラウザー ベースのシェル環境です。

Cloud Shell は、インストール、バージョン管理、およびコンピューター管理のオーバーヘッドなしで、ブラウザーでアクセスできる、事前構成済みのシェル環境を提供します。

Cloud Shell は要求ごとにコンピューターをプロビジョニングするため、セッション間ではコンピューターの状態が保持されません。 Cloud Shell は対話型セッション向けに構築されているため、シェルが 20 分間非アクティブの状態になると、シェルは自動的に終了します。

## <a name="bash-in-cloud-shell"></a>Cloud Shell の Bash
### <a name="tools"></a>ツール
|カテゴリ   |名前   |
|---|---|
|Linux シェル インタープリター|Bash<br> sh               |
|Azure ツール            |[Azure CLI 2.0](https://github.com/Azure/azure-cli) と [1.0](https://github.com/Azure/azure-xplat-cli)<br> [AzCopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy)<br> [Batch Shipyard](https://github.com/Azure/batch-shipyard)     |
|テキスト エディター           |vim<br> nano<br> emacs       |
|ソース管理         |git                    |
|ビルド ツール            |make<br> maven<br> npm<br> pip         |
|コンテナー             |[Docker CLI](https://github.com/docker/cli)/[Docker Machine](https://github.com/docker/machine)<br> [Kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/)<br> [下書き](https://github.com/Azure/draft)<br> [DC/OS CLI](https://github.com/dcos/dcos-cli)         |
|データベース              |MySQL クライアント<br> PostgreSql クライアント<br> [sqlcmd ユーティリティ](https://docs.microsoft.com/sql/tools/sqlcmd-utility)<br> [mssql-scripter](https://github.com/Microsoft/sql-xplat-cli) |
|その他                  |iPython クライアント<br> [Cloud Foundry CLI](https://github.com/cloudfoundry/cli)<br> |

### <a name="language-support"></a>言語のサポート
|言語   |バージョン   |
|---|---|
|.NET       |1.01       |
|Go         |1.7        |
|Java       |1.8        |
|Node.js    |6.9.4      |
|Python     |2.7 および 3.5 (既定)|

## <a name="secure-automatic-authentication"></a>自動認証をセキュリティで保護する
Cloud Shell は、Azure CLI 2.0 のアカウント アクセスを安全かつ自動的に認証します。

## <a name="azure-files-persistence"></a>Azure ファイルの永続化
Cloud Shell は、一時的なマシンを使用して、要求ごとに割り当てられるため、$Home の外のファイルやマシンの状態は、セッション間で保持されません。
セッション間でファイルを保持する場合、Cloud Shell の初回起動時に、Azure ファイル共有のアタッチについてのチュートリアルがあります。
チュートリアルが完了すると、今後のすべてのセッションで、記憶域が自動的にアタッチされます。

[Azure ファイル共有を Cloud Shell にアタッチする方法の詳細について説明します。](persisting-shell-storage.md)

## <a name="next-steps"></a>次のステップ
[Cloud Shell のクイック スタート](quickstart.md) <br>
[Azure CLI 2.0 について](https://docs.microsoft.com/cli/azure/) <br>
