---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/25/2019
ms.author: alkohli
ms.openlocfilehash: 1d52117440028c75b249f469f2b3576c2ab1c5c5
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/05/2019
ms.locfileid: "67181839"
---
最後に、発送するデバイスを準備します。 このステップでは、デバイスのすべての共有がオフラインにされます。 いったんこのプロセスを始めると、共有にアクセスできなくなります。

> [!IMPORTANT]
> 発送準備は必須です。Azure の名前付け規則に準拠していないデータにはフラグが設定されます。 この手順をスキップすると、データが規則に準拠していないために、データのアップロードに失敗するおそれがあります。

1. **[発送準備]** に移動し、 **[準備の開始]** をクリックします。 既定では、データのコピー中にチェックサムが計算されます。 発送準備によりチェックサムの計算が完了し、ファイルの一覧 ( *-BOM ファイル*) が作成されます。 チェックサムの計算は、データのサイズによっては数時間から数日間かかる場合があります。 
   
    ![発送の準備をする 1](media/data-box-prepare-to-ship/prepare-to-ship1.png)

    なんらかの理由でデバイスの準備を中止したい場合は、 **[準備の停止]** をクリックします。 発送準備は後から再開できます。
        
    ![発送の準備をする 2](media/data-box-prepare-to-ship/prepare-to-ship2.png)
    
2. 発送準備が開始され、デバイスの共有がオフラインになります。 デバイスの準備が完了すると、配送先住所ラベルをダウンロードするためのリマインダーが表示されます。

    ![配送先住所ラベルのリマインダーをダウンロードしてください](media/data-box-prepare-to-ship/download-shipping-label-reminder.png)

3. デバイスの準備が完了すると、デバイスの状態が "*発送する準備ができました*" に更新され、デバイスがロックされます。
        
    ![発送の準備をする 3](media/data-box-prepare-to-ship/prepare-to-ship3.png)

    デバイスにコピーしたいデータが他にもある場合は、デバイスのロックを解除して、それらをコピーしてから、もう一度、発送準備を実行します。

    この手順でエラーが発生した場合は、エラー ログをダウンロードしてエラーを解決する必要があります。 エラーが解決されたら、 **[発送準備]** を実行します。

4. 発送準備が正常に (エラーが発生することなく) 完了したら、この手順でコピーしたファイルのリスト (マニフェスト) をダウンロードします。 後でこのリストを使用して、Azure にアップロードされたファイルを確認できます。 詳細については、「[Inspect BOM files during Prepare to ship (発送準備中に BOM ファイルを検査する)](../articles/databox/data-box-logs.md#inspect-bom-during-prepare-to-ship)」をご覧ください。
        
    ![発送の準備をする 1](media/data-box-prepare-to-ship/prepare-to-ship4.png)

5. デバイスをシャットダウンします。 **[シャットダウンまたは再起動]** ページに移動し、 **[シャットダウン]** をクリックします。 確認を求められたら、 **[OK]** をクリックして続行します。

6. ケーブルを取り外します。 次の手順では、Microsoft にデバイスを発送します。
