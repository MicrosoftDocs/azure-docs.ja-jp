---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: 3cd5f59a3dcf3afcd26d16874e7dc77ca0a7e844
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841684"
---
### <a name="install-via-composer"></a>Composer 経由でインストールする
1. プロジェクトのルートに **composer.json** という名前のファイルを作成して、次のコードを追加します。
   
    ```json
    {
      "require": {
        "microsoft/azure-storage": "*"
      }
    }
    ```
2. **[composer.phar][composer-phar]** をプロジェクトのルートにダウンロードします。
3. コマンド プロンプトを開き、次のコマンドをプロジェクトのルートで実行します。
   
    ```
    php composer.phar install
    ```

または、GitHub で [Azure Storage PHP Client Library][php-sdk-github] に移動し、ソース コードを複製します。

[php-sdk-github]: https://github.com/Azure/azure-storage-php
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: https://github.com/Azure/azure-sdk-for-php
[composer-phar]: http://getcomposer.org/composer.phar
