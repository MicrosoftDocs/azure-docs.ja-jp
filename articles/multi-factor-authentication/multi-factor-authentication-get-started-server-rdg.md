---
title: "RADIUS を使用した RDG と Azure MFA Server | Microsoft Docs"
description: "これは、RADIUS を使用してリモート デスクトップ (RD) ゲートウェイと Azure Multi-Factor Authentication Server をデプロイする際に役立つ Azure Multi-Factor Authentication のページです。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: f2354ac4-a3a7-48e5-a86d-84a9e5682b42
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: d0e35237b412d4c3bcc26c77d124f3ed2fef6403
ms.lasthandoff: 03/16/2017


---
# <a name="remote-desktop-gateway-and-azure-multi-factor-authentication-server-using-radius"></a>RADIUS を使用したリモート デスクトップ ゲートウェイと Multi-Factor Authentication Server
リモート デスクトップ (RD) ゲートウェイでは多くの場合、ローカル ネットワーク ポリシー サービス (NPS) を使ってユーザーを認証します。 この記事では、リモート デスクトップ ゲートウェイからの RADIUS 要求を (ローカルの NPS を通じて) Multi-Factor Authentication Server にルーティングする方法について説明します。 Azure MFA と RD ゲートウェイを組み合わせると、強力な認証機能により会社の資産に対する統制を維持しつつ、ユーザーが自らの作業環境にどこからでもアクセスできる状態を実現できます。 

Windows Server 2012 R2 ではターミナル サービス向けの Windows 認証をサポートしていないため、MFA Server との統合には RD ゲートウェイと RADIUS を使用します。 

Multi-Factor Authentication Server は別個のサーバーにインストールする必要があります。このサーバーがプロキシとしての役割を果たし、RADIUS 要求をリモート デスクトップ ゲートウェイ サーバーの NPS に戻します。 NPS でユーザー名とパスワードが検証されると、Multi-Factor Authentication Server に応答が返されます。Multi-Factor Authentication Server は、その応答を受けて認証の&2; つ目の要素を実行し、ゲートウェイに結果を返します。

## <a name="prerequisites"></a>前提条件

- ドメインに参加している Azure MFA Server。 インストールがまだの場合には、「[Azure Multi-Factor Authentication Server の概要](multi-factor-authentication-get-started-server.md)」の手順に従ってインストールしてください。
- ネットワーク ポリシー サービスを使って認証処理を実行するリモート デスクトップ ゲートウェイ。

## <a name="configure-the-remote-desktop-gateway"></a>リモート デスクトップ ゲートウェイの構成
RD ゲートウェイは、RADIUS 認証を Azure Multi-Factor Authentication Server に送信するように構成する必要があります。 

1. RD ゲートウェイ マネージャーでサーバー名を右クリックし、**[プロパティ]** を選択します。
2. **[RD CAP ストア]** タブに移動し、**[NPS を実行するセントラル サーバー]** を選択します。 
3. RADIUS サーバーとして&1; つ以上の Azure Multi-Factor Authentication Server を追加します。これには、各サーバーの名前または IP アドレスを入力します。 
4. 各サーバーの共有シークレットを作成します。

## <a name="configure-nps"></a>NPS の構成
RD ゲートウェイは、NPS を使用して Azure Multi-Factor Authentication に RADIUS 要求を送信します。 NPS の構成で最初に行うのは、タイムアウトの設定の変更です。これにより、2 段階認証の完了前に RD ゲートウェイがタイムアウトになるのを防ぎます。 次に、NPS を更新して MFA Server から RADIUS 認証の結果を受信できるようにします。 NPS の構成に使用する手順は、以下のとおりです。

### <a name="modify-the-timeout-policy"></a>タイムアウト ポリシーの変更

