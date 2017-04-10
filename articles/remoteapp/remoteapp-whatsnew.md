---
title: "Azure RemoteApp の新機能。 | Microsoft Docs"
description: "Azure RemoteApp の変更点と改善点について説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 40f1ba79-80f1-47bd-bf39-f86c03e2306a
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: e4d94d3f9736378d93e93be6645ed04ade763ca3
ms.openlocfilehash: 05487c4bbdce24ea9f31ed451026531c16ead7c4


---
# <a name="whats-new-in-azure-remoteapp"></a>Azure RemoteApp の新機能。
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Azure RemoteApp の利点の&1; つは、常に改善の努力がされていることです。 改善を行うたびに、どのような変更を加えたかをここでお知らせします。

## <a name="future-updates"></a>今後の更新
Azure RemoteApp チームが RDS ブログを毎月更新していることをご存知でしたか? Azure RemoteApp の変更点だけでなく、RDS の使用方法に関する他の情報も確認できます。 詳細については、Azure RemoteApp チームのブログ、 [Remote Desktop Services Blog](https://blogs.msdn.microsoft.com/rds/)をご覧ください。 たとえば、数週間前に、 [Azure RemoteApp と Azure AD を使用したワークロードのリフト アンド シフト](https://blogs.msdn.microsoft.com/rds/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services/)に関するエントリが投稿されました。

## <a name="september-2015"></a>2015 年 9 月
* Microsoft Office 365 テンプレートとギャラリー イメージの InfoPath を追加しました。 InfoPath を共有する場合、コレクションを最新のイメージで更新します。
* クライアント更新:
  * Windows クライアントが更新され、フィードバック (特に、接続問題に関する) を共有できるようになりました。
  * iOS クライアントが更新され、エラー メッセージングが修正され、資格情報の有効期限が予想より早く切れる問題が修正されました。
* 現在、Office 2016 サポートのテストに取り組んでいます。 完了したら、更新された画像を探してください。
* [クラウド コレクションとハイブリッド コレクションの違い](remoteapp-collections.md)に関する新しい記事を発行しました。お使いのアプリに最適なコレクション タイプ (クラウド専用、クラウド + VNET、ハイブリッド) を選択する際に役立ちます。
* Azure RemoteApp で QuickBooks を共有したいのに手順がわかりませんか。 [エリックの新しい記事](remoteapp-quickbooks.md)を参考にしてください。必要なことが書いてあります。

## <a name="august-2015"></a>2015 年 8 月
8 月に大きな変化がありました。ここに概要を記載します。

* クラウド コレクションで Azure VNET を使えるようになりました。 新しい手順については、 [クラウドの作成方法](remoteapp-create-cloud-deployment.md) を参考にしてください。
* Windows RemoteApp クライアントの **[スタート] **メニューをアプリに追加できるようになりました。 アプリがアプリケーションの一覧に表示されるので、それを Windows の **[スタート] **メニューに固定できます。
* Azure VM ギャラリーに新しいイメージが追加されました。「Windows Server Remote Desktop Session Host with Microsoft Office 365 ProPlus」です。
* Mac クライアントが修正され、モーダル ウィンドウのアプリのフリーズを解消しました。
* Azure RemoteApp で [Office 365 ProPlus サブスクリプション](remoteapp-officesubscription.md) を使用する方法を文書にしました。
* Azure RemoteApp コレクションの [アプリとデータを守る](remoteapp-secure.md) 方法を詳述しました。

## <a name="july-2015"></a>2015 年 7 月
7 月は&8; 月に予定されている変更の準備をしているため、現時点でお知らせはほとんどなく、大半はドキュメントの更新です。 最新の変更内容を次に示します。

* ポータルに **[サポート]** タブが追加されました。これにより、フォーラムなどのサポート リソースにアクセスしやすくなりました。
* ハイブリッド コレクションの作成に関するトラブルシューティング情報が修正されました。 VNET 用に構成するための適切なポートを特定する方法などのトラブルシューティングのヒントについては、 [最新情報](remoteapp-hybridtrouble.md) をご覧ください。
* Azure RemoteApp で [ユーザー データ](remoteapp-upd.md) を作成および保存する方法についてのドキュメントを作成しました。
* [アプリをロックダウンする](remoteapp-secure.md)方法に関するドキュメントが作成されました。
* [Azure RemoteApp のコマンドレット](https://msdn.microsoft.com/library/mt428031.aspx)が公開されました。
* 最後に、Microsoft は、一部の Azure RemoteApp ユーザーと用語についての話し合いを開始しました。 さまざまなコレクションのオプションの表現方法を変更することを検討しています。

## <a name="june-2015"></a>2015 年 6 月
多くの機能が変更されました。 6 月、チームは非常に忙しい日々を過ごしました。

* Azure RemoteApp [ランディング ページ](https://www.remoteapp.windowsazure.com/)が変更されました。チェックしてください。
* サブスクリプションの一部として利用できるすべてのイメージでソフトウェアを更新しました。
* ハイブリッド コレクションを改善しました。強制トンネリング対応となり、コレクションの作成前に IP サブネット サイズを確認します。
* Web カメラの場合、* ワイルドカードが適切に機能しないことが判明しました。 代わりに、インスタンス ID または GUID を指定する必要があります。 リダイレクト情報を更新し、これを反映する予定です。
* それにより、Azure ギャラリーからテンプレート イメージを作成するとき、イメージにカスタムのウイルス対策ソフトウェアを追加できます。

7 月にさらに多くの変更点をロールアウトします。近いうちに新しい更新で戻ってきます。

## <a name="may-2015"></a>2015 年 5 月
最初にこのトピックを作ってから長い時間が経過し、さまざまな機能が追加されました。そのため、このリストは少し異なり、3 月の初めから&5; 月の終わりまでを対象にしています。 新しい機能:

* すべてを自動化 - Azure RemoteApp に [Azure PowerShell モジュールのコマンドレット](remoteapp-tutorial-arawithpowershell.md)が与えられました。
* [Azure 仮想マシンから Azure RemoteApp イメージを作成します](remoteapp-image-on-azurevm.md)。 カスタム イメージを Azure にアップロードする作業が大幅に速くなりました。
* RemoteApp VNET ではなく Azure VNET を使用し、企業ネットワーク リソースを Azure に接続します。 [ハイブリッド コレクションの指示](remoteapp-create-hybrid-deployment.md)を更新しました。段階的に Azure VNET を作成できます (その手順 1)。
* VNET とに関しては、VNET サイズの制限と制約に関する [新しい指針](remoteapp-vnetsizing.md) を確認してください。
* また、制限に関してですが、 [サービスの上限と初期値](../azure-subscription-service-limits.md)とは何でしょうか。

Azure RemoteApp について詳細を知りたいですか。 数週間前、RemoteApp チームは大勢で Ignite に出動しました。 Eric の動画、「 [Microsoft Azure RemoteApp の管理の基礎](http://channel9.msdn.com/Events/Ignite/2015/BRK3868)」をご覧ください。

Azure RemoteApp が実際に使われている様子を確認したいですか。 「[どこからでもあらゆるデバイスであらゆるアプリを実行する](remoteapp-anyapp.md)」チュートリアルをご覧ください。ユーザーとアクセスを強要する方法が紹介されています。データベース ファイルを共有することもできます。 同じ [Office 365](remoteapp-tutorial-o365anywhere.md) をあらゆるデバイスで実行する方法に関するチュートリアルも用意しています。

お読みいただいてありがとうございました。翌月はさらに多くの更新を届けます。

### <a name="help-us-help-you"></a>サポートのサポートのお願い
記事を評価したり、下にコメントを投稿したりするだけでなく、記事自体を変更できることを知っていましたか。 説明不足ですか。 間違いがありますか。 わかりにくいことが書いてありますか。 上へスクロールし、**[GitHub で編集]** をクリックすると変更を加えることができます。その後は Microsoft で確認が行われ、承認されると変更点や改善点がこちらのページに反映されます。




<!--HONumber=Dec16_HO2-->


