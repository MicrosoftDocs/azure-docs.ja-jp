---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/04/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4604616cd4f2d6c75c272586df1331fc405061cb
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70737493"
---
## <a name="error-conditionheadersnotsupported-from-a-web-application-using-azure-files-from-browser"></a>ブラウザーから Azure Files を使用する Web アプリケーションの ConditionHeadersNotSupported エラー

条件ヘッダーが使用されるアプリケーション (Web ブラウザーなど) を通して Azure Files でホストされているコンテンツにアクセスすると、アクセスが失敗し、ConditionHeadersNotSupported エラーが発生します。 このエラーは、条件ヘッダーがサポートされていないことを示します。

![Azure Files の条件ヘッダー エラー](media/storage-files-condition-headers/conditionalerror.png)

### <a name="cause"></a>原因

条件ヘッダーはまだサポートされていません。 それらを実装しているアプリケーションでは、ファイルにアクセスするたびに、ファイル全体を要求する必要があります。

### <a name="workaround"></a>対処法

新しいファイルがアップロードされるとき、cache-control プロパティは既定で "no-cache" になっています。 アプリケーションで毎回ファイルを要求するようにするには、ファイルの cache-control プロパティを "no-cache" から "no-cache, no-store, must-revalidate" に更新する必要があります。 これは、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用して実現できます。

![Azure Files の条件ヘッダーに対応するための、ストレージ エクスプローラーでのコンテンツ キャッシュの変更](media/storage-files-condition-headers/storage-explorer-cache.png)