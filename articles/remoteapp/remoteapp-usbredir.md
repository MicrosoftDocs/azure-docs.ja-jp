---
title: "Azure RemoteApp で USB デバイスをリダイレクトする方法 | Microsoft Docs"
description: "Azure RemoteApp で USB デバイスのリダイレクトを使用する方法について説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 191d98af-2f5a-4307-9042-aae0e4049f9f
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 937ebbbb7daf573d072697ee09ab7f6557d0184f


---
# <a name="how-do-you-redirect-usb-devices-in-azure-remoteapp"></a>Azure RemoteApp で USB デバイスをリダイレクトする方法
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

デバイス リダイレクトを使用すると、ユーザーは、コンピューターやタブレットに接続された USB デバイスを Azure RemoteApp のアプリで利用できます。 たとえば、Azure RemoteApp を利用して Skype を共有した場合、ユーザーはデバイスのカメラを使用できるようにする必要があります。

先へ進む前に、「[Azure RemoteApp でリダイレクトを使用する](remoteapp-redirection.md)」で USB リダイレクトに関する情報を必ず確認してください。 ただし、推奨されている nusbdevicestoredirect:s:* は USB Web カメラには使用できません。また、一部の USB プリンターや USB 多機能デバイスにも使用できない場合があります。 仕様やセキュリティ上の理由により、Azure RemoteApp の管理者は、デバイス クラス GUID またはデバイス インスタンス ID を使用してリダイレクトを有効にしないと、ユーザーがこれらのデバイスを使用できません。

この記事では、Web カメラのリダイレクトについて説明しますが、 **nusbdevicestoredirect:s:*** コマンドでリダイレクトされない USB プリンターやその他の USB 多機能デバイスをリダイレクトする場合にも同様の方法を使用できます。

## <a name="redirection-options-for-usb-devices"></a>USB デバイスのリダイレクト オプション
Azure RemoteApp では、USB デバイスをリダイレクトするために、リモート デスクトップ サービスで使用できるものと非常によく似たメカニズムが使用されています。 基盤となるテクノロジにより、 **nusbdevicestoredirect:s:** コマンドを使用した高度な RemoteFX USB デバイスのリダイレクトよりも優れた、特定のデバイスに適切なリダイレクト方法を選ぶことができます。 このコマンドに対する要素は 4 つあります。

| 処理の順序 | パラメーター | Description |
| --- | --- | --- |
| 1 |* |高度なリダイレクトで取得されないすべてのデバイスを選択します。 注: 仕様により、* は USB Web カメラには使用できません。 |
| {Device class GUID} |指定されたデバイス セットアップ クラスと一致するすべてのデバイスを選択します。 | |
| USB\InstanceID |特定のインスタンス ID に指定された USB デバイスを選択します。 | |
| 2 |-USB\Instance ID |指定されたデバイスのリダイレクト設定を削除します。 |

## <a name="redirecting-a-usb-device-by-using-the-device-class-guid"></a>デバイス クラス GUID を使用した USB デバイスのリダイレクト
リダイレクトに使用できるデバイス クラス GUID を検索する方法は 2 とおりあります。 

最初のオプションでは、 [ベンダーが使用できる、システム定義のデバイス セットアップ クラス](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx)を使用します。 ローカル コンピューターに接続されているデバイスに対して最も適合性が高いクラスを選択します。 デジタル カメラの場合、これは、イメージング デバイス クラスかキャプチャ デバイス クラスになります。 ローカルに接続された USB デバイス (この場合は Web カメラ) で動作する適切なクラス GUID を見つけるには、デバイス クラスでいくつかの実験を行う必要があります。

さらに優れた方法、つまり 2 番目のオプションでは、次の手順に従って、特定のデバイス クラス GUID を見つけます。

1. デバイス マネージャーを開き、リダイレクトするデバイスを探して右クリックし、プロパティを開きます。
   ![Open the Device Manager](./media/remoteapp-usbredir/ra-devicemanager.png)
2. **[詳細]** タブで、**[クラス GUID]** プロパティを選択します。 表示された値は、その種類のデバイスのクラス GUID です。
   ![Camera properties](./media/remoteapp-usbredir/ra-classguid.png)
3. このクラス GUID 値を使用して、それに対応するデバイスをリダイレクトします。

次に例を示します。

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s:<Class Guid value>"

複数のデバイス リダイレクトを 1 つのコマンドレットにまとめることができます。 たとえば、ローカル ストレージと USB Web カメラをリダイレクトする場合、コマンドレットは次のようになります。

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:<Class Guid value>"

クラス GUID でデバイス リダイレクトを設定すると、指定されたコレクション内でそのクラス GUID に対応するすべてのデバイスがリダイレクトされます。 たとえば、ローカル ネットワーク上の複数のコンピューターに同じ USB Web カメラが搭載されている場合、1 つのコマンドレットを実行して、すべての Web カメラをリダイレクトすることができます。

## <a name="redirecting-a-usb-device-by-using-the-device-instance-id"></a>デバイス インスタンス ID を使用した USB デバイスのリダイレクト
より細かく制御する場合やデバイスごとにリダイレクトを制御する場合は、**USB\InstanceID** リダイレクト パラメーターを使用できます。

この方法の最も難しい部分は、USB デバイス インスタンス ID を見つけるところです。 コンピューターと特定の USB デバイスへのアクセス権が必要になります。 その後、次の手順に従います。

1. 「[デバイスやリソースをリモート デスクトップ セッションで使用する方法](http://windows.microsoft.com/en-us/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session)」の説明に従い、リモート デスクトップ セッションでデバイス リダイレクトを有効にします。
2. リモート デスクトップ接続を開き、 **[オプションの表示]**をクリックします。
3. **[名前を付けて保存]** をクリックし、現在の接続設定を RDP ファイルに保存します。  
    ![Save the settings as an RDP file](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. ファイル名と場所 (たとえば、"MyConnection.rdp" と "This PC\Documents") を選択し、ファイルを保存します。
5. テキスト エディターで MyConnection.rdp ファイルを開き、リダイレクトするデバイスのインスタンス ID を検索します。

ここで、見つかったインスタンス ID を次のコマンドレットで使用します。

    Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s: USB\<Device InstanceID value>"



### <a name="help-us-help-you"></a>サポートのお願い
記事を評価したり、下にコメントを投稿したりするだけでなく、記事自体を変更できることを知っていましたか。 説明不足ですか。 間違いがありますか。 わかりにくいことが書いてありますか。 上へスクロールし、**[GitHub で編集]** をクリックすると変更を加えることができます。その後は Microsoft で確認が行われ、承認されると変更点や改善点がこちらのページに反映されます。




<!--HONumber=Dec16_HO2-->


