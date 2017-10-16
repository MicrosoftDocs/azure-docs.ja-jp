---
title: "Azure Active Directory で Cloud App Discovery のスナップショット レポートを作成する | Microsoft Docs"
description: "Cloud App Discovery でアプリケーションを検索および管理する利点と機能について説明します。"
services: active-directory
keywords: "Cloud App Discovery, アプリケーションの管理"
documentationcenter: 
author: curtand
manager: femila
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: curtand
ms.reviewer: nigu
ms.custom: it-pro
ms.openlocfilehash: dd4cf725689739ce4c8b86a4d8203ffc5aff016b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-cloud-app-discovery-snapshot-reports"></a>Cloud App Discovery のスナップショット レポートの作成

自動ログ コレクターをセットアップする前に、ログを手動でアップロードし、Cloud App Security がログを解析できるようにします。 まだログがなく、ログの内容をサンプルで確認する場合は、次の手順に従ってサンプル ログ ファイルをダウンロードし、ログがどのように表示されるのかを確認してください。

## <a name="to-create-a-snapshot-report"></a>スナップショット レポートを作成するには

1. 組織のユーザーがインターネットにアクセスする際に使用するファイアウォールとプロキシ サーバーからログ ファイルを収集します。 組織内の代表的なユーザー アクティビティを示す、トラフィックのピーク時のログを収集します。
2. [Cloud App Security のメニュー バー](https://portal.cloudappsecurity.com)の **[検出]** をクリックし、**[スナップショット レポートの作成]** を選択します。
  
  ![新しいスナップショット レポートの作成](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-command.png)
3. **レポート名**と**説明**を入力します。
    
  ![新しいスナップショット レポート](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-form.png)
4. ログ ファイルをアップロードする**データソース**を選択します。
5. ダウンロードできるサンプルに従ってログが適切にフォーマットされるように、ログ形式を確認します。 **[表示して検証]** をクリックし、**[サンプル ログのダウンロード]** をクリックします。 ログを提供されたサンプルと比較して、互換性があることを確認します。
  
  ![ログ形式の確認](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-verify.png)
  >  [!NOTE]
  > FTP サンプル形式はスナップショットと自動アップロードでサポートされていますが、syslog は自動アップロードでのみサポートされています。 サンプル ログをダウンロードすると、サンプル FTP ログがダウンロードされます。
6. アップロードする**トラフィック ログを選択**します。 一度に最大 20 個のファイルをアップロードできます。 圧縮された ZIP ファイルもサポートされています。
  
7. **Create** をクリックしてください。 アップロードの完了後、ファイルの解析と分析に少し時間がかかることがあります。 処理が完了すると、準備ができたことを通知する電子メールが送信されます。

8. **[スナップショット レポートの管理]** を選択し、スナップショット レポートを選択します。
  
  ![スナップショット レポートの管理](./media/cloudappdiscovery-set-up-snapshots/create-snapshot-manage.png)

## <a name="next-steps"></a>次のステップ

* [Azure AD の Cloud App Discovery の使用を開始する](cloudappdiscovery-get-started.md)
* [継続的なレポートのために自動ログ アップロードを構成する](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [カスタム ログ パーサーを使用する](https://docs.microsoft.com/cloud-app-security/custom-log-parser)
