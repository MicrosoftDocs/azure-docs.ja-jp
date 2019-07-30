---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a24300958c27daaaf49cc3045a5e99d77c938ab7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704211"
---
コンテナーへのクエリは、`<ApiKey>` に使用される Azure リソースの価格レベルで課金されます。

Azure Cognitive Services コンテナーは、計測のために課金エンドポイントに接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に課金エンドポイントに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーが、お客様のデータ (解析対象の画像やテキストなど) を Microsoft に送信することはありません。 

### <a name="connect-to-azure"></a>Azure への接続

コンテナーには、実行する課金引数の値が必要です。 これらの値により、コンテナーは課金エンドポイントに接続することができます。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。 許可された時間枠内でコンテナーが Azure に接続しなかった場合、コンテナーは引き続き実行されますが、課金エンドポイントが復元されるまでクエリには対応しません。 接続は、10 ～15 分の同じ時間間隔で、10 回試行されます。 10 回以内に課金エンドポイントに接続できなかった場合は、コンテナーの実行が停止されます。 

### <a name="billing-arguments"></a>課金引数

`docker run` コマンドでコンテナーを起動するには、次の 3 つのオプションすべてに有効な値を指定する必要があります。

| オプション | 説明 |
|--------|-------------|
| `ApiKey` | 課金情報を追跡するために使用される Cognitive Services リソースの API キー。<br/>このオプションの値には、`Billing` に指定されたプロビジョニング済みのリソースの API キーが設定されている必要があります。 |
| `Billing` | 課金情報を追跡するために使用される Cognitive Services リソースのエンドポイント。<br/>このオプションの値には、プロビジョニング済みの Azure リソースのエンドポイント URI が設定されている必要があります。|
| `Eula` | お客様がコンテナーのライセンスに同意したことを示します。<br/>このオプションの値は **accept** に設定する必要があります。 |


