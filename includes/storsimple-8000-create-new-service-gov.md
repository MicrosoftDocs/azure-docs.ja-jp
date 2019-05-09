---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 6939553206fe223d8166368d2963f03b32d2629e
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889594"
---
#### <a name="to-create-a-new-service"></a>新しいサービスを作成するには
1. Microsoft アカウントの資格情報を使用し、[Microsoft Azure Government ポータル](https://portal.azure.us/)にサインインします。
2. Government ポータルで **+** をクリックし、Marketplace で **[すべて表示]** をクリックします。 "_StorSimple 物理_" を検索します。 **[StorSimple 物理デバイス シリーズ]** を選択してクリックし、**[作成]** をクリックします。 または、Government ポータルで **+** をクリックし、**[ストレージ]** で **[StorSimple 物理デバイス シリーズ]** をクリックします。
3. **[StorSimple デバイス マネージャー]** ブレードで、次の操作を実行します。
   
   1. サービスの一意の**リソース名**を指定します。 この名前は、サービスの識別に使用できる表示名です。 名前の長さは 2 ～ 50 文字とし、文字、数字、ハイフンを含めることができます。 名前の最初と最後は、文字か数字とする必要があります。
   2. **[サブスクリプション]** ボックスの一覧で、サブスクリプションを選択します。 サブスクリプションは、課金アカウントにリンクされます。 このフィールドは、保有するサブスクリプションが 1 つだけの場合は表示されません。
   3. **リソース グループ**は、**既存のグループを使用**するか、**新しいグループを作成**します。 詳細については、[Azure のリソース グループ](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-infrastructure-resource-groups-guidelines/)に関する記事をご覧ください。
   4. サービスの **[場所]** を指定します。 場所は、デバイスをデプロイする地理的領域です。 **[米国政府アイオワ州]** または **[米国政府バージニア州]** を選択します。
   5. **[新しいストレージ アカウントを作成する]** をオンにすると、サービスの作成時にストレージ アカウントが自動的に作成されます。 このストレージ アカウントの名前を指定します。 別の場所でデータが必要になる場合、このボックスをオフにします。
   6. ダッシュボードにこのサービスへのクイック リンクが必要な場合、**[ダッシュボードにピン留めする]** チェック ボックスをオンにします。
   7. **[作成]** をクリックして、StorSimple デバイス マネージャーを作成します。 サービスの作成には数分かかります。 サービスが正常に作成されると、通知が表示され、新しいサービス ブレードが開きます。


