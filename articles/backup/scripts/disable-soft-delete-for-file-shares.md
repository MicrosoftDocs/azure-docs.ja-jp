---
title: サンプル スクリプト - ファイル共有に対して論理的な削除を無効にする
description: ストレージ アカウント内のファイル共有に対して論理的な削除を無効にするスクリプトの使用方法について説明します。
ms.topic: sample
ms.date: 02/02/2020
ms.openlocfilehash: 172a7bd19ffd07ca7900a457c447362343e5f185
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84121285"
---
# <a name="disable-soft-delete-for-file-shares-in-a-storage-account"></a>ストレージ アカウント内のファイル共有に対して論理的な削除を無効にする

このドキュメントでは、ストレージ アカウント内のファイル共有に対して論理的な削除を無効にするプロセスについて説明します。

次の手順に従います。

1. armclient をインストールします。 インストール方法については、[こちらのリンク](https://github.com/projectkudu/ARMClient)をクリックしてください。

2. 次の 2 つの要求本文ファイルを、ご使用のマシン上のフォルダーに保存します。

    ```json
    rqbody-enableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":true,
        "days": 14
        }
    },
    "cors": {
        "corsRules": []
    }
    }

    rqbody-disableSoftDelete.json

    {
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled":false,
        "days": 0
        }
    },
    "cors": {
        "corsRules": []
    }
    }
    ```

3. ストレージ アカウントの Azure Resource Manager (ARM) ID を手元に用意します。 例: `/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare/providers/Microsoft.Storage/storageAccounts/inquirytest`

4. **armclient login** を実行し、資格情報を使用してサインインします。

5. ストレージ アカウント内のファイル共有について、現在の論理的な削除のプロパティを取得します。

    次の GET 操作は、*inquirytest* アカウントのファイル共有に対する論理的な削除のプロパティをフェッチします。

    ```cmd
    armclient get /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01
    ```

    ```output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "cors": {
        "corsRules": []
        },
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```

6. ストレージ アカウント内のファイル共有に対して論理的な削除を無効にします。

    次の PUT 操作は、*inquirytest* アカウントのファイル共有に対する論理的な削除のプロパティを無効にします。

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-disableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/de
    fault",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": false,
        "days": 0
        }
    }
    }
    ```

7. 論理的な削除を再度有効にする場合は、次のサンプルを使用します。

    次の PUT 操作は、"inquirytest" アカウントのファイル共有に対する論理的な削除のプロパティを有効にします。

    ```cmd
    armclient put /subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/afsshare /providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default?api-version=2019-04-01 .\rqbody-EnableSoftDelete.json
    ```

    ```Output
    {
    "id": "/subscriptions/37aa2d43-d4f5-4322-bae0-6ee11c627f50/resourceGroups/Bugbash/providers/Microsoft.Storage/storageAccounts/inquirytest/fileServices/default",
    "name": "default",
    "type": "Microsoft.Storage/storageAccounts/fileServices",
    "properties": {
        "shareDeleteRetentionPolicy": {
        "enabled": true,
        "days": 14
        }
    }
    }
    ```
