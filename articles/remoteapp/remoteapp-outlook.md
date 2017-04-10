---
title: "Azure RemoteApp で Outlook を使用する | Microsoft Docs"
description: "Azure RemoteApp で Outlook を構成し、使用する方法について説明する | Microsoft Azure"
services: remoteapp
documentationcenter: 
author: pavithir
manager: mbaldwin
ms.assetid: cb2a498f-9539-4522-a874-542114926a61
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 300ee477a00cdd463a2abf87dd73550777fd9e05
ms.openlocfilehash: 941cc40c3c1979609649eda42e4b39aa383c3fd2


---
# <a name="using-microsoft-outlook-in-azure-remoteapp"></a>Azure RemoteApp で Microsoft Outlook を使用する
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Azure RemoteApp は Microsoft Outlook O365 をサポートします。 [Azure RemoteApp における Office の動作](remoteapp-officesubscription.md)について参照してください。 Azure RemoteApp で使用するとき、Outlook にはいくつかの推奨設定があります。

## <a name="cached-mode"></a>キャッシュ モード
キャッシュ モードは、Azure RemoteApp で Outlook を使用するときの推奨構成です。 Exchange キャッシュ モードを使用するように Outlook 2013 を構成すると、オフライン アドレス帳 (OAB) と共に、ユーザーのコンピューターのオフライン データ ファイルに保存されているユーザーの Microsoft Exchange メールボックスのローカル コピーから Outlook 2013 は機能します。 キャッシュ メールボックスと OAB は O365 サービスから定期的に更新されます。 [キャッシュ モードとオンライン モードの違い](https://technet.microsoft.com/library/jj683103.aspx)について参照してください。

ユーザーは、アカウントの設定または変更時に **Exchange キャッシュ モード**か**オンライン モード**を選択できます。 Office カスタマイズ ツール (OCT) またはグループ ポリシーを利用し、いずれかのモードをデプロイすることもできます。  

[キャッシュ モードを有効にするための段階的指示](https://technet.microsoft.com/library/c6f4cad9-c918-420e-bab3-8b49e1885034#proc)を参照してください。

## <a name="search"></a>Search
Azure RemoteApp では、Outlook 内での検索利用に制限があります。 Azure RemoteApp はプールされた VM を利用し、ユーザー セッションに対応します。 検索インデックス作成はマシン ID に基づきます。このマシン ID は VM ごとに異なります。 Azure RemoteApp にログインするたびに、ユーザーを新しい VM に向けることができます。 つまり、ローカル検索を有効にすると、マシン ID が変わるたびに (ユーザーが別の VM にいるとき) インデクサーが実行されます。 .OST ファイルのサイズによっては、インデクサーが完了し、他のアプリに必要なリソースを使い果たすまで時間がかかることがあります。 検索が遅くなるだけでなく、結果が得られないこともあります。 オンライン モード アカウント プロファイルを使用すればこれを回避できますが、ローカル キャッシュが存在しないために全体的なパフォーマンスが低下する可能性があります (キャッシュ モードとオンライン モードの違いについては、前述のリンクを参照してください)。 残念ながら、Outlook 2013 では、インデックス付きまたはローカルの検索は無効にできず、オンライン検索は既定で有効にできません。




<!--HONumber=Dec16_HO1-->


