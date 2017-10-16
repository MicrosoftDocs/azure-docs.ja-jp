---
title: "Azure Data Lake Store での暗号化 | Microsoft Docs"
description: "Azure Data Lake Store における暗号化とキーの交換のしくみについて説明します"
services: data-lake-store
documentationcenter: 
author: yagupta
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 4/14/2017
ms.author: yagupta
ms.openlocfilehash: 20444d368c568ee716ff242e33323b91ffd198eb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="encryption-of-data-in-azure-data-lake-store"></a>Azure Data Lake Store でのデータの暗号化

Azure Data Lake Store では、暗号化によってデータを保護し、企業のセキュリティ ポリシーを実装できるほか、規制上のコンプライアンス要件を満たすことができます。 この記事では、設計の概要を示すと共に、実装の技術的な側面について取り上げます。

Data Lake Store は、保存データと転送中のデータの両方について暗号化をサポートしています。 保存データに関しては、"既定でオン" の透過的な暗号化がサポートされます。 これらの用語の意味をここで少し詳しく説明します。

* **既定でオン**: 新しい Data Lake Store アカウントを作成する際に、既定の設定で暗号化が有効になります。 その後、Data Lake Store に格納されているデータは永続メディアに格納される前に必ず暗号化されます。 これはすべてのデータに対して実行されます。また、この動作をアカウント作成後に変更することはできません。
* **透過的**: データを保存する前の暗号化とデータを取得する前の解読は Data Lake Store によって自動的に行われます。 暗号化の構成と管理は、管理者によって Data Lake Store レベルで行われます。 データ アクセス API は変更されていません。 そのため、Data Lake Store と連携するアプリケーションやサービスに、暗号化に伴う変更を加える必要はありません。

Data Lake Store では転送中のデータ (つまり移動中のデータ) も常に暗号化されます。 データは永続メディアに格納される前に暗号化されるうえ、転送中も HTTPS を使用して常にセキュリティ保護されます。 HTTPS は、Data Lake Store REST インターフェイスでサポートされる唯一のプロトコルです。 次の図は、Data Lake Store でどのようにデータが暗号化されるかを示しています。

![Data Lake Store におけるデータ暗号化の図](./media/data-lake-store-encryption/fig1.png)


## <a name="set-up-encryption-with-data-lake-store"></a>Data Lake Store での暗号化の設定

Data Lake Store の暗号化はアカウントの作成中に設定され、常に既定で有効になります。 キーは自分で管理するか、Data Lake Store で自動的に管理することができます (既定の動作は後者)。

詳細については、[概要](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)に関するページを参照してください。

## <a name="how-encryption-works-in-data-lake-store"></a>Data Lake Store における暗号化のしくみ

以下の情報では、マスター暗号化キーの管理方法のほか、Data Lake Store のデータ暗号化に使用できる 3 種類のキーについて説明しています。

### <a name="master-encryption-keys"></a>マスター暗号化キー

Data Lake Store では、マスター暗号化キー (MEK) を管理するモードが 2 つあります。 ここでは、マスター暗号化キーがトップレベル キーであると考えてください。 Data Lake Store に格納されているデータを解読するには、マスター暗号化キーにアクセスする必要があります。

マスター暗号化キーを管理するモードには次の 2 つがあります。

*   サービス管理キー
*   顧客管理キー

