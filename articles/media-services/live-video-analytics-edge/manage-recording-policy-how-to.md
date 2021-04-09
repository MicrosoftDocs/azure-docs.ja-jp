---
title: 記録ポリシーの管理 - Azure
description: このトピックでは、記録ポリシーを管理する方法について説明します。
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: ec72f28496c1392b9d95134c343e1892998a0c28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99224991"
---
# <a name="manage-recording-policy"></a>記録ポリシーの管理

Live Video Analytics on IoT Edge を使用して、クラウドにビデオを数週間から数か月にわたって録画する[継続的なビデオ記録](continuous-video-recording-concept.md)を行うことができます。 Azure ストレージに組み込まれている[ライフサイクル管理ツール](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)を使用して、そのクラウド アーカイブの長さ (日数) を管理できます。  

お使いの Media Services アカウントを Azure Storage アカウントにリンクしして、ビデオをクラウドに録画すると、コンテンツは Media Service [アセット](../latest/assets-concept.md)に書き込まれます。 各アセットは、ストレージ アカウント内のコンテナーにマップされます。 ライフサイクル管理を使用すると、ストレージ アカウントの[ポリシー](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy)を定義できます。ここで、次のような[ルール](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules)を指定できます。

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

上記のルールは:

* ストレージ アカウントのすべてのブロック BLOB に適用されます。
* BLOB の期間が 30 日を超えたら、[ホット アクセス層からクール アクセス層](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)に移動することを指定します。
* また、期間が 90 日を超えると、その BLOB は削除されます。

Live Video Analytics を使用してアセットに記録する場合は、`segmentLength` プロパティを指定して、クラウドに書き込まれるまでのビデオの最小期間 (秒単位) を集計するようにモジュールに指示します。 アセットには一連のセグメントが含まれ、各セグメントの作成タイムスタンプは前のものより `segmentLength` だけ新しくなります。 ライフサイクル管理ポリシーが開始されると、指定したしきい値よりも古いセグメントは削除されます。 ただし、引き続き Media Service API を使用して、残りのセグメントにアクセスして再生することができます。 詳細については、「[記録の再生](playback-recordings-how-to.md)」を参照してください。 

## <a name="limitations"></a>制限事項

次に、ライフサイクル管理に関する既知の制限事項をいくつか示します。

* ポリシー内には最大で 100 のルールを設定でき、ルールごとに最大 10 個のコンテナーを指定できます。 したがって、異なる記録ポリシーを持つ必要がある場合 (たとえば、駐車場に面したカメラには 3 日間のアーカイブ、発送センターのカメラには 30 日間、レジ カウンターの背後にあるカメラには 180 日間)、1 つの Media Service アカウントを使用して、最大 1,000 台のカメラのルールをカスタマイズできます。
* ライフサイクル管理ポリシーの更新はすぐには行われません。 詳細については、[この FAQ セクション](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq)を参照してください。
* BLOB をクール層に移動するポリシーを適用する場合は、アーカイブのその部分の再生が影響を受ける可能性があります。 追加の待機時間、または散発的なエラーが表示されることがあります。 Media Services は、アーカイブ層のコンテンツの再生をサポートしていません。

## <a name="next-steps"></a>次のステップ

[記録の再生](playback-recordings-how-to.md)
