---
title: "チュートリアル: Azure Portal を使用した DevOps | Microsoft Docs"
description: "Azure ポータルでのさまざまな DevOps ワークフローについて説明します。"
services: azure-portal
documentationcenter: 
author: mlearned
manager: douge
editor: mlearned
ms.assetid: 4f1c5bc1-c732-4d35-b5df-0fd68e547d38
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 06/05/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: eec7d1402bdea4e5433c473dd713eed23aa80464
ms.lasthandoff: 03/29/2017


---
# <a name="tutorial-devops-with-the-azure-portal"></a>チュートリアル: Azure ポータルを使用した DevOps
Azure プラットフォームには、柔軟性の高い DevOps ワークフローが豊富に用意されています。 このチュートリアルでは、開発、テスト、デプロイのほか、実行中のアプリケーションのトラブルシューティング、監視、管理を行うために Azure ポータルの機能を利用する方法について説明します。 このチュートリアルでは、主に次の内容を取り上げます。

1. Web アプリの作成と継続的なデプロイの有効化
2. アプリの開発とテスト
3. アプリの監視とトラブルシューティング
4. 一般的なアプリケーション管理タスク

## <a name="creating-a-web-app-and-enabling-continuous-deployment"></a>Web アプリの作成と継続的なデプロイの有効化
[Azure App Service](https://azure.microsoft.com/services/app-service/) を使って Web アプリを作成します。作成した Web アプリはこのチュートリアルの残りの部分で使用します。 最初に、ソース コード リポジトリから実行中の Azure 環境への継続的なデプロイを有効にします。

1. Azure ポータルにサインインします。
2. **[App Services]** &gt; **追加アイコン**を選択し、名前を入力します。次に、サブスクリプションを選択し、サービスのコンテナーとして使用する新しいリソース グループを作成します。
   
   リソース グループでは、[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) を使用して、課金、デプロイ、監視などのソリューションのさまざまな面をすべて 1 つのグループとして管理できます。
   
   ![Image1][image1]
3. しばらくすると、アプリ サービスが作成されます。 少し時間を取って、サービスのさまざまなメニュー オプションをポータルで確認してください。
   
   ![Image2][image2]    
4. URL をクリックします。 さまざまなツールとリポジトリを選択できることがわかります。 また、.NET、Java、Ruby など、好みの言語とフレームワークを使用できます。
   
   ![Image3][image3]    
5. Azure ポータルを使用すれば、シンプルな数ステップの手順で継続的なデプロイを簡単に行えます。 Azure ポータルで、先ほど作成したアプリ サービスのアイコンから [設定] を選択します。
   
   ![image4][image4]
   
   右側に表示されるブレードで、[発行] セクションまでスクロールします。
   
   ![image5][image5]
6. 次に、アプリの継続的なデプロイを有効にするために、いくつかの設定を構成します。 [展開元]、[ソースの選択] の順にクリックします。 さまざまなリポジトリのソースを使用できることがわかります。
   
   ![image6][image6]
7. この例では、GitHub を選択します。 必要に応じてお好きなリポジトリを選択し、承認資格情報を設定します。
   
   ![image7][image7]
8. リポジトリに対する承認の後、デプロイするプロジェクトと分岐を選択できます。 次の一覧には架空のサンプルがいくつか示されています。
   
   ![image8][image8]
9. プロジェクトと分岐を選択してから、[OK] をクリックします。 デプロイの通知が表示されるようになります。
   
   ![image9][image9]
10. GitHub に戻り、ソース管理リポジトリと Azure を統合するために作成された webhook を確認します。 Azure Portal では、いくつかの簡単な手順のみで GitHub との統合が可能です。
    
    ![image10][image10]
11. 継続的なデプロイを実際に試すために、すぐにコンテンツをリポジトリに追加してみます。 単純な例では、サンプル テキスト ファイルを GitHub リポジトリに追加します。 App Service では、.NET、Ruby、Python、またはその他の種類のアプリケーションを自由に使用できます。 テキスト ファイル、ASP.NET MVC、Java、または Ruby のアプリケーションを好みのリポジトリに追加してかまいません。
    
    ![image11][image11]
12. リポジトリに変更をコミットした後、ポータルの通知領域で新しいデプロイが開始されるのがわかります。 リポジトリへのコミット後にすぐに変更が反映されない場合は、[同期] をクリックしてください。
    
    ![image12][image12]
13. この時点でアプリ サービスのページを読み込もうとすると、403 エラーが発生する場合があります。 この例では、index.htm ファイルまたは default.html ファイルなど、ページの一般的な既定のドキュメント設定が存在しないことが原因です。 このエラーは Azure ポータルのツールを使ってすぐに修正できます。  Azure Portal で、[設定] &gt; [アプリケーション設定] を選択します。
    
     ![image13][image13]
14. アプリケーションの設定に関するブレードが開きます。 ページの名前として「SamplePage.html」と入力し、[保存] をクリックします。 少しの間、他の設定を確認します。
    
    ![image14][image14]
15. 必要に応じてブラウザーの URL を更新し、予想される変更が表示されることを確認します。 この場合、ページにその時点で入力されている簡単なテキストが表示されます。 リポジトリに変更を加えるたびに、改めて自動デプロイが実行されます。
    
    ![image15][image15]
    
    Azure ポータルを使って継続的なデプロイを有効にするのは簡単です。 また、より複雑なリリース パイプラインを構築できるほか、Azure へのデプロイには、既存のソース管理および継続的インテグレーションのシステムと組み合わせて、他の多くの手法を使用できます (自動化されたビルドとリリース管理システムの利用など)。

## <a name="develop-and-test-an-app"></a>アプリの開発とテスト
次に、コード ベースにいくつか変更を加え、その変更を迅速にデプロイします。 また、Web アプリのパフォーマンス テストを設定します。

1. Azure ポータルのナビゲーション ウィンドウで [App Services] を選択し、目的のアプリ サービスを見つけます。
   
   ![image16][image16]
2. [ツール] をクリックします。
   
   ![image17][image17]
3. [ツール] の下の [開発] カテゴリに注目してください。 ここには、Azure ポータルから移動せずにアプリを操作できる便利なツールがいくつかあります。 [コンソール] をクリックします。
   
   ![image18][image18]
4. コンソール ウィンドウでは、アプリにライブのコマンドを発行できます。 dir コマンドを入力し、Enter キーを押します。 昇格された特権が必要なコマンドは動作しないことに注意してください。
   
   ![image19][image19]
5. [開発] カテゴリに戻り、[Visual Studio Online] を選択します。 注: Visual Studio Online は現在 Visual Studio Team Services という名前に変更されています。
   
   ![image20][image20]
6. アプリのブラウザー内編集機能をオンに切り替えます。
   
   ![image21][image21]
7. Web 拡張機能がアプリにインストールされます。 拡張機能によって Azure のアプリに迅速かつ簡単に機能が追加されます。 次のスクリーンショットには、ほかに利用可能な拡張機能の種類が示されています。
   
   ![image22][image22]
8. Visual Studio Online 拡張機能のインストール後、[移動] をクリックします。
   
   ![image23][image23]
9. ブラウザーのタブが開き、ブラウザーに直接開発 IDE が表示されます。 次の例では Chrome を使用していることがわかります。
   
   ![image24][image24]
10. ファイルの編集、ファイルとフォルダーの追加、ライブ サイトからのコンテンツのダウンロードなど、いくつかのアクティビティを実行できます。 SamplePage.html ファイルに少し編集を加えてみます。
    
    ![image25][image25]
11. しばらくすると、変更は自動的に保存されます。 ページに戻ると変更を確認できます。 このようなライブ編集は運用環境には適していない可能性が高いことを覚えておいてください。 しかし、このツールを使用すれば、開発環境とテスト環境で迅速な変更をごく簡単に行えます。
    
    ![image26][image26]
    
    ![image27][image27]
12. [ツール] ブレードの [開発] カテゴリに戻り、[パフォーマンス テスト] をクリックします。
    
    ![image28][image28]
13. Team Services アカウントを設定する必要があります。 詳細については、こちらの [Team Services アカウントの作成](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services)
14. [新規] をクリックしてパフォーマンス テストを作成します。
    
    ![image29][image29]
    
    各種の値を構成し、ダイアログの下部にある [テストの実行] をクリックして、パフォーマンス テストを開始します。
    
    ![image30][image30]
    
    ![image31][image31]
15. テストの実行が開始されると、その状態を監視できます。
    
    ![image32][image32]
    
    テストが完了したら、結果をクリックして詳細を表示します。
    
    ![image33][image33]
16. この例では小規模なテストの実行を作成したため、分析するデータが限られています。しかし、各種メトリックを確認したり、このビューからテストを再実行したりすることもできます。 Azure ポータルを利用すれば、Web パフォーマンス テストの作成、実行、分析が簡単です。 以下のスクリーンショットではパフォーマンス データが表示されています。
    
    ![image34][image34]
    
    ![image35][image35]
    
    ![image36][image36]

## <a name="monitoring-and-troubleshooting-an-app"></a>アプリの監視とトラブルシューティング
Azure には、実行中のアプリケーションを監視し、トラブルシューティングを行うための機能が多数用意されています。

1. Web アプリの Azure ポータルで [ツール] を選択します。
   
   ![image37][image37]
2. [トラブルシューティング] カテゴリには、実行中のアプリの潜在的な問題のトラブルシューティング用に各種ツールが用意されています。 ライブ HTTP トラフィックの監視、自己復旧機能の有効化、ログの表示などを実行できます。
   
   ![image38][image38]
3. [サイト メトリック] を選択し、HTTP コードの一部をすばやく表示します。
   
   ![image39][image39]
4. [サービスとしての診断] を選択します。 アプリケーションの種類を選択し、[実行] を選択します。
   
   ![image40][image40]
   
   収集が開始されます。
   
   ![image41][image41]
5. 適切なログを選択し、潜在的な問題を診断できます。 HTTP ログなどの利用可能なデータ オプションをすべて表示するために、ログを有効化する必要があります。
   
   ![image42][image42]
   
   メモリ ダンプ ファイルをクリックすることで、潜在的な問題の発見に役立つ DebugDiag 分析レポートをダウンロードして分析できます。
   
   ![image43][image43]
6. より多くのデータを表示するためには、さらにログを有効化する必要があります。 Azure ポータルで [Web アプリ] に移動し、[設定] を選択します。
   
   ![image44][image44]
7. [機能] カテゴリまで下へスクロールし、[診断ログ] を選択します。
   
      ![image45][image45]
8. ログにはさまざまなオプションがあることがわかります。 [Web サーバー ログ] をオンに切り替えて [保存] をクリックします。
   
   ![image46][image46]
9. アプリの [ツール] 領域に戻り、[サービスとしての診断] を選択します。次に、[実行] をクリックしてデータ収集をもう一度実行します。
   
   ![image47][image47]
10. HTTP ログの設定が有効になっているため、今回は HTTP ログについて収集されたデータが表示されます。
    
    ![image48][image48]
11. HTML ファイル ログをクリックすると、データが満載のレポートがブラウザーベースで作成され、詳細な調査に使用できます。
    
    ![image49][image49]
12. アプリの Azure ポータルの [ツール] セクションに戻ります。 [ツール] セクションまでスクロールし、[プロセス エクスプローラー] を選択します。
    
    ![image50][image50]
13. [プロセス エクスプローラー] を選択すると、実行中のプロセスの詳細を表示できます。 以下では、プロセスの詳細を確認できるほか、Azure ポータルからすべてのプロセスを強制終了できることがわかります。
    
    ![image51][image51]
    
    ![image52][image52]
14. 左側の [設定] ブレードに戻ります。 [新しいサポート要求] をクリックします。
    
    ![image53][image53]
15. 右側のブレードから問題の詳細を記入し、連絡先情報を入力できます。さらに、診断データをアップロードすることもできます。 Azure ポータルでは、Microsoft サポートとの連携がシームレスに行われます。
    
    ![image54][image54]
    
    ![image55][image55]
    
    Azure ポータルでは、実行中のアプリケーションの監視とトラブルシューティングに役立つ、強力でなじみのあるツール エクスペリエンスが提供されます。 また、プロセスのリサイクル、各種データ収集の有効化と無効化、Microsoft プロフェッショナル サポートとの統合などのタスクを実行することで、迅速な対応が可能です。

## <a name="general-application-management"></a>一般的なアプリケーション管理
アプリケーションを管理する場合、バックアップ戦略の構成、ID プロバイダーの実装と管理、ロールベースのアクセス制御の構成などの幅広いアクティビティを実行する必要がよくあります。 その他の DevOps エクスペリエンスと同様に、Azure プラットフォームではこれらのタスクがポータルに直接統合されています。

1. Web アプリをデータ損失から確実に保護し続けるためには、バックアップを構成する必要があります。 目的の Web アプリの [設定] 領域に移動します。
   
   ![image56][image56]
2. 右側のブレードで、[機能] カテゴリまで下へスクロールします。
   
    ![image57][image57]
3. [バックアップ] を選択すると、右側にブレードが開きます。
   
   ![image58][image58]
4. [構成] をクリックし、右側のブレードからストレージ アカウントを選択します。
   
   ![image59][image59]
5. ここで、バックアップを保持するためのストレージ コンテナーを作成、選択します。 ブレードの下部にある [作成] をクリックします。 次に、作成したコンテナーを選択します。
   
   ![image60][image60]
6. コンテナーを選択すると、スケジュールを構成できるほか、データベースのバックアップを設定できます。 このシナリオでは、[保存] アイコンをクリックします。
   
    ![image61][image61]
7. 保存後、左側の [バックアップ] ブレードまでスクロールして戻ります。 [今すぐバックアップする] をクリックして、アプリケーションをバックアップします。
   
    ![image62][image62]
8. しばらくすると、作成されたバックアップが表示されます。 次のスクリーンショットに [今すぐ復元] オプションがあることがわかります。
   
    ![image63][image63]
9. [今すぐ復元] をクリックし、右側のブレードのオプションを確認します。 必要に応じて適切なバックアップを選択し、以前の状態を簡単に復元できます。 Azure ポータルを利用して、アプリのシンプルな障害復旧戦略を簡単に有効にできました。
   
    ![image64][image64]
10. 左側の [設定] ブレードに戻り、[機能] から [認証/承認] を選択します。
    
     ![image65][image65]
11. 右側のブレードで、[App Service 認証] を選択します。 人気のプロバイダーを使って構成できる、さまざまなオプションがあることがわかります。
    
     ![image66][image66]
12. 使用するプロバイダーを選択し、スコープのオプションを確認します。 アプリ ID とアプリケーション シークレットを入力し、アプリの Facebook 認証を直ちに有効にできます。 Azure ポータルでは、アプリのターンキー ソリューションとして認証を使用できます。
    
     ![image67][image67]
13. [設定] ブレードに戻り、[リソース管理] カテゴリから [ユーザー] を選択します。
    
     ![image68][image68]
14. 右側のブレードで、ロールとユーザーの追加に関するさまざまなオプションを確認します。 Azure ポータルを使用すると、アプリケーションの RBAC (ロールベースのアクセス制御) を簡単に管理できます。
    
     ![image69][image69]

## <a name="summary"></a>概要
このチュートリアルでは、Azure プラットフォームを実際に使用してその機能の一部を説明しました。まず、Web アプリの継続的なデプロイを短時間で有効にし、さまざまなデプロイとテスト アクティビティを実行しました。その後、実行中のアプリの監視とトラブルシューティングを行い、最後に障害復旧、ID、ロールベースのアクセス制御などの主要な戦略を管理しました。 Azure プラットフォームでは、これらの DevOps ワークフローの統合されたエクスペリエンスが実現されており、手元のタスクのコンテキストを維持して効率的に作業できます。

## <a name="next-steps"></a>次のステップ
* Azure Resource Manager は、Azure プラットフォームで DevOps を有効にする場合に重要です。  詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。
* Azure App Service のデプロイの詳細については、「 [Azure App Service へのアプリのデプロイ](../app-service-web/web-sites-deploy.md)

[image1]: ./media/tutorial-azureportal-devops/image1.png
[image2]: ./media/tutorial-azureportal-devops/image2.png
[image3]: ./media/tutorial-azureportal-devops/image3.png
[image4]: ./media/tutorial-azureportal-devops/image4.png
[image5]: ./media/tutorial-azureportal-devops/image5.png
[image6]: ./media/tutorial-azureportal-devops/image6.png
[image7]: ./media/tutorial-azureportal-devops/image7.png
[image8]: ./media/tutorial-azureportal-devops/image8.png
[image9]: ./media/tutorial-azureportal-devops/image9.png
[image10]: ./media/tutorial-azureportal-devops/image10.png
[image11]: ./media/tutorial-azureportal-devops/image11.png
[image12]: ./media/tutorial-azureportal-devops/image12.png
[image13]: ./media/tutorial-azureportal-devops/image13.png
[image14]: ./media/tutorial-azureportal-devops/image14.png
[image15]: ./media/tutorial-azureportal-devops/image15.png
[image16]: ./media/tutorial-azureportal-devops/image16.png
[image17]: ./media/tutorial-azureportal-devops/image17.png
[image18]: ./media/tutorial-azureportal-devops/image18.png
[image19]: ./media/tutorial-azureportal-devops/image19.png
[image20]: ./media/tutorial-azureportal-devops/image20.png
[image21]: ./media/tutorial-azureportal-devops/image21.png
[image22]: ./media/tutorial-azureportal-devops/image22.png
[image23]: ./media/tutorial-azureportal-devops/image23.png
[image24]: ./media/tutorial-azureportal-devops/image24.png
[image25]: ./media/tutorial-azureportal-devops/image25.png
[image26]: ./media/tutorial-azureportal-devops/image26.png
[image27]: ./media/tutorial-azureportal-devops/image27.png
[image28]: ./media/tutorial-azureportal-devops/image28.png
[image29]: ./media/tutorial-azureportal-devops/image29.png
[image30]: ./media/tutorial-azureportal-devops/image30.png
[image31]: ./media/tutorial-azureportal-devops/image31.png
[image32]: ./media/tutorial-azureportal-devops/image32.png
[image33]: ./media/tutorial-azureportal-devops/image33.png
[image34]: ./media/tutorial-azureportal-devops/image34.png
[image35]: ./media/tutorial-azureportal-devops/image35.png
[image36]: ./media/tutorial-azureportal-devops/image36.png
[image37]: ./media/tutorial-azureportal-devops/image37.png
[image38]: ./media/tutorial-azureportal-devops/image38.png
[image39]: ./media/tutorial-azureportal-devops/image39.png
[image40]: ./media/tutorial-azureportal-devops/image40.png
[image41]: ./media/tutorial-azureportal-devops/image41.png
[image42]: ./media/tutorial-azureportal-devops/image42.png
[image43]: ./media/tutorial-azureportal-devops/image43.png
[image44]: ./media/tutorial-azureportal-devops/image44.png
[image45]: ./media/tutorial-azureportal-devops/image45.png
[image46]: ./media/tutorial-azureportal-devops/image46.png
[image47]: ./media/tutorial-azureportal-devops/image47.png
[image48]: ./media/tutorial-azureportal-devops/image48.png
[image49]: ./media/tutorial-azureportal-devops/image49.png
[image50]: ./media/tutorial-azureportal-devops/image50.png
[image51]: ./media/tutorial-azureportal-devops/image51.png
[image52]: ./media/tutorial-azureportal-devops/image52.png
[image53]: ./media/tutorial-azureportal-devops/image53.png
[image54]: ./media/tutorial-azureportal-devops/image54.png
[image55]: ./media/tutorial-azureportal-devops/image55.png
[image56]: ./media/tutorial-azureportal-devops/image56.png
[image57]: ./media/tutorial-azureportal-devops/image57.png
[image58]: ./media/tutorial-azureportal-devops/image58.png
[image59]: ./media/tutorial-azureportal-devops/image59.png
[image60]: ./media/tutorial-azureportal-devops/image60.png
[image61]: ./media/tutorial-azureportal-devops/image61.png
[image62]: ./media/tutorial-azureportal-devops/image62.png
[image63]: ./media/tutorial-azureportal-devops/image63.png
[image64]: ./media/tutorial-azureportal-devops/image64.png
[image65]: ./media/tutorial-azureportal-devops/image65.png
[image66]: ./media/tutorial-azureportal-devops/image66.png
[image67]: ./media/tutorial-azureportal-devops/image67.png
[image68]: ./media/tutorial-azureportal-devops/image68.png
[image69]: ./media/tutorial-azureportal-devops/image69.png

