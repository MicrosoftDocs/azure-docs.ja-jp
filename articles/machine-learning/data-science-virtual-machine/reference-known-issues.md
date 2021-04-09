---
title: 'リファレンス: 既知の問題とトラブルシューティング'
titleSuffix: Azure Data Science Virtual  Machine
description: Azure Data Science Virtual Machine に関する既知の問題、回避策、トラブルシューティングの一覧を示します
services: machine-learning
ms.service: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: bcd75347f91109967ac48427ca0b8855c11b7d9b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101656858"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine の既知の問題とトラブルシューティング

この記事は、Azure Data Science Virtual Machine の使用時に発生する可能性のあるエラーや障害を見つけて修正するのに役立ちます。

## <a name="python-package-installation-issues"></a>Python パッケージのインストールに関する問題

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>pip を使用してパッケージをインストールすると、Linux 上の依存関係が壊れる

パッケージをインストールする際、`pip install` ではなく、`sudo pip install` を使用してください。

## <a name="disk-encryption-issues"></a>ディスクの暗号化の問題

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Ubuntu DSVM でディスクの暗号化が失敗する

Azure Disk Encryption (ADE) は、現在 Ubuntu DSVM ではサポートされていません。 回避策として、[Azure Managed Disks のサーバー側暗号化](../../virtual-machines/disk-encryption.md)を構成することを検討してください。

## <a name="tool-appears-disabled"></a>ツールが無効に見える

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Hyper-V が Windows DSVM で動作しない

Hyper-V が最初は Windows で正しく動作しないのは、想定されている動作です。 ブート パフォーマンスのために、一部のサービスを無効にしました。 Hyper-V を有効化するには、次の手順に従います。

1. Windows DSVM で検索バーを開きます
1. 「サービス」と入力します
1. すべての Hyper-V サービスを "手動" に設定します
1. "Hyper-V 仮想マシンの管理" を "自動" に設定します

最後の画面は、次のようになります。

   ![Hyper-V の有効化](./media/workaround/hyperv-enable-dsvm.png)