---
title: Azure Data Lake Analytics の U-SQL ユーザー定義演算子 (UDO) の開発
description: Azure Data Lake Analytics ジョブで使用および再使用されるユーザー定義演算子の開発方法について説明します。
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 122a4b6af78a22f74d5057da75767077f8d9b978
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496148"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>U-SQL ユーザー定義演算子 (UDO) の開発
このアーティクルでは、U-SQL ジョブ内のデータを処理するユーザー定義演算子を開発する方法について説明します。

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>U-SQL でのユーザー定義演算子の定義と使用
**U-SQL ジョブを作成して送信するには**

1. Visual Studio で、**[ファイル]、[新規作成]、[プロジェクト]、[ U-SQL プロジェクト]** の順に選択します。
2. Click **OK**. Visual Studio によって、Script.usql ファイルを使用するソリューションが作成されます。
3. **ソリューション エクスプローラー**で、Script.usql を展開してから **Script.usql.cs** をダブルクリックします。
4. 以下のコードをファイルに貼り付けます。

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
6. **Script.usql** を開き、次の U-SQL スクリプトを貼り付けます。

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
7. Data Lake Analytics アカウント、データベース、スキーマを指定します。
8. **ソリューション エクスプローラー**で **Script.usql** を右クリックし、**[スクリプトのビルド]** をクリックします。
9. **ソリューション エクスプローラー**で **Script.usql** を右クリックし、**[スクリプトの送信]** をクリックします。
10. Azure サブスクリプションに接続していない場合は、Azure アカウント資格情報の入力を求められます。
11. **[送信]** をクリックします。 送信が完了すると、[結果] ウィンドウに送信結果とジョブのリンクが示されます。
12. 最新のジョブの状態を表示して、画面を更新するには、**[更新]** ボタンをクリックします。

**出力を表示するには**

1. **サーバー エクスプローラー**で、**[Azure]**、**[Data Lake Analytics]**、Data Lake Analytics アカウント、**[ストレージ アカウント]** の順に展開し、既定のストレージを右クリックしてから **[エクスプローラー]** をクリックします。
2. [サンプル]、[出力] の順にクリックしてから、 **Drivers.csv**をダブルクリックします。

## <a name="see-also"></a>関連項目
* [ユーザー コードによる U-SQL 式の拡張](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [U-SQL アプリケーションを開発するための Data Lake Tools for Visual Studio の使用](data-lake-analytics-data-lake-tools-get-started.md)
