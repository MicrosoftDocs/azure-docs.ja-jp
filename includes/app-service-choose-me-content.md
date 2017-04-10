<a name="tellmeas"></a>

## <a name="tell-me-about-app-service"></a>App Service について
Azure Virtual Machines では、幅広いクラウド ホスティング タスクを処理できます。 ただし、VM インフラストラクチャを作成して管理するには、特別なスキルと多くの労力が必要です。 お客様の Web Apps、Mobile App バックエンド、API apps を実行する VM を完全にコントロールする必要がない場合は、 *サービスとしてのプラットフォーム* (PaaS) という、使いやすい (かつ安価な) ソリューションがあります。 PaaS では、アプリケーションを実行する VM に対する管理作業のほとんどを Azure が処理します。 [Azure App Service](../articles/app-service/app-service-value-prop-what-is.md) は、エンタープライズ レベルのアプリの構築、デプロイ、拡張を秒単位で実行できる完全管理された PaaS 製品です。

App Service は、さまざまな種類のアプリケーションのワークロードに最適な選択肢です。 企業は、毎週数百万回のヒットに対応できる商用 Web サイトの構築や移行を望み、世界中に複数のデータセンターをデプロイすることがあります。 さらに、同じ会社内に、会社の Active Directory の認証ユーザーに対する経費報告書を追跡する事業部門アプリも存在することがあり、そのアプリにはモバイル デバイスのコンポーネントがあり、オンプレミス リソースやビジネス プロセスに接続される可能性があります。 この経費報告書が、大量の情報を計算して集計するための定期的なバックグラウンド ジョブを必要とすることがあります。 IT コンサルタントが、一般的なオープン ソース アプリケーションを採用して、スモール ビジネス用のコンテンツ管理システムをセットアップすることがあります。 次の図に、Azure App Service で実行できる Web apps の一部を示します。

<a name="appservice_diagram"></a>
![アプリ サービス図](media/app-service-choose-me-content/diagram.png)

**図 : Azure App Service は静的 Web ページ、一般的な Web アプリケーション、さまざまなテクノロジで構築されたカスタム Web アプリケーションをサポートしている。さらに、(Web ジョブを使用して) モバイル バックエンド、API アプリ、Web 以外のコンピューティング ワークロードを実行できます。**

Azure App Service なら、 [Web ジョブ](../articles/app-service-web/websites-webjobs-resources.md) 機能を使用して、あらゆる種類のコンピューティング ワークロードを実行できます。

Azure App Service では、複数のユーザーによって作成された複数のアプリを含む共有 VM 上で実行するか、専用の VM 上で実行するかを選択できます。 VM は Azure App Service によって管理されているリソースのプールの一部であり、高い信頼性とフォルト トレランスを可能にします。

また、簡単に使い始めることができます。 Azure App Service では、ユーザーはさまざまなアプリケーション、フレームワーク、テンプレートを選択することで、わずか数秒で Web アプリを作成できます。 また、好みの開発ツール (WebMatrix、Visual Studio、その他のエディター) とソース管理オプションを使用して継続的な統合をセットアップし、チームとして開発することができます。 MySQL DB に依存するアプリケーションは、Microsoft パートナーの ClearDB が Azure 向けに提供している MySQL サービスを使用できます。

開発者はAzure App Service を使用して、大規模でスケーラブルな Web アプリケーションを作成できます。 このテクノロジは、ASP.NET、PHP、Node.js、Python を使用したアプリケーションの作成をサポートします。 アプリケーションはスティッキー セッションなどを使用でき、多数の既存の Web Apps を変更なしにこのクラウド プラットフォームに移動できます。 Azure App Service 上に構築された Web Apps は、Service Bus、SQL Database、Blob Storage など、Azure の他の要素を自由に使用できます。 また、アプリケーションの複数のコピーを別の VM で実行できます。 Azure App Service なら、VM 間で要求の負荷分散が自動的に実行されます。 新しい Web アプリ インスタンスは既存の VM 内に作成されるため、アプリケーションの新しいインスタンスを瞬時に開始できます。つまり、VM の新規作成を待つよりもはるかに高速です。

次の [図](#appservice_diagram) に示すように、コードやその他の Web コンテンツを Azure App Service に発行する方法は複数あります。 たとえば、FTP、FTPS、Microsoft の WebDeploy テクノロジを使用できます。 Azure App Service は、ソース管理システム (Git、GitHub、CodePlex、BitBucket、Dropbox、Mercurial、Team Foundation Server、クラウドベースの Team Foundation Service など) からのコードの発行もサポートしています。



<!--HONumber=Jan17_HO3-->


