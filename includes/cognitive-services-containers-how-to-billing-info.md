---
author: aahill
ms.author: aahi
ms.date: 03/02/2021
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d61813e723992f4381c5ea82121da8bbb70016dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102032936"
---
コンテナーへのクエリは、`ApiKey` に使用される Azure リソースの価格レベルで課金されます。

Azure Cognitive Services コンテナーは、計測または課金エンドポイントに接続していないと、実行のライセンスが許可されません。 お客様は、コンテナーが常に課金エンドポイントに課金情報を伝えられるようにする必要があります。 Cognitive Services コンテナーによって、お客様のデータ (解析対象の画像やテキストなど) が Microsoft に送信されることはありません。

### <a name="connect-to-azure"></a>Azure に接続する

コンテナーには、実行する課金引数の値が必要です。 これらの値により、コンテナーは課金エンドポイントに接続することができます。 コンテナーから、約 10 ～ 15 分ごとに使用状況が報告されます。 許可された時間枠内でコンテナーが Azure に接続しなかった場合、コンテナーは引き続き実行されますが、課金エンドポイントが復元されるまでクエリには対応しません。 接続は、10 ～15 分の同じ時間間隔で、10 回試行されます。 10 回以内に課金エンドポイントに接続できなかった場合、コンテナーによる要求の処理は停止されます。 課金のために Microsoft に送信される情報の例については、[Cognitive Services コンテナーについてよく寄せられる質問](../articles/cognitive-services/containers/container-faq.yml#how-does-billing-work)を参照してください。

### <a name="billing-arguments"></a>課金引数

<a href="https://docs.docker.com/engine/reference/commandline/run/" target="_blank">`docker run` <span class="docon docon-navigate-external x-hidden-focus"></span></a> コマンドは、次の 3 つのオプションのすべてに有効な値が指定された場合にコンテナーを起動します。

| オプション | 説明 |
|--------|-------------|
| `ApiKey` | 課金情報を追跡するために使用される Cognitive Services リソースの API キー。<br/>このオプションの値には、`Billing` に指定されたプロビジョニング済みのリソースの API キーが設定されている必要があります。 |
| `Billing` | 課金情報を追跡するために使用される Cognitive Services リソースのエンドポイント。<br/>このオプションの値には、プロビジョニング済みの Azure リソースのエンドポイント URI が設定されている必要があります。|
| `Eula` | お客様がコンテナーのライセンスに同意したことを示します。<br/>このオプションの値は **accept** に設定する必要があります。 |
