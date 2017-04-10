---
title: "Azure Analysis Services を管理する | Microsoft Docs"
description: "Azure で Analysis Services サーバーを管理する方法について説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 79491d0b-b00d-4e02-9ca7-adc99bc02fdb
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/27/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 3992e327bbe887338234fc2d516b053f0750a151
ms.openlocfilehash: fdc4e495fb3fb99022b0f7c487001b2fba42cccc
ms.lasthandoff: 02/16/2017


---
# <a name="manage-analysis-services"></a>Analysis Services を管理する
Azure に Analysis Services サーバーを作成した後は、すぐに、または後で、管理タスクを行うことが必要になる場合があります。 たとえば、データ更新処理の実行、サーバー上のモデルにアクセスできるユーザーの制御、サーバーの正常性の監視などです。 Azure Portal または SQL Server Management Studio (SSMS) のどちらかでしか実行できないもの、またはどちらでも実行できるものがあります。

## <a name="azure-portal"></a>Azure ポータル
[Azure Portal](http://portal.azure.com/) では、サーバーの作成と削除、サーバー リソースの監視、サイズの変更、サーバーにアクセスできるユーザーの管理を行うことができます。  問題がある場合は、サポート要求を送信することもできます。

![Azure でサーバー名を取得する](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Azure のサーバーへの接続は、組織内のサーバー インスタンスへの接続と同じです。 SSMS では、データの処理、処理スクリプトの作成、ロールの管理、PowerShell の使用など、多くの同じタスクを実行できます。 [SSMS をダウンロードしてインストールしてください](#download-and-install-ssms)。
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

 大きな違いの&1; つは、サーバーへの接続に使う認証です。 Azure Analysis Services サーバーに接続するには、**[Active Directory パスワード認証]** を選ぶ必要があります。

 SSMS を使用する場合は、最初にサーバーに接続する前に、Analysis Services の管理者グループにユーザー名が含まれていることを確認してください。 詳細については、この記事で後述する「[サーバー管理者](#server-administrators)」を参照してください。

### <a name="to-connect-with-ssms"></a>SSMS で接続するには
1. 接続する前に、サーバー名を取得する必要があります。 **Azure Portal** でサーバーを選び、**[概要]** > **[サーバー名]** のサーバー名をコピーします。
   
    ![Azure でサーバー名を取得する](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. SSMS の**オブジェクト エクスプローラー**で、**[接続]** > **[Analysis Services]** の順にクリックします。
3. **[サーバーへの接続]** ダイアログ ボックスで、サーバー名を貼り付けてから、**[認証]** で次のいずれかを選びます。
   
    **[Active Directory 統合認証]**: Active Directory で Azure Active Directory フェデレーションへのシングル サインオンを使います。
   
    **[Active Directory パスワード認証]**: 組織アカウントを使います。 たとえば、ドメイン参加非コンピューターから接続するときです。
   
    注: Active Directory 認証が表示されない場合は、[最新バージョンの SSMS](#download-and-install-ssms) への更新が必要な可能性があります。
   
    ![SSMS で接続する](./media/analysis-services-manage/aas-manage-connect-ssms.png)

SSMS を使う Azure でのサーバーの管理はオンプレミスのサーバーの管理とよく似ているので、ここでは詳しく説明しません。 必要なヘルプはすべて、MSDN の「[Analysis Services インスタンス管理](https://msdn.microsoft.com/library/hh230806.aspx)」で見つかります。

## <a name="server-administrators-and-database-users"></a>サーバー管理者とデータベース ユーザー
Azure Analysis Services のユーザーには、サーバー管理者とデータベース ユーザーの&2; 種類があります。 両方のユーザーが Azure Active Directory に必要で、組織の電子メール アドレスまたは UPN で指定されている必要があります。 これは、Windows ドメイン ユーザー名でサーバー管理者とデータベース ユーザーをサポートする、オンプレミスの表形式モデル データベースとは異なります。 詳細については、「[Azure Analysis Services でのユーザーの管理](analysis-services-manage-users.md)」を参照してください。

## <a name="download-and-install-ssms"></a>SSMS のダウンロードとインストール
[SQL Server Management Studio をダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)します。

ダウンロード ページには、2 つのオプションがあります。
 
* リリース候補 - Azure Analysis Services プレビューのみに接続する場合に推奨されています。 このリリースは、最新の更新プログラムを取得するため、Azure Analysis Services プレビューで最適に動作します。
* 最新の運用リリース - 運用サーバーと Azure Analysis Services プレビューの両方に接続する場合に使用することが推奨されています。 このリリースを使用する際は、レジストリを編集して Azure Active Directory 認証を有効にすることが必要になる場合があります。

## <a name="enable-azure-active-directory-authentication"></a>Azure Active Directory 認証を有効にする

レジストリで SSMS の Azure Active Directory 認証機能を有効にするには、EnableAAD.reg という名前のテキスト ファイルを作り、次のスクリプトをコピーして貼り付けます。

```
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Software\Microsoft\Microsoft SQL Server\Microsoft Analysis Services\Settings]
"AS AAD Enabled"="True"
```

ファイルを保存して実行します。

## <a name="troubleshooting-connection-problems"></a>接続の問題のトラブルシューティング
SSMS を使用してサーバーに接続するときに、(手順 3. で) 非フェデレーション アカウントまたは Azure Active Directory 以外のアカウントを使用してサインインしようとして接続できない場合は、ログイン キャッシュを消去する必要があります。 次の手順に従う前に、SSMS を閉じます。

1. ファイル エクスプローラーで `C:\Users\<user_name>\AppData\Local\` に移動します。
2. **AADCacheOM** フォルダーを削除します。
3. **ローカル** フォルダーで、**omlibs-tokens-cache.** の名前で始まる .dat ファイルを検索します。 見つかった場合は、削除します。
4. SSMS を開き、上記の「[SSMS で接続するには](#to-connect-with-ssms)」の手順を繰り返します。

## <a name="next-steps"></a>次のステップ
新しいサーバーに表形式モデルをまだデプロイしていない場合は、ここでやるのが絶好のタイミングです。 詳しくは、「[Azure Analysis Services にデプロイする](analysis-services-deploy.md)」をご覧ください。

モデルをサーバーにデプロイしてある場合は、クライアントまたはブラウザーを使って接続できる状態になっています。 詳しくは、「[Get data from Azure Analysis Services server](analysis-services-connect.md)」(Azure Analysis Services サーバーからデータを取得する) をご覧ください。