どちらのモードでも、マスター暗号化キーは Azure Key Vault に格納されてセキュリティ保護されます。 Key Vault は、安全性の高い完全管理型の Azure サービスであり、暗号化キーの保護に使用できます。 詳細については、「[Key Vault](https://azure.microsoft.com/services/key-vault)」を参照してください。

次の図では、MEK を管理する 2 つのモードで使用できる機能を簡単に比較しています。

|  | サービス管理キー | 顧客管理キー |
| --- | --- | --- |
|データをどのように格納するか|格納する前に常に暗号化|格納する前に常に暗号化|
|マスター暗号化キーをどこに格納するか|Key Vault|Key Vault|
|Key Vault の外部に平文で格納される暗号化キーがあるか |いいえ|いいえ|
|Key Vault から MEK を取得できるか|いいえ。 いったん Key Vault に格納された MEK は、暗号化と解読にしか使用できません。|いいえ。 いったん Key Vault に格納された MEK は、暗号化と解読にしか使用できません。|
|Key Vault インスタンスと MEK の所有者はだれか|Data Lake Store サービス|ユーザーが Key Vault インスタンスを所有し、その Key Vault インスタンスはユーザー自身の Azure サブスクリプションに属します。 Key Vault 内の MEK は、ソフトウェアまたはハードウェアで管理できます。|
|Data Lake Store サービスの MEK へのアクセスを取り消すことができるか|いいえ|はい。 Key Vault のアクセス制御リストを管理できるほか、Data Lake Store サービスのサービス ID に対するアクセス制御エントリを削除できます。|
|MEK を完全に削除できるか|いいえ|はい。 Key Vault から MEK を削除すると、Data Lake Store サービスを含めだれも Data Lake Store アカウント内のデータを解読できなくなります。 <br><br> Key Vault から削除する前に MEK を明示的にバックアップしておけば、その MEK を復元してデータを回復することができます。 一方、MEK をバックアップせずに Key Vault から削除した場合、その後 Data Lake Store アカウント内のデータを解読することはできません。|


MEK とそれが格納される Key Vault インスタンスの管理者が 2 つのモードで異なるという点を除けば、残りの設計はどちらのモードも同じです。

マスター暗号化キーのモードを選択するうえで重要な点を次に示します。

*   顧客管理キーとサービス管理キーのどちらを使用するかは、Data Lake Store アカウントをプロビジョニングするときに選択できます。
*   Data Lake Store アカウントのプロビジョニング後にモードを変更することはできません。

### <a name="encryption-and-decryption-of-data"></a>データの暗号化と解読

データ暗号化の設計で使用されるキーの種類は 3 つあります。 次の表で簡単に説明します。

| キー                   | 省略形 | 関連付け先 | 保存先                             | 型       | メモ                                                                                                   |
|-----------------------|--------------|-----------------|----------------------------------------------|------------|---------------------------------------------------------------------------------------------------------|
| マスター暗号化キー | MEK          | Data Lake Store アカウント | Key Vault                              | 非対称 | Data Lake Store 側で管理するか、ユーザー側で管理できます。                                                              |
| データ暗号化キー   | DEK          | Data Lake Store アカウント | Data Lake Store サービスによって管理される永続的ストレージ | 対称  | DEK は MEK で暗号化されます。 その暗号化された DEK が永続メディアに格納されます。 |
| ブロック暗号化キー  | BEK          | データのブロック | なし                                         | 対称  | BEK は DEK から派生したデータ ブロックです。                                                      |

次の図にこれらの概念を示します。

![データ暗号化のキー](./media/data-lake-store-encryption/fig2.png)

#### <a name="pseudo-algorithm-when-a-file-is-to-be-decrypted"></a>ファイルが復号化される際の疑似アルゴリズム:
1.  Data Lake Store アカウントの DEK がキャッシュされていて使用できる状態であることを確認します。
    - 準備が整っていない場合、永続的なストレージから暗号化された DEK を読み取り、それを Key Vault に送信して解読します。 解読した DEK をメモリにキャッシュします。 これで使用できる状態になります。
2.  ファイル内のすべてのデータ ブロックに関して次の処理を実行します。
    - 永続的なストレージから暗号化されたデータ ブロックを読み取ります。
    - DEK と暗号化されたデータ ブロックから BEK を生成します。
    - BEK を使用してデータを解読します。


#### <a name="pseudo-algorithm-when-a-block-of-data-is-to-be-encrypted"></a>データ ブロックが暗号化される際の疑似アルゴリズム:
1.  Data Lake Store アカウントの DEK がキャッシュされていて使用できる状態であることを確認します。
    - 準備が整っていない場合、永続的なストレージから暗号化された DEK を読み取り、それを Key Vault に送信して解読します。 解読した DEK をメモリにキャッシュします。 これで使用できる状態になります。
2.  DEK からデータ ブロックの一意の BEK を生成します。
3.  AES 256 暗号化を使用して BEK でデータ ブロックを暗号化します。
4.  暗号化されたデータ ブロックを永続的なストレージに格納します。

> [!NOTE] 
> パフォーマンス上の理由から、平文の DEK は短い期間メモリにキャッシュされ、その後すぐに消去されます。 このデータ暗号化キーは常に、MEK によって暗号化された状態で永続メディアに格納されます。

## <a name="key-rotation"></a>キーの交換

顧客管理キーを使用している場合、MEK を交換することができます。 顧客管理キーを使用して Data Lake Store アカウントを設定する方法については、[概要](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)に関するページを参照してください。

### <a name="prerequisites"></a>前提条件

Data Lake Store アカウントを設定するときに、独自キーの使用を選択済みであることが必要です。 このオプションをアカウントの作成後に変更することはできません。 以下の手順は、顧客管理キーが使用されている (つまり Key Vault から独自のキーが選択されている) ことを想定しています。

暗号化で既定のオプションを使用する場合、データは常に Data Lake Store によって管理されたキーを使用して暗号化されます。 このオプションでは Data Lake Store によってキーが管理されるため、ユーザーがキーを交換することはできません。

### <a name="how-to-rotate-the-mek-in-data-lake-store"></a>Data Lake Store の MEK を交換する方法

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. ご利用の Data Lake Store アカウントに関連付けられているキーの格納場所となっている Key Vault インスタンスに移動します。 **[キー]** を選択します。

    ![Key Vault のスクリーンショット](./media/data-lake-store-encryption/keyvault.png)

3.  Data Lake Store アカウントに関連付けられているキーを選択し、このキーの新しいバージョンを作成します。 現在 Data Lake Store でサポートされるのは、新しいバージョンのキーへの交換だけです。 異なるキーへの交換はサポートされません。

   ![[キー] ウィンドウのスクリーンショット ([新しいバージョン] に注目)](./media/data-lake-store-encryption/keynewversion.png)

4.  Data Lake Store ストレージ アカウントに移動して **[暗号化]** を選択します。

    ![Data Lake Store ストレージ アカウント ウィンドウのスクリーンショット ([暗号化] に注目)](./media/data-lake-store-encryption/select-encryption.png)

5.  新しいバージョンのキーが利用可能であることを伝えるメッセージが表示されます。 **[回転キー]** をクリックすると、キーが新しいバージョンに更新されます。

    ![Data Lake Store ウィンドウのスクリーンショット (メッセージとキーの交換に注目)](./media/data-lake-store-encryption/rotatekey.png)

この処理は 2 分未満で完了します。キーの交換が原因でダウンタイムは発生しないものと思われます。 処理が完了したら、新しいバージョンのキーが使用中の状態になります。
