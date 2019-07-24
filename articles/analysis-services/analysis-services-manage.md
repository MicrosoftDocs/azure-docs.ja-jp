---
title: Azure Analysis Services を管理する | Microsoft Docs
description: Azure で Analysis Services サーバーを管理する方法について説明します。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 0bae06d46c2c96ba9dd058e9c2d380379523811c
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993923"
---
# <a name="manage-analysis-services"></a>Analysis Services を管理する
Azure に Analysis Services サーバーを作成した後は、すぐに、または後で、管理タスクを行うことが必要になる場合があります。 たとえば、データ更新処理の実行、サーバー上のモデルにアクセスできるユーザーの制御、サーバーの正常性の監視などです。 Azure Portal または SQL Server Management Studio (SSMS) のどちらかでしか実行できないもの、またはどちらでも実行できるものがあります。

## <a name="azure-portal"></a>Azure ポータル
[Azure Portal](https://portal.azure.com/) では、サーバーの作成と削除、サーバー リソースの監視、サイズの変更、サーバーにアクセスできるユーザーの管理を行うことができます。  問題がある場合は、サポート要求を送信することもできます。

![Azure でサーバー名を取得する](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Azure のサーバーへの接続は、組織内のサーバー インスタンスへの接続と同じです。 SSMS では、データの処理、処理スクリプトの作成、ロールの管理、PowerShell の使用など、多くの同じタスクを実行できます。
  
![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

### <a name="download-and-install-ssms"></a>SSMS のダウンロードとインストール
最新の機能をすべて入手するとともに、Azure Analysis Services サーバーとの円滑な接続を実現するために、必ず最新バージョンの SSMS を使用してください。 

[SQL Server Management Studio をダウンロード](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)します。


### <a name="to-connect-with-ssms"></a>SSMS で接続するには
 SSMS を使用する場合は、最初にサーバーに接続する前に、Analysis Services の管理者グループにユーザー名が含まれていることを確認してください。 詳細については、この記事で後述する「[サーバー管理者とデータベース ユーザー](#server-administrators-and-database-users)」を参照してください。

1. 接続する前に、サーバー名を取得する必要があります。 **Azure Portal** でサーバーを選び、**[概要]** > **[サーバー名]** のサーバー名をコピーします。
   
    ![Azure でサーバー名を取得する](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. SSMS の**オブジェクト エクスプローラー**で、**[接続]** > **[Analysis Services]** の順にクリックします。
3. **[サーバーへの接続]** ダイアログ ボックスで、サーバー名を貼り付けてから、**[認証]** で次のいずれかの認証の種類を選びます。   
    > [!NOTE]
    > 認証の種類として、**[Active Directory - MFA サポートで汎用]** が推奨されます。

    > [!NOTE]
    > Microsoft アカウント、Live ID、Yahoo、Gmail などでサインインする場合は、パスワード フィールドを空白のままにしておきます。 [接続] をクリックすると、パスワードの入力を求められます。

    **[Windows 認証]**: <Windows ドメイン>\<ユーザー名> とパスワードを資格情報として使います。

    **[Active Directory パスワード認証]**: 組織アカウントを使います。 たとえば、ドメイン参加非コンピューターから接続するときです。

    **[Active Directory - MFA サポートで汎用]**: [非対話型認証または多要素認証](../sql-database/sql-database-ssms-mfa-authentication.md)を使います。 
   
    ![SSMS で接続する](./media/analysis-services-manage/aas-manage-connect-ssms.png)

## <a name="server-administrators-and-database-users"></a>サーバー管理者とデータベース ユーザー
Azure Analysis Services のユーザーには、サーバー管理者とデータベース ユーザーの 2 種類があります。 両方のユーザーが Azure Active Directory に必要で、組織の電子メール アドレスまたは UPN で指定されている必要があります。 詳しくは、「[認証とユーザーのアクセス許可](analysis-services-manage-users.md)」をご覧ください。


## <a name="troubleshooting-connection-problems"></a>接続の問題のトラブルシューティング
SSMS を使用して接続する際に問題が発生した場合、ログイン キャッシュをクリアすることが必要になることがあります。 ディスクには何もキャッシュされません。キャッシュをクリアするには、接続プロセスを閉じてからもう一度開始してください。 

## <a name="next-steps"></a>次の手順
新しいサーバーに表形式モデルをまだデプロイしていない場合は、ここでやるのが絶好のタイミングです。 詳しくは、「[Azure Analysis Services にデプロイする](analysis-services-deploy.md)」をご覧ください。

モデルをサーバーにデプロイしてある場合は、クライアントまたはブラウザーを使って接続できる状態になっています。 詳しくは、「[Get data from Azure Analysis Services server](analysis-services-connect.md)」(Azure Analysis Services サーバーからデータを取得する) をご覧ください。

