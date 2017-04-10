---
title: "トラブルシューティング: Machine Learning ワークスペースの作成と接続 | Microsoft Docs"
description: "Azure Machine Learning ワークスペースの作成と接続に関する一般的な問題の解決策"
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 1a8aec4b-35f9-44e8-9570-2575b8979ab1
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 9e738c4e5f43ae6c939f7c6da90c258498943e73
ms.openlocfilehash: b4740816ac61ffad032b86367fef99be7581f4c6
ms.lasthandoff: 12/14/2016


---
# <a name="troubleshooting-guide-create-and-connect-to-an-machine-learning-workspace"></a>トラブルシューティング ガイド: Machine Learning ワークスペースの作成と接続
このガイドでは、Azure Machine Learning ワークスペースの設定に伴い、よく生じることのある問題の解決策を説明します。

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="workspace-owner"></a>ワークスペースの所有者
Machine Learning Studio でワークスペースを開くには、ワークスペースを作成する際に使用した Microsoft アカウントにサインインするか、所有者からワークスペースへの参加の招待を受け取る必要があります。 ワークスペースの管理は Azure Portal で実行できます。アクセス許可もポータルで構成できます。

ワークスペースの管理方法の詳細については、「 [Azure Machine Learning ワークスペースの管理]」をご覧ください。

[Azure Machine Learning ワークスペースの管理]: machine-learning-manage-workspace.md

## <a name="allowed-regions"></a>許可されたリージョン
現在、Machine Learning は一部のリージョンでのみ利用できます。 サブスクリプションに対象のリージョンが含まれていない場合は、"許可されたリージョンにサブスクリプションがありません" というエラー メッセージが表示されることがあります。

サブスクリプションへのリージョンの追加を要求するには、Azure Portal で Microsoft サポート要求を新規作成し、問題の種類として **[課金]** を選択して、プロンプトに従って要求を送信します。

## <a name="storage-account"></a>ストレージ アカウント
Machine Learning サービスでは、データを格納するためのストレージ アカウントが必要です。 既存のストレージ アカウントを使用するか、新しい Machine Learning ワークスペースの作成時に新しいストレージ アカウントを作成することができます (新しいストレージ アカウントを作成するためのクォータがある場合)。

新しい Machine Learning ワークスペースの作成後は、ワークスペースの作成に使用した Microsoft アカウントで Machine Learning Studio にサインインできます。 エラー メッセージ「ワークスペースが見つかりません」(次のスクリーン ショットに類似) が発生する場合は、次の手順に従ってブラウザーの Cookie を削除します。

![Workspace not found][screen3]

**ブラウザーの Cookie を削除するには**

1. Internet Explorer を使用している場合は、右上隅の **[ツール]** をクリックして、**[インターネット オプション]** を選択します。  

![[インターネット オプション]][screen4]

2. **[全般]** タブで、**[削除]** をクリックします。

![[全般] タブ][screen5]

3. **[閲覧の履歴の削除]** ダイアログ ボックスで、**[クッキーと Web サイト データ]** が選択されていることを確認し、**[削除]** をクリックします。

![Cookie の削除][screen6]

Cookie が削除されたら、ブラウザーを再起動し、 [Microsoft Azure Machine Learning](https://studio.azureml.net) ページに移動します。 ユーザー名とパスワードの入力が求められたら、ワークスペースの作成時に使用したアカウントと同じ Microsoft アカウントを入力します。

## <a name="comments"></a>説明

目的は、可能な限り円滑でシームレスに Machine Learning を体験していただくことです。 お客様の操作の円滑化をお手伝いできるよう、コメントや問題がありましたら、 [Azure Machine Learning フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) に投稿してください。

[screen1]:media/machine-learning-troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/machine-learning-troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/machine-learning-troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/machine-learning-troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/machine-learning-troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/machine-learning-troubleshooting-creating-ml-workspace/screen6.png

