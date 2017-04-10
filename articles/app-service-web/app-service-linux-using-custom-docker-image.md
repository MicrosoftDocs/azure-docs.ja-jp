---
title: "Azure App Service on Linux 向けにカスタム Docker イメージを使用する方法 | Microsoft Docs"
description: "App Service on Linux 向けにカスタム Docker イメージを使用する方法を説明します。"
keywords: "Azure App Service, Web アプリ, Linux, Docker, コンテナー"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
translationtype: Human Translation
ms.sourcegitcommit: bb4c7ea7adfe1326ae8259782b5de2762c8c2bf5
ms.openlocfilehash: 7e4aab65feac187b48ccca65b35bb94185323506
ms.lasthandoff: 02/17/2017


---

# <a name="using-a-custom-docker-image-for-app-service-on-linux"></a>App Service on Linux でのカスタム Docker イメージの使用 #

App Service は、事前定義済みのアプリケーション スタックを Linux 上で提供し、PHP 7.0 や Node.js 4.5 などの特定のバージョンをサポートします。 App Service on Linux では、これらの構成済みのアプリケーション スタックをホストするために Docker コンテナーを使用します。 まだ Azure で定義されていないアプリケーション スタックに Web アプリをデプロイする場合にも、カスタム Docker イメージを使用できます。 カスタム Docker イメージは、パブリック Docker リポジトリとプライベート Docker リポジトリのどちらでもホストできます。


## <a name="how-to-set-a-custom-docker-image-for-a-web-app"></a>Web アプリのカスタム Docker イメージを設定する方法
カスタム Docker イメージは、新規の Web アプリと既存の Web アプリのどちらにでも設定できます。 [Azure Portal](https://portal.azure.com) を使用して Linux で Web アプリを作成する場合、**[コンテナーの構成]** をクリックしてカスタム Docker イメージを設定します。

![Linux の新しい Web アプリのカスタム Docker イメージ][1]


## <a name="how-to-use-a-custom-docker-image-from-docker-hub"></a>Docker Hub からカスタム Docker イメージを使用する方法 ##
Docker Hub からカスタム Docker イメージを使用するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で Linux の Web アプリを特定し、**[設定]** で **[Docker コンテナー]** をクリックします。

2.  **[イメージ ソース]** として **[Docker Hub]** を選択し、**[パブリック]** または **[プライベート]** をクリックし、**イメージとオプションのタグ名**を入力します (例: `node:4.5`)。 **[スタートアップ コマンド]** は、Docker イメージ ファイル内の定義に基づいて自動的に設定されますが、独自のコマンドを設定することもできます。  

    ![Docker Hub パブリック リポジトリ イメージの構成][2]

    イメージがプライベート リポジトリ由来の場合は、さらに Docker Hub の資格情報をプライベート Docker Hub リポジトリの **[ログイン ユーザー名]** と **[パスワード]** として入力する必要があります。

    ![Docker Hub プライベート リポジトリ イメージの構成][3]

3. コンテナーを構成したら、**[保存]** をクリックします。

## <a name="how-to-use-a-docker-image-from-a-private-image-registry"></a>プライベート イメージ レジストリから Docker イメージを使用する方法 ##
プライベート イメージ レジストリからカスタム Docker イメージを使用するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で Linux の Web アプリを特定し、**[設定]** で **[Docker コンテナー]** をクリックします。

2.  **[イメージのソース]** として **[プライベート レジストリ]** をクリックします。 資格情報 (**[ログイン ユーザー名]** および **[パスワード]**) と共に、**[イメージとオプションのタグ]** とプライベート レジストリの **[サーバー URL]** を入力します。 **[保存]**をクリックします。

    ![プライベート レジストリからの Docker イメージの構成][4]


## <a name="how-to-set-the-port-used-by-your-docker-image"></a>Docker イメージで使用されるポートを設定する方法 ##

Web アプリ向けにカスタム Docker イメージを使用する場合、Dockerfile 内で `PORT` 環境変数を使用することができます。この環境変数は、生成されたコンテナーに追加されます。 Ruby アプリケーションの場合は、次の Docker ファイルの例を検討してみてください。

    FROM ruby:2.2.0
    RUN mkdir /app
    WORKDIR /app
    ADD . /app
    RUN bundle install
    CMD bundle exec puma config.ru -p $PORT -e production

コマンドの最後の行で、実行時に PORT 環境変数が渡されることが確認できます。 コマンドでは大文字小文字の区別が重要ですので注意してください。

他のユーザーによって作成された既存の Docker イメージを使用する場合は、アプリケーション用のポート 80 以外のポートを指定する必要がある場合があります。 ポートを構成するには、次に示す値を使用して、`PORT` というアプリケーション設定を追加します。

![カスタム Docker イメージの PORT アプリ設定の構成][6]


## <a name="how-to-switch-back-to-using-a-built-in-image"></a>組み込みイメージを使用するように切り替える方法 ##

カスタム イメージの使用から組み込みイメージの使用に切り替えるには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) で Linux の Web アプリを特定し、**[設定]** で **[App Service]** をクリックします。

2. 組み込みイメージで使用する **[ランタイム スタック]** を選択し、**[保存]** をクリックします。 

![組み込み Docker イメージの構成][5]


## <a name="troubleshooting"></a>トラブルシューティング ##

カスタム Docker イメージを使用してアプリケーションを起動できない場合は、LogFiles/docker ディレクトリで Docker のログを確認してください。 このディレクトリには、SCM サイトまたは FTP 経由でアクセスできます。 

![Kudu を使用した Docker のログの表示][7]

SCM サイトには、**[開発ツール]** メニューの **[Advanced Tools]** からアクセスできます。

## <a name="next-steps"></a>次のステップ ##

App Service on Linux の使用を開始するには、次のリンクを参照してください。   

* [App Service on Linux の概要](./app-service-linux-intro.md)
* [App Service on Linux での Web Apps の作成](./app-service-linux-how-to-create-a-web-app.md)
* [Web Apps on Linux での Node.js 向け PM2 構成の使用](./app-service-linux-using-nodejs-pm2.md)
* [Azure App Service Web Apps on Linux の FAQ](app-service-linux-faq.md)

質問や問題は、[フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)に投稿してください。


<!--Image references-->
[1]: ./media/app-service-linux-using-custom-docker-image/new-configure-container.png
[2]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-public.png
[3]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-private.png
[4]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-privateregistry.png
[5]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-builtin.png
[6]: ./media/app-service-linux-using-custom-docker-image/setting-port.png
[7]: ./media/app-service-linux-using-custom-docker-image/kudu-docker-logs.png