1. NPS で、左の列にある **[RADIUS Clients and Server (RADIUS クライアントと サーバー)]** のメニューを開き、**[リモート RADIUS サーバー グループ]** を選択します。 
2. **[TS GATEWAY SERVER GROUP (TS ゲートウェイ サーバー グループ)]** を選択します。 
3. **[負荷分散]** タブに移動します。 
4. [要求が破棄されたとみなされる応答待ち時間 (秒数)] と [サーバーが利用不可能とみなされる要求間隔 (秒数)] の値をそれぞれ、30 ～ 60 秒に変更します  (サーバーが認証中にタイムアウトになる場合には、ここに戻って秒数を増やしてください)。
5. **[認証/アカウント]** タブに移動し、指定されている RADIUS ポートと、Multi-Factor Authentication Server がリッスンするポートが一致していることを確認します。

### <a name="prepare-nps-to-receive-authentications-from-the-mfa-server"></a>NPS で MFA Server から認証結果を受信するための準備

1. 左の列にある [RADIUS Clients and Server (RADIUS クライアントと サーバー)] で **[RADIUS クライアント]** を右クリックし、**[新規]** を選択します。
2. Azure Multi-Factor Authentication Server を RADIUS クライアントとして追加します。 フレンドリ名を選択し、共有シークレットを指定します。
3. 左の列で **[ポリシー]** メニューを開き、**[接続要求ポリシー]** を選択します。 すると、RD ゲートウェイの構成時に作成されたポリシー "TS GATEWAY AUTHORIZATION POLICY" があるはずです。 このポリシーは、Multi-Factor Authentication Server に RADIUS 要求を転送します。
4. **[TS GATEWAY AUTHORIZATION POLICY]** を右クリックして、**[ポリシーの複製]** を選択します。 
5. 新しいポリシーを開いて、**[条件]** タブに移動します。
6. 手順 2 で Azure Multi-Factor Authentication Server の RADIUS クライアントに設定したフレンドリ名とクライアントのフレンドリ名とを照合する条件を追加します。 
7. **[設定]** タブに移動して、**[認証]** を選択します。
8. 認証プロバイダーの設定を **[このサーバーで要求を認証する]** に変更します。 このポリシーは、NPS が Azure MFA Server から RADIUS 要求を受信した場合に、RADIUS 要求を Multi-Factor Authentication Server に戻してループ状態にするのではなく、ローカルで認証が行われるようにするものです。 
9. 条件のループを防ぐためには、**[接続要求ポリシー]** ウィンドウで新しいポリシーを元のポリシーよりも上に配置してください。

## <a name="configure-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication の構成

Azure Multi-Factor Authentication Server は、RD ゲートウェイと NPS 間の RADIUS プロキシとして構成されます。  これは、RD ゲートウェイ サーバーとは別個のドメインに参加しているサーバーにインストールする必要があります。 Azure Multi-Factor Authentication Server を構成するには、次の手順に従います。

1. Azure Multi-Factor Authentication Server を開き、[RADIUS 認証] アイコンを選択します。 
2. **[RADIUS 認証を有効にする]** チェック ボックスをオンにします。
3. [クライアント] タブで、ポートが NPS の構成と一致していることを確認し、**[追加]** を選択します。
4. RD ゲートウェイ サーバーの IP アドレス、アプリケーション名 (省略可能)、共有シークレットを追加します。 共有シークレットは、Azure Multi-Factor Authentication Server と RD ゲートウェイの両方で共通である必要があります。
3. **[ターゲット]** タブに移動し、**[RADIUS サーバー]** ラジオ ボタンを選択します。
4. **[追加]** を選択し、IP アドレス、共有シークレット、NPS サーバーのポートを入力します。 一元的な NPS を使用していない限り、RADIUS クライアントと RADIUS ターゲットは同じになります。 共有シークレットは、NPS サーバーの RADIUS クライアント セクションの&1; つのセットアップと一致している必要があります。

![RADIUS 認証](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## <a name="next-steps"></a>次のステップ

- Azure MFA と [IIS Web アプリ](multi-factor-authentication-get-started-server-iis.md)を統合する

- 「[Azure Multi-Factor Authentication についてよく寄せられる質問 (FAQ)](multi-factor-authentication-faq.md)」で回答を得る

