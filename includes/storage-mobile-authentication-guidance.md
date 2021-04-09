---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: eeea7b1fed0c2a1f805e21b4dec9ec3cad7fc976
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996818"
---
## <a name="configure-your-application-to-access-azure-storage"></a>Azure Storage にアクセスするようにアプリケーションを構成する
Storage サービスにアクセスできるようにアプリケーションを認証するには、次の 2 つの方法があります。

* 共有キー:テスト目的のみに共有キーを使用します
* Shared Access Signature (SAS):運用アプリケーション用に SAS を使用します

### <a name="shared-key"></a>共有キー
共有キー認証の場合、アプリケーションは Storage サービスへのアクセスにアカウント名とアカウント キーを使用します。 このライブラリを使用する方法を簡単に説明するため、ここでは共有キー認証を使用します。

> [!WARNING] 
> **共有キー認証はテスト目的にのみ使用します。** アカウント名とアカウント キーは、関連付けられているストレージ アカウントへの完全な読み取りおよび書き込みアクセスが付与されており、アプリをダウンロードするすべてのユーザーに配布されます。 これは信頼できないクライアントによってキーが侵害される危険があるため、 **お勧めしません** 。
> 
> 

共有キー認証を使用する場合は、 [接続文字列](../articles/storage/common/storage-configure-connection-string.md)を作成します。 接続文字列の構成要素は次のとおりです。  

* **DefaultEndpointsProtocol** - HTTP または HTTPS を選択できますが、 HTTPS の使用を強くお勧めします。
* **Account Name** - ストレージ アカウントの名前
* **Account Key** - [Azure Portal](https://portal.azure.com) でお使いのストレージ アカウントに移動し、 **[キー]** アイコンをクリックして確認します。
* (省略可能) **EndpointSuffix** - Azure China、Azure Governance など、別のエンドポイント サフィックスを持つリージョンのストレージ サービスに対して使用されます。

共有キー認証を使用する接続文字列の例を次に示します。

`"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here"`

### <a name="shared-access-signatures-sas"></a>Shared Access Signatures (SAS)
モバイル アプリケーションの場合、Azure Storage サービスに対するクライアントからの要求を認証する際には、Shared Access Signature (SAS) を使用することをお勧めします。 SAS を使用すると、期間およびアクセス許可セットを指定して、リソースへのクライアント アクセスを許可することができます。
ストレージ アカウント所有者は、モバイル クライアントが使用する SAS を生成する必要があります。 SAS を生成するには、クライアントに配布する SAS を生成する別のサービスを記述することが必要になります。 テスト目的で、[Microsoft Azure ストレージ エクスプローラー](https://storageexplorer.com)または [Azure Portal](https://portal.azure.com) を使用して SAS を生成できます。 SAS の作成時には、SAS の有効期間と、SAS がクライアントに付与するアクセス許可を指定できます。

次に示すのは、Microsoft Azure ストレージ エクスプローラーを使用して SAS を生成する方法の例です。

1. まだインストールしていない場合は、 [Microsoft Azure ストレージ エクスプローラーをインストール](https://storageexplorer.com)
2. サブスクリプションに接続します。
3. ストレージ アカウントをクリックし、左下の [アクション] タブをクリックします。 [Get Shared Access Signature] \(Shared Access Signature の取得) をクリックすると、SAS の "接続文字列" が生成されます。
4. 次に示すのは、ストレージ アカウントの BLOB サービスに対するサービス、コンテナー、およびオブジェクト レベルの読み取りおよび書き込みのアクセス許可を付与する SAS 接続文字列の例です。
   
   `"SharedAccessSignature=sv=2015-04-05&ss=b&srt=sco&sp=rw&se=2016-07-21T18%3A00%3A00Z&sig=3ABdLOJZosCp0o491T%2BqZGKIhafF1nlM3MzESDDD3Gg%3D;BlobEndpoint=https://youraccount.blob.core.windows.net"`

ご覧のように、SAS を使用する場合、アプリケーションでアカウント キーを公開することはありません。 SAS の詳細および SAS 使用のベスト プラクティスについては、[Shared Access Signature:SAS モデルの説明](../articles/storage/common/storage-sas-overview.md)に関するページをご覧ください。