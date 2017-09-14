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
ms.date: 08/15/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: 847eb792064bd0ee7d50163f35cd2e0368324203
ms.openlocfilehash: b897e81351ebee11c292e67ac76ba8202a6f0108
ms.contentlocale: ja-jp
ms.lasthandoff: 08/19/2017

---
# <a name="manage-analysis-services"></a>Analysis Services を管理する
Azure に Analysis Services サーバーを作成した後は、すぐに、または後で、管理タスクを行うことが必要になる場合があります。 たとえば、データ更新処理の実行、サーバー上のモデルにアクセスできるユーザーの制御、サーバーの正常性の監視などです。 Azure Portal または SQL Server Management Studio (SSMS) のどちらかでしか実行できないもの、またはどちらでも実行できるものがあります。

## <a name="azure-portal"></a>Azure ポータル
[Azure Portal](http://portal.azure.com/) では、サーバーの作成と削除、サーバー リソースの監視、サイズの変更、サーバーにアクセスできるユーザーの管理を行うことができます。  問題がある場合は、サポート要求を送信することもできます。

![Azure でサーバー名を取得する](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Azure のサーバーへの接続は、組織内のサーバー インスタンスへの接続と同じです。 SSMS では、データの処理、処理スクリプトの作成、ロールの管理、PowerShell の使用など、多くの同じタスクを実行できます。
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>SSMS のダウンロードとインストール
最新の機能をすべて入手するとともに、Azure Analysis Services サーバーとの円滑な接続を実現するために、必ず最新バージョンの SSMS を使用してください。 

[SQL Server Management Studio をダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)します。


### <a name="to-connect-with-ssms"></a>SSMS で接続するには
 SSMS を使用する場合は、最初にサーバーに接続する前に、Analysis Services の管理者グループにユーザー名が含まれていることを確認してください。 詳細については、この記事で後述する「[サーバー管理者](#server-administrators)」を参照してください。

1. 接続する前に、サーバー名を取得する必要があります。 **Azure Portal** でサーバーを選び、**[概要]** > **[サーバー名]** のサーバー名をコピーします。
   
    ![Azure でサーバー名を取得する](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. SSMS の**オブジェクト エクスプローラー**で、**[接続]** > **[Analysis Services]** の順にクリックします。
3. **[サーバーへの接続]** ダイアログ ボックスで、サーバー名を貼り付けてから、**[認証]** で次のいずれかの認証の種類を選びます。
   
    **[Windows 認証]**: <Windows ドメイン>\<ユーザー名> とパスワードを資格情報として使います。

    **[Active Directory パスワード認証]**: 組織アカウントを使います。 たとえば、ドメイン参加非コンピューターから接続するときです。

    **[Active Directory のユニバーサル認証]**: [非対話型認証や多要素認証](../sql-database/sql-database-ssms-mfa-authentication.md)を使います。 
   
    ![SSMS で接続する](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>サーバー管理者とデータベース ユーザー
Azure Analysis Services のユーザーには、サーバー管理者とデータベース ユーザーの 2 種類があります。 両方のユーザーが Azure Active Directory に必要で、組織の電子メール アドレスまたは UPN で指定されている必要があります。 詳しくは、「[認証とユーザーのアクセス許可](analysis-services-manage-users.md)」をご覧ください。


## <a name="troubleshooting-connection-problems"></a>接続の問題のトラブルシューティング
SSMS を使用して接続する際に問題が発生した場合、ログイン キャッシュをクリアすることが必要になることがあります。 ディスクには何もキャッシュされません。キャッシュをクリアするには、接続プロセスを閉じてからもう一度開始してください。 

## <a name="next-steps"></a>次のステップ
新しいサーバーに表形式モデルをまだデプロイしていない場合は、ここでやるのが絶好のタイミングです。 詳しくは、「[Azure Analysis Services にデプロイする](analysis-services-deploy.md)」をご覧ください。

モデルをサーバーにデプロイしてある場合は、クライアントまたはブラウザーを使って接続できる状態になっています。 詳しくは、「[Get data from Azure Analysis Services server](analysis-services-connect.md)」(Azure Analysis Services サーバーからデータを取得する) をご覧ください。


