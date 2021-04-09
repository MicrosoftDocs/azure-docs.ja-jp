---
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 12/30/2019
ms.author: kgremban
ms.openlocfilehash: 8cae7ac2d3b961120cb9100f5072dc141769afff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95557530"
---
## <a name="create-a-container-registry"></a>コンテナー レジストリの作成

このチュートリアルでは、Azure IoT Tools の拡張機能を使用してモジュールをビルドし、ファイルから **コンテナー イメージ** を作成します。 その後、このイメージを **レジストリ** にプッシュし、格納および管理します。 最後に、レジストリからイメージを展開し、IoT Edge デバイスで実行します。

コンテナー イメージは、Docker と互換性のある任意のレジストリを使用して格納できます。 2 つの一般的な Docker レジストリ サービスは、[Azure Container Registry](../articles/container-registry/index.yml) と [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags) です。 このチュートリアルでは、Azure Container Registry を使用します。

コンテナー レジストリがまだない場合は、次の手順に従って、Azure に新しいコンテナー レジストリを作成します。

1. [Azure portal](https://portal.azure.com) で、 **[リソースの作成]**  >  **[コンテナー]**  >  **[コンテナー レジストリ]** の順に選択します。

2. コンテナー レジストリを作成するには、以下の値を指定します。

   | フィールド | 値 |
   | ----- | ----- |
   | サブスクリプション | ドロップダウン リストで、サブスクリプションを選択します。 |
   | Resource group | IoT Edge のクイック スタートおよびチュートリアルで作成するすべてのテスト リソースに、同じリソース グループを使用することをお勧めします。 たとえば、**IoTEdgeResources** を使用します。 |
   | レジストリ名 | 一意の名前を指定します。 |
   | Location | 近くの場所を選択します。 |
   | SKU | **[Basic]** を選択します。 |

3. **［作成］** を選択します

4. コンテナー レジストリが作成された後、それを参照し、左側のペインの **[設定]** の下にあるメニューから **[アクセス キー]** を選択します。 

5. クリックして管理者ユーザーを有効にし、コンテナー レジストリの **ユーザー名** と **パスワード** を表示します。

6. **ログイン サーバー**、**ユーザー名**、および **パスワード** の値をコピーして、どこか都合のいい場所に保存します。 このチュートリアルではこれらの値を使用して、コンテナー レジストリへのアクセスを提供します。

   ![コンテナー レジストリのログイン サーバー、ユーザー名、パスワードをコピーします。](./media/iot-edge-create-container-registry/registry-access-key.png)