---
title: "Azure Mobile Engagement で個人用に設定された通知を送信する"
description: "名前などのユーザー プロファイル情報を通知に含めることで個人用に設定した通知を送信する方法"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 51007089-156a-4bac-bb1b-a590633bf2a2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: all
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
translationtype: Human Translation
ms.sourcegitcommit: 06e16033435ed0a37d5688055743875827d3aec2
ms.openlocfilehash: f27ed36eddddfab1de424574b0cf8e9ef04a162d
ms.lasthandoff: 03/01/2017


---
# <a name="personalize-notifications-by-including-user-name"></a>ユーザー名を追加し、通知を個人用に設定する
アプリ ユーザーに通知にもっと注目してもらうには、通知を個人に合わせて設定します。 たとえば、アプリ ユーザーの名前を選択し、個々のユーザーに合わせて通知をカスタマイズすると非常に効果的です。 ここで注意が必要です。この方法を使いすぎると、一部のユーザーにとって気味が悪く感じられる可能性があるため、ユーザーの名前を通知に追加する方法は慎重に利用する必要があります。 また、この方法を利用する前に、モバイル アプリで個人情報の提供に関して完全な同意を与えるかどうかをユーザーが選択できるようにします。 

技術的には、Azure Mobile Engagement を利用し、以下の手順で通知をカスタマイズします。ユーザー名を通知に追加するシナリオが利用されています。 アプリ情報またはタグの概念が利用されます。その値はモバイル アプリに統合されている SDK か API で渡されます。 これらのアプリ情報またはタグは次のように利用できます。

1. アプリ情報の値に基づき、通知の対象を特定のユーザーにするために 
2. 通知内でデバイス/ユーザーに固有の値で置換されるプレースホルダーとして (そのデバイスに通知が送信されます) 

> [!IMPORTANT]
> アプリ情報値を各通知で置換するというこの追加処理に起因し、通知の送信速度が落ちることに注意してください。 
> 
> 

## <a name="register-app-info-in-the-mobile-engagement-portal"></a>Mobile Engagement ポータルでアプリ情報を登録する
1) 最初に Azure Portal でアプリ情報またはタグを登録します。 そのために **[設定]** -> **[タグ (アプリ情報)]** に進みます。  

![][1]    

2) **[新しいタグ (アプリ情報)]** をクリックし、「*user_name*」と名付け、種類を "*文字列*" に指定し、**[送信]** をクリックします。 

![][2]

3) 最後に、この登録されたアプリ情報が次のように表示されます。

![][3]

## <a name="send-app-info-from-the-client-sdk"></a>クライアント SDK からアプリ情報を送信する
ここでは Windows ユニバーサル アプリの例を使用しますが、Microsoft の他の SDK にもこれに相当する手法があります。 

おそらくは認証後に名前などのプロファイル情報をユーザーから取得する方法がモバイル アプリにある場合、`SendAppInfo` メソッドをここで呼び出し、先に登録した `user_name` アプリ情報の値を Mobile Engagement サービス バックエンドに入力します。 

    Dictionary<object, object> appInfo = new Dictionary<object, object>();
    appInfo.Add("user_name", str);
    EngagementAgent.Instance.SendAppInfo(appInfo); 

## <a name="send-personalized-notifications"></a>個人用に設定した通知を送信する
この **user_name** を利用し、通知を送信する用意ができました。 

1) Mobile Engagement ポータルに進みます。**[リーチ]** タブで通知を作成できます。このプレースホルダーは、通知タイトルまたは本文のどこでも、次の形式で使用できます。 

![][4]    

> [!NOTE]
> user_name アプリ情報が設定されていないユーザーは通知を受け取りません。 通知キャンペーンをテスト モードで実行したときにアプリ情報を設定しなかった場合は、プレース ホルダーを置換するために'?' が送信されます。 
> 
> 

2) Mobile Engagement がこの通知を送信するデバイスを選択するとき、このアプリ情報を見て、プレースホルダーの値を置換します。  
たとえば、ユーザーに `str = "Scott"` を設定している場合、このユーザーに関して、デバイス登録が **user_name = SCOTT** というアプリ情報に関連付けられます。このユーザーにはアプリ プッシュ通知の出力が次の形式で表示されます。 

![][5]    

<!-- Images. -->
[1]: ./media/mobile-engagement-send-personalized-notifications/app-info.png
[2]: ./media/mobile-engagement-send-personalized-notifications/create-app-info.png
[3]: ./media/mobile-engagement-send-personalized-notifications/app-info-user-name.png
[4]: ./media/mobile-engagement-send-personalized-notifications/personal-notification.png
[5]: ./media/mobile-engagement-send-personalized-notifications/notification.png


