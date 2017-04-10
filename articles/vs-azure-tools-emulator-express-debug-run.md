---
title: "Emulator Express を使用したローカル コンピューターでの Azure クラウド サービスの実行とデバッグ | Microsoft Docs"
description: "Emulator Express を使用したローカル コンピューターでのクラウド サービス実行とデバッグ"
services: visual-studio-online
documentationcenter: n/a
author: TomArcher
manager: douge
editor: 
ms.assetid: 73108f98-a552-4817-b7a1-551367b71906
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 882b0b78f94e220e8a94ee3b614c575b509a8170
ms.lasthandoff: 03/27/2017


---
# <a name="using-emulator-express-to-run-and-debug-an-azure-cloud-service-on-a-local-machine"></a>Emulator Express を使用したローカル コンピューターでの Azure クラウド サービスの実行とデバッグ
Emulator Express を使用することにより、管理者として Visual Studio を実行せずに、クラウド サービスをテストおよびデバッグできます。 クラウド サービスの要件に応じて、Emulator Express または完全なエミュレーターのどちらを使用するかをプロジェクト設定で指定できます。 完全なエミュレーターの詳細については、「[コンピューティング エミュレーターでの Azure アプリケーションの実行](storage/storage-use-emulator.md)」を参照してください。

## <a name="using-emulator-express-in-visual-studio"></a>Visual Studio での Emulator Express の使用
Azure SDK 2.3 以降で Azure プロジェクトを作成すると、Emulator Express が自動的に使用されます。 以前のバージョンの Azure SDK で作成された既存のプロジェクトは、次の手順に従って Emulator Express を選択します。

1. Visual Studio で Azure クラウド サービス プロジェクトを開くか新たに作成します。

1. **ソリューション エクスプローラー**でそのプロジェクトを右クリックし、コンテキスト メニューから **[プロパティ]** を選択します。

1. プロジェクト プロパティのページで、**[Web]** タブを選択します。

    ![Azure クラウド サービス プロジェクトのプロパティ](./media/vs-azure-tools-emulator-express-debug-run/web-properties.png)

1. **[ローカル開発サーバー]** で、**[IIS Express を使用する]** オプションを選択します。

1. **[エミュレーター]** で、**[Emulator Express を使用する]** を選択します。
   
1. Emulator Express を起動するには、コマンド プロンプトで次のコマンドを実行します。 

    ```
    csrun.exe /useemulatorexpress
    ```

## <a name="emulator-express-limitations"></a>Emulator Express の制限事項
Emulator Express には、次のような制限事項があることがわかっています。 

- Emulator Express は IIS Web サーバーと互換性がありません。
- クラウド サービスには複数のロールを含めることができますが、各ロールのインスタンスは 1 つに制限されます。
- 1,000 未満のポート番号にはアクセスできません。 通常 1,000 未満のポートが使用される認証プロバイダーを使用する場合は、この値を 1,000 を超えるポート番号に変更する必要がある場合があります。
- Azure コンピューティング エミュレーターに適用されるすべての制限は、Emulator Express にも適用されます。 たとえば、デプロイあたり 50 を超えるロール インスタンスを保持できません。 Azure コンピューティング エミュレーターの詳細については、「[クラウド サービスのパフォーマンスのテスト](http://go.microsoft.com/fwlink/p/?LinkId=623050)」をご覧ください。

## <a name="next-steps"></a>次のステップ
[Azure Cloud Services のデバッグ](https://msdn.microsoft.com/library/azure/ee405479.aspx)

