---
title: 記録ポリシーの管理 - Azure
description: このトピックでは、記録ポリシーを管理する方法について説明します。
ms.topic: how-to
ms.date: 04/27/2020
ms.openlocfilehash: d3a1be915dc1cc8714e49cc7b2fe68bbe9cad161
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011483"
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

Live Video Analytics では、指定された時間単位でビデオがアーカイブされるため、アセットには一連の BLOB (セグメントごとに 1 つの BLOB) が含まれます。 ライフサイクル管理ポリシーが開始され、以前の BLOB が削除されると、Media Service API を使用して引き続き残りの BLOB にアクセスして再生できるようになります。 詳細については、[記録の再生](playback-recordings-how-to.md)に関するページを参照してください。 

## <a name="limitations"></a>制限事項

次に、ライフサイクル管理に関する既知の制限事項をいくつか示します。

* ポリシー内には最大で 100 のルールを設定でき、ルールごとに最大 10 個のコンテナーを指定できます。 したがって、異なる記録ポリシーを持つ必要がある場合 (たとえば、駐車場に面したカメラには 3 日間のアーカイブ、発送センターのカメラには 30 日間、レジ カウンターの背後にあるカメラには 180 日間)、1 つの Media Service アカウントを使用して、最大 1,000 台のカメラのルールをカスタマイズできます。
* ライフサイクル管理ポリシーの更新はすぐには行われません。 詳細については、[この FAQ セクション](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq)を参照してください。
* BLOB をクール層に移動するポリシーを適用する場合は、アーカイブのその部分の再生が影響を受ける可能性があります。 追加の待機時間、または散発的なエラーが表示されることがあります。 Media Services は、アーカイブ層のコンテンツの再生をサポートしていません。

## <a name="next-steps"></a>次のステップ

[記録の再生](playback-recordings-how-to.md)
