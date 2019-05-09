---
title: ワークスペースのトラブルシューティング
titleSuffix: Azure Machine Learning Studio
description: このガイドでは、Azure Machine Learning Studio ワークスペースの設定に伴い、よく生じることのある問題の解決策を説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7cc825daa29a0398793f3c6fc5ce8ee426ad79e6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57884199"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-workspace"></a>トラブルシューティング ガイド:Azure Machine Learning Studio ワークスペースの作成と接続
このガイドでは、Azure Machine Learning Studio ワークスペースの設定に伴い、よく生じることのある問題の解決策を説明します。



## <a name="workspace-owner"></a>ワークスペースの所有者
Machine Learning Studio でワークスペースを開くには、ワークスペースを作成する際に使用した Microsoft アカウントにサインインするか、所有者からワークスペースへの参加の招待を受け取る必要があります。 ワークスペースの管理は Azure Portal で実行できます。アクセス許可もポータルで構成できます。

ワークスペースの管理方法の詳細については、「[Azure Machine Learning Studio ワークスペースの管理]」をご覧ください。

[Azure Machine Learning Studio ワークスペースの管理]: manage-workspace.md

## <a name="allowed-regions"></a>許可されたリージョン
現在、Machine Learning は一部のリージョンでのみ利用できます。 サブスクリプションに対象のリージョンが含まれていない場合は、"許可されたリージョンにサブスクリプションがありません" というエラー メッセージが表示されることがあります。

サブスクリプションへのリージョンの追加を要求するには、Azure Portal で Microsoft サポート要求を新規作成し、問題の種類として **[課金]** を選択して、プロンプトに従って要求を送信します。

## <a name="storage-account"></a>ストレージ アカウント
Machine Learning サービスでは、データを格納するためのストレージ アカウントが必要です。 既存のストレージ アカウントを使用するか、新しい Machine Learning Studio ワークスペースの作成時に新しいストレージ アカウントを作成することができます (新しいストレージ アカウントを作成するためのクォータがある場合)。

新しい Machine Learning Studio ワークスペースの作成後は、ワークスペースの作成に使用した Microsoft アカウントで Machine Learning Studio にサインインできます。 エラー メッセージ「ワークスペースが見つかりません」(次のスクリーン ショットに類似) が発生する場合は、次の手順に従ってブラウザーの Cookie を削除します。

![Workspace not found](media/troubleshooting-creating-ml-workspace/screen3.png)

**ブラウザーの Cookie を削除するには**

1. Internet Explorer を使用している場合は、右上隅の **[ツール]** をクリックして、**[インターネット オプション]** を選択します。  

   ![[インターネット オプション]](media/troubleshooting-creating-ml-workspace/screen4.png)

2. **[全般]** タブで、**[削除]** をクリックします。

   ![[全般] タブ](media/troubleshooting-creating-ml-workspace/screen5.png)

3. **[閲覧の履歴の削除]** ダイアログ ボックスで、**[クッキーと Web サイト データ]** が選択されていることを確認し、**[削除]** をクリックします。

   ![Cookie の削除](media/troubleshooting-creating-ml-workspace/screen6.png)

Cookie が削除されたら、ブラウザーを再起動し、[Microsoft Azure Machine Learning Studio](https://studio.azureml.net) ページに移動します。 ユーザー名とパスワードの入力が求められたら、ワークスペースの作成時に使用したアカウントと同じ Microsoft アカウントを入力します。

## <a name="comments"></a>説明

目的は、可能な限り円滑でシームレスに Machine Learning を体験していただくことです。 お客様の操作の円滑化をお手伝いできるよう、コメントや問題がありましたら、 [Azure Machine Learning フォーラム](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) に投稿してください。
