---
title: U-SQL ユーザー定義演算子の開発 - Azure Data Lake Analytics
description: Azure Data Lake Analytics ジョブで使用および再使用されるユーザー定義演算子の開発方法について説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: 11efdb727bacadb674fb49374ef1c70fcc788ecc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219991"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>U-SQL ユーザー定義演算子 (UDO) の開発
このアーティクルでは、U-SQL ジョブ内のデータを処理するユーザー定義演算子を開発する方法について説明します。

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>U-SQL でのユーザー定義演算子の定義と使用

### <a name="to-create-and-submit-a-u-sql-job"></a>U-SQL ジョブを作成して送信するには

1. Visual Studio で、 **[ファイル]、[新規作成]、[プロジェクト]、[ U-SQL プロジェクト]** の順に選択します。
2. **[OK]** をクリックします。 Visual Studio によって、Script.usql ファイルを使用するソリューションが作成されます。
3. **ソリューション エクスプローラー** で、Script.usql を展開してから **Script.usql.cs** をダブルクリックします。
4. 以下のコードをファイルに貼り付けます。

   ```usql
   using Microsoft.Analytics.Interfaces;
   using System.Collections.Generic;
   namespace USQL_UDO
   {
       public class CountryName : IProcessor
       {
           private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
           {
               {
                   "Deutschland", "Germany"
               },
               {
                   "Suisse", "Switzerland"
               },
               {
                   "UK", "United Kingdom"
               },
               {
                   "USA", "United States of America"
               },
               {
                   "中国", "PR China"
               }
           };
           public override IRow Process(IRow input, IUpdatableRow output)
           {
               string UserID = input.Get<string>("UserID");
               string Name = input.Get<string>("Name");
               string Address = input.Get<string>("Address");
               string City = input.Get<string>("City");
               string State = input.Get<string>("State");
               string PostalCode = input.Get<string>("PostalCode");
               string Country = input.Get<string>("Country");
               string Phone = input.Get<string>("Phone");
               if (CountryTranslation.Keys.Contains(Country))
               {
                   Country = CountryTranslation[Country];
               }
               output.Set<string>(0, UserID);
               output.Set<string>(1, Name);
               output.Set<string>(2, Address);
               output.Set<string>(3, City);
               output.Set<string>(4, State);
               output.Set<string>(5, PostalCode);
               output.Set<string>(6, Country);
               output.Set<string>(7, Phone);
               return output.AsReadOnly();
           }
       }
   }
   ```

5. **Script.usql** を開き、次の U-SQL スクリプトを貼り付けます。

   ```usql
   @drivers =
       EXTRACT UserID      string,
               Name        string,
               Address     string,
               City        string,
               State       string,
               PostalCode  string,
               Country     string,
               Phone       string
       FROM "/Samples/Data/AmbulanceData/Drivers.txt"
       USING Extractors.Tsv(Encoding.Unicode);

   @drivers_CountryName =
       PROCESS @drivers
       PRODUCE UserID string,
               Name string,
               Address string,
               City string,
               State string,
               PostalCode string,
               Country string,
               Phone string
       USING new USQL_UDO.CountryName();

   OUTPUT @drivers_CountryName
       TO "/Samples/Outputs/Drivers.csv"
       USING Outputters.Csv(Encoding.Unicode);
   ```

6. Data Lake Analytics アカウント、データベース、スキーマを指定します。
7. **ソリューション エクスプローラー** で **Script.usql** を右クリックし、 **[スクリプトのビルド]** をクリックします。
8. **ソリューション エクスプローラー** で **Script.usql** を右クリックし、 **[スクリプトの送信]** をクリックします。
9. Azure サブスクリプションに接続していない場合は、Azure アカウント資格情報の入力を求められます。
10. **[送信]** をクリックします。 送信が完了すると、[結果] ウィンドウに送信結果とジョブのリンクが示されます。
11. 最新のジョブの状態を表示して、画面を更新するには、 **[更新]** ボタンをクリックします。

### <a name="to-see-the-output"></a>出力を表示するには

1. **サーバー エクスプローラー** で、 **[Azure]** 、 **[Data Lake Analytics]** 、Data Lake Analytics アカウント、 **[ストレージ アカウント]** の順に展開し、既定のストレージを右クリックしてから **[エクスプローラー]** をクリックします。

2. [サンプル]、[出力] の順にクリックしてから、 **Drivers.csv** をダブルクリックします。

## <a name="next-steps"></a>次のステップ

* [ユーザー コードによる U-SQL 式の拡張](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [U-SQL アプリケーションを開発するための Data Lake Tools for Visual Studio の使用](data-lake-analytics-data-lake-tools-get-started.md)
