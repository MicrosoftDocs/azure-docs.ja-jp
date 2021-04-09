---
title: Microsoft Storage Explorer を使用してコンテナーと BLOB の Shared Access Signature (SAS) トークンを作成する
description: Microsoft Storage Explorer と Azure portal を使用してコンテナーと BLOB の Shared Access Token (SAS) を作成する方法
ms.topic: how-to
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 03/05/2021
ms.openlocfilehash: e40fc569ad1c8ec5894f06915422bea37cfc40ee
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102489635"
---
# <a name="create-sas-tokens-for-document-translation-processing"></a>ドキュメント翻訳処理の SAS トークンを作成する

この記事では、Azure Storage Explorer または Azure portal を使用して、Shared Access Signature (SAS) トークンを作成する方法について説明します。 SAS トークンを使用すると、Azure ストレージ アカウント内のリソースへの安全な委任アクセスが可能になります。

## <a name="create-your-sas-tokens-with-azure-storage-explorer"></a>Azure Storage Explorer を使用して SAS トークンを作成する

### <a name="prerequisites"></a>前提条件

* Windows、macOS、または Linux 開発環境に [**Azure Storage Explorer**](../../../vs-azure-tools-storage-manage-with-storage-explorer.md) アプリがインストールされている必要があります。 Azure Storage Explorer は、Azure クラウド ストレージ リソースを簡単に管理できるようにする無料ツールです。
* Azure Storage Explorer アプリがインストールされたら、ドキュメント翻訳に使用している[ストレージ アカウントに接続](../../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#connect-to-a-storage-account-or-service)します。

### <a name="create-your-tokens"></a>トークンを作成する

### <a name="sas-tokens-for-containers"></a>[コンテナーの SAS トークン](#tab/Containers)

1. ローカル コンピューターで Azure Storage Explorer アプリを開き、接続されている **ストレージ アカウント** に移動します。
1. [ストレージ アカウント] ノードを展開し、 **[BLOB コンテナー]** を選択します。
1. [BLOB コンテナー] ノードを展開し、ストレージ **コンテナー** のノードを右クリックするか、オプションのメニューを表示します。
1. オプションのメニューから **[Shared Access Signature の取得]** を選択します。
1. **[Shared Access Signature]** ウィンドウで、次の選択を行います。
    * **[アクセス ポリシー]** を選択します (既定値は [なし] です)。
    * 署名されたキーの **開始** と **有効期限** の日時を指定します。 いったん生成された SAS は取り消しができないため、有効期間を短くすることをお勧めします。
    * 開始と有効期限の日時の **タイムゾーン** を選択します (既定値は [ローカル] です)。
    * 該当するチェック ボックスをオンまたはオフにして、コンテナーの **アクセス許可** を定義します。
    * 確認し、 **[作成]** を選択します。

1. コンテナーの **コンテナー** 名、**URI**、**クエリ文字列** が新しいウィンドウに表示されます。  
1. **コンテナー、URI、クエリ文字列の値をコピーして安全な場所に貼り付けます。これらは 1 回だけ表示され、ウィンドウが閉じると取得できなくなります。**
1. SAS URL を作成するには、SAS トークン (URI) をストレージ サービスの URL に追加します。

### <a name="sas-tokens-for-blobs"></a>[BLOB の SAS トークン](#tab/blobs)

1. ローカル コンピューターで Azure Storage Explorer アプリを開き、接続されている **ストレージ アカウント** に移動します。
1. ストレージ ノードを展開し、 **[BLOB コンテナー]** を選択します。
1. [BLOB コンテナー] ノードを展開し、**コンテナー** ノードを選択して、その内容をメイン ウィンドウに表示します。
1. SAS アクセスを委任する BLOB を選択し、右クリックしてオプションのメニューを表示します。
1. オプションのメニューから **[Shared Access Signature の取得]** を選択します。
1. **[Shared Access Signature]** ウィンドウで、次の選択を行います。
    * **[アクセス ポリシー]** を選択します (既定値は [なし] です)。
    * 署名されたキーの **開始** と **有効期限** の日時を指定します。 いったん生成された SAS は取り消しができないため、有効期間を短くすることをお勧めします。
    * 開始と有効期限の日時の **タイムゾーン** を選択します (既定値は [ローカル] です)。
    * 該当するチェック ボックスをオンまたはオフにして、コンテナーの **アクセス許可** を定義します。
    * 確認し、 **[作成]** を選択します。
1. BLOB の **BLOB** 名、**URI**、**クエリ文字列** が新しいウィンドウに表示されます。  
1. **BLOB、URI、クエリ文字列の値をコピーして安全な場所に貼り付けます。これらは 1 回だけ表示され、ウィンドウが閉じると取得できなくなります。**
1. SAS URL を作成するには、SAS トークン (URI) をストレージ サービスの URL に追加します。

---

## <a name="create-sas-tokens-for-blobs-in-the-azure-portal"></a>Azure portal で BLOB の SAS トークンを作成する

> [!NOTE]
> コンテナーの SAS トークンを Azure portal で直接作成することは現在サポートされていません。 ただし、[**Azure Storage Explorer**](#create-your-sas-tokens-with-azure-storage-explorer) を使用して SAS トークンを作成するか、[プログラム](../../../storage/blobs/sas-service-create.md)でそのタスクを行うことはできます。

<!-- markdownlint-disable MD024 -->
### <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

* アクティブな [**Azure アカウント**](https://azure.microsoft.com/free/cognitive-services/)。  アカウントがない場合は、[**無料アカウントを作成**](https://azure.microsoft.com/free/)できます。
* [**Translator**](https://ms.portal.azure.com/#create/Microsoft) サービス リソース (Cognitive Services マルチサービス リソース **ではない**)。  [新しい Azure リソースの作成](../../cognitive-services-apis-create-account.md#create-a-new-azure-cognitive-services-resource)に関する記事を "*参照してください*"。  
* [**Azure Blob Storage アカウント**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 ストレージ アカウント内に BLOB データを格納して整理するためのコンテナーを作成します。

### <a name="create-your-tokens"></a>トークンを作成する

[Azure portal](https://ms.portal.azure.com/#home) にアクセスし、次のように移動します。  

 **ご自身のストレージ アカウント** → **コンテナー** → **ご自身のコンテナー** → **ご自身の BLOB**

1. ページの上部付近にあるメニューから **[SAS の生成]** を選択します。

1. **[署名方法]** → **[ユーザーの委任キー]** を選択します。

1. 該当するチェック ボックスをオンまたはオフにして、**アクセス許可** を定義します。

1. 署名されたキーの **開始** と **有効期限** の時刻を指定します。

1. **[使用できる IP アドレス]** フィールドは省略可能であり、要求を受け入れる IP アドレスまたは IP アドレスの範囲を指定します。 要求の IP アドレスが SAS トークンで指定された IP アドレスまたはアドレス範囲と一致しない場合は、承認されません。

1. **[許可されるプロトコル]** フィールドは省略可能であり、SAS を使用して行われた要求で許可されるプロトコルを指定します。 既定値は HTTPS です。

1. 確認してから、 **[SAS トークンおよび URL を生成]** を選択します。

1. **BLOB SAS トークン** のクエリ文字列と **BLOB SAS URL** が、ウィンドウの下部に表示されます。  

1. **BLOB SAS トークンと URL の値をコピーして安全な場所に貼り付けます。これらは 1 回だけ表示され、ウィンドウが閉じると取得できなくなります。**

1. SAS URL を作成するには、SAS トークン (URI) をストレージ サービスの URL に追加します。

## <a name="learn-more"></a>詳細情報

* [プログラムを使用して BLOB またはコンテナーの SAS トークンを作成する](../../../storage/blobs/sas-service-create.md)
* [ディレクトリ、コンテナー、または BLOB のアクセス許可](/rest/api/storageservices/create-service-sas#permissions-for-a-directory-container-or-blob)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ドキュメント翻訳を使ってみる](get-started-with-document-translation.md)
>
>