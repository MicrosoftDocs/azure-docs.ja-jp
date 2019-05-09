---
title: Azure Data Lake Storage Gen1 出力に対して Azure Stream Analytics ジョブを認証する
description: この記事では、マネージド ID を使用して、Azure Data Lake Storage Gen1 出力に対してご自身の Azure Stream Analytics ジョブを認証する方法について説明します。
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 695591fedfacb34742335a6e9d6ca32a9c77eb7e
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59522063"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>マネージド ID を使用して Azure Data Lake Storage Gen1 に対して Stream Analytics を認証する

Azure Stream Analytics では、Azure Data Lake Storage (ADLS) Gen1 出力でのマネージド ID 認証をサポートします。 ID は、特定の Stream Analytics ジョブを表す Azure Active Directory に登録済みのマネージド アプリケーションであり、対象のリソースを認証するために使用できます。 マネージド ID は、パスワードの変更や 90 日ごとに発生するユーザー トークンの有効期限切れによる再認証の必要性などのユーザー ベースの認証方法の制限を排除します。 さらに、マネージド ID は、Azure Data Lake Storage Gen1 に出力する Stream Analytics ジョブのデプロイの自動化で役に立ちます。

この記事では、Azure Data Lake Storage Gen1 に出力する Azure Stream Analytics ジョブのマネージド ID を有効にする 3 つの方法を示します。Azure portal による方法、Azure Resource Manager テンプレートのデプロイによる方法、そして Visual Studio 用の Azure Stream Analytics ツールによる方法があります。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure ポータル

1. Azure portal で、新しい Stream Analytics ジョブを作成するか既存のジョブを開くことから始めます。 画面の左側にあるメニュー バーで、**[構成]** の下にある **[マネージド ID]** を選択します。

   ![Stream Analytics のマネージド ID の構成](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. 右側に表示されるウィンドウで **[システム割り当てマネージド ID を使用]** を選択します。 **[保存]** をクリックして、Azure Active Directory 内に Stream Analytics ジョブの ID 用のサービス プリンシパルを保存します。 新しく作成された ID のライフ サイクルは、Azure によって管理されます。 Stream Analytics ジョブが削除されると、関連付けられた ID (つまりサービス プリンシパル) も Azure によって自動的に削除されます。

   構成が保存されると、サービス プリンシパルのオブジェクト ID (OID) が、次に示すようにプリンシパル ID として表示されます。

   ![Stream Analytics サービス プリンシパル ID](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   サービス プリンシパルは、Stream Analytics ジョブと同じ名前を持ちます。 たとえば、ジョブの名前が **MyASAJob** であれば、作成されるサービス プリンシパルの名前も **MyASAJob** になります。

3. ADLS Gen1 出力シンクの [出力プロパティ] ウィンドウで、[認証モード] ドロップダウンをクリックし、**[マネージド ID]** を選択します。

4. 残りのプロパティを入力します。 ADLS 出力の作成の詳細については、[Stream Analytics を使用した Data Lake Store 出力の作成](../data-lake-store/data-lake-store-stream-analytics.md)に関する記事を参照してください。 操作が終了したら、**[OK]** をクリックします。

   ![Azure Data Lake Storage の構成](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. ADLS Gen1 の [概要] ページに移動し、**[データ エクスプローラー]** をクリックします。

   ![Data Lake Storage の [概要] での構成](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. [データ エクスプローラー] ウィンドウで、**[アクセス]** をクリックし、[アクセス] ウィンドウで **[追加]** をクリックします。

   ![Data Lake Store のアクセスの構成](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. **[ユーザーまたはグループの選択]** ウィンドウで、サービス プリンシパルの名前をテキスト ボックスに入力します。 サービス プリンシパルの名前は、対応する Stream Analytics ジョブの名前でもあることに注意してください。 プリンシパル名の入力を開始すると、テキスト ボックスの下に該当するプリンシパル名が表示されます。 目的のサービス プリンシパル名を選択し、**[選択]** をクリックします。

   ![サービス プリンシパル名の選択](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. **[アクセス許可]** ウィンドウで、**[書き込み]** と **[実行]** のアクセス許可をオンにし、割当先として **[このフォルダーとすべての子]** を選択します。 次に、**[OK]** をクリックします。

   ![書き込みと実行のアクセス許可を選択](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. 次のように、サービス プリンシパルが、**[アクセス]** ウィンドウの **[割り当てられたアクセス許可]** の下に表示されます。 これで、Stream Analytics ジョブに戻って、ジョブを開始できます。

   ![ポータルの Stream Analytics アクセス リスト](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Data Lake Storage Gen1 ファイル システムのアクセス許可の詳細については、「[Azure Data Lake Store Gen1 のアクセス制御](../data-lake-store/data-lake-store-access-control.md)」を参照してください。

## <a name="stream-analytics-tools-for-visual-studio"></a>Visual Studio 用の Stream Analytics ツール

1. JobConfig.json 内で、**[システム割り当て ID を使用]** を **[True]** に設定します。

   ![Stream Analytics ジョブ構成のマネージド ID](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. ADLS Gen1 出力シンクの [出力プロパティ] ウィンドウで、[認証モード] ドロップダウンをクリックし、**[マネージド ID]** を選択します。

   ![ADLS 出力のマネージド ID](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. 残りのプロパティを入力して、**[保存]** をクリックします。

4. クエリ エディターで **[Azure に送信]** をクリックします。

   ジョブを送信するときに、ツールでは次の 2 つの操作を行います。

   * Azure Active Directory 内に Stream Analytics ジョブの ID に対応するサービス プリンシパルを自動的に作成します。 新しく作成された ID のライフ サイクルは、Azure によって管理されます。 Stream Analytics ジョブが削除されると、関連付けられた ID (つまりサービス プリンシパル) も Azure によって自動的に削除されます。

   * ジョブ内で使用される ADLS Gen1 プレフィックス パスへの**書き込み**と**実行**のアクセス許可を自動的に設定し、このフォルダーおよびすべての子に割り当てます。

5. [Stream Analytics CI.CD Nuget パッケージ](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) バージョン 1.5.0 以上をビルド コンピューター上 (Visual Studio 外部) で使用して、次のプロパティによって Resource Manager テンプレートを生成できます。 次のセクションの Resource Manager テンプレートのデプロイに関する手順に従って、サービス プリンシパルを取得し、PowerShell によってサービス プリンシパルへのアクセスを付与します。

## <a name="resource-manager-template-deployment"></a>Resource Manager テンプレートの展開

1. Resource Manager テンプレートのリソース セクションに次のプロパティを含めることで、マネージド ID を持つ *Microsoft.StreamAnalytics/streamingjobs* を作成できます。

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   このプロパティは、Azure Stream Analytics ジョブの ID を作成して管理するように Azure Resource Manager に通知します。

   **サンプル ジョブ**
   
   ```json
   {
     "Name": "AsaJobWithIdentity",
     "Type": "Microsoft.StreamAnalytics/streamingjobs",
     "Location": "West US",
     "Identity": {
       "Type": "SystemAssigned",
     },
     "properties": {
       "sku": {
         "name": "standard"
       },
       "outputs": [
         {
           "name": "string",
           "properties":{
             "datasource": {
               "type": "Microsoft.DataLake/Accounts",
               "properties": {
                 "accountName": "myDataLakeAccountName",
                 "filePathPrefix": "cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
             }
           }
         }
       }
     }
   }
   ```
  
   **サンプル ジョブの応答**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
     }
   }
   ```

   ジョブの応答から、必要な ADLS リソースへのアクセスを許可するプリンシパル ID (principalId) をメモしておきます。

   **テナント ID (tenantId)** は、サービス プリンシパルが作成されている Azure Active Directory テナントの ID です。 サービス プリンシパルは、サブスクリプションによって信頼されている Azure テナント内に作成されます。

   **Type (種類)** は、マネージド ID の種類で説明したように、マネージド ID の種類を示します。 システムによる割り当てのみがサポートされます。

2. PowerShell を使用してサービス プリンシパルにアクセスします。 PowerShell を使用してサービス プリンシパルにアクセスできるようにするには、次のコマンドを実行します。

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** は、サービス プリンシパルのオブジェクト ID であり、サービス プリンシパルの作成後にポータルの画面に表示されます。 Resource Manager テンプレートのデプロイを使用してジョブを作成した場合、オブジェクト ID は、ジョブの応答の ID プロパティの中に表示されます。

   **例**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   上記の PowerShell コマンドの詳細については、[Set-AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) のドキュメントを参照してください。

## <a name="limitations"></a>制限事項
この機能は次をサポートしません。

1. **マルチテナント アクセス**: 特定の Stream Analytics ジョブに対して作成されたサービス プリンシパルは、ジョブが作成された Azure Active Directory テナント上に配置され、別の Azure Active Directory テナントに配置されたリソースに対しては使用できません。 そのため、MSI は、Azure Stream Analytics ジョブと同じ Azure Active Directory テナント内にある ADLS ジェネレーション 1 リソースに対してのみ使用できます。 

2. **[ユーザー割り当て ID](../active-directory/managed-identities-azure-resources/overview.md)**: サポートされていません。 つまりユーザーは、Stream Analytics ジョブで使用される、独自のサービス プリンシパルを入力することはできません。 サービス プリンシパルは、Azure Stream Analytics によって生成されます。

## <a name="next-steps"></a>次の手順

* [Stream Analytics を使用して Data Lake Store 出力を作成する](../data-lake-store/data-lake-store-stream-analytics.md)
* [Visual Studio で Stream Analytics クエリをローカルでテストする](stream-analytics-vs-tools-local-run.md)
* [Visual Studio 用の Azure Stream Analytics ツールを使用してライブ データをローカルにテストする](stream-analytics-live-data-local-testing.md) 
