# V15 セキュアコーディングとアーキテクチャ

## 管理目標

多くの ASVS 要件は、認証や認可といった特定のセキュリティ領域に関連しているか、ログ記録やファイル処理などの特定のタイプのアプリケーション機能に関係しています。

この章ではアプリケーションの設計と開発の際に考慮すべき、一般的なセキュリティ要件を提供します。これらの要件は、クリーンなアーキテクチャとコード品質だけでなく、アプリケーションセキュリティに必要となる特定のアーキテクチャとコーディングプラクティスについても焦点を当てています。

## V15.1 セキュアコーディングとアーキテクチャドキュメント

安全で防御力の高いアーキテクチャを確立するための要件の多くは、特定のセキュリティコントロールの実装とアプリケーション内で使用されるコンポーネントに関する決定を明確に文書化することに依存します。

このセクションでは、「危険な機能」を含むとみなされるコンポーネントや「リスクのあるコンポーネント」と考えられるコンポーネントの特定など、ドキュメント要件について概説します。

「危険な機能」を持つコンポーネントとは、信頼できないデータのデシリアライゼーション、生ファイルやバイナリデータの解析、動的コード実行、直接メモリ操作などの操作を実行する、内部開発またはサードパーティのコンポーネントを指します。これらのタイプの操作の脆弱性は、アプリケーションを侵害し、基盤となるインフラストラクチャを開示する可能性のある高いリスクをもたらします。

「リスクのあるコンポーネント」とは、開発プロセスや機能に関するセキュリティコントロールが欠如しているか適切に実装されていないサードパーティライブラリ (つまり、内部開発ではないもの) を指します。たとえば、保守が不十分であったり、サポートされていなかったり、サポート終了段階にあったり、重大な脆弱性の経歴があるコンポーネントなどがあります。

また、このセクションでは、サードパーティコンポーネントの脆弱性に対処するための適切な期間を定義することの重要性も強調しています。

| # | 説明 | レベル |
| :---: | :--- | :---: |
| **15.1.1** | アプリケーションドキュメントでは、脆弱性があるサードパーティコンポーネントのバージョンやライブラリ全般の更新について、リスクに基づく修復時間枠を定義して、これらのコンポーネントからのリスクを最小限に抑えている。 | 1 |
| **15.1.2** | 使用しているすべてのサードパーティライブラリについて、ソフトウェア部品表 (SBOM) などのインベントリカテゴリを保守している。コンポーネントは事前定義済みで信頼でき、継続的に保守されているリポジトリから取得している。 | 2 |
| **15.1.3** | アプリケーションドキュメントでは、時間のかかる機能やリソースを必要とする機能を特定している。これには、この機能の過剰使用による可用性の損失を防ぐ方法や、レスポンスの構築にコンシューマのタイムアウトよりも長い時間がかかる状況を避ける方法を含む必要がある。可能性のある防御策としては、非同期処理、キューの使用、ユーザごとおよびアプリケーションごとの並列処理の制限などがある。 | 2 |
| **15.1.4** | アプリケーションドキュメントでは「リスクのあるコンポーネント」とみなされるサードパーティライブラリを強調している。 | 3 |
| **15.1.5** | アプリケーションドキュメントでは「危険な機能」が使用されているアプリケーションのパーツを強調している。 | 3 |

## V15.2 セキュリティアーキテクチャと依存関係

このセクションでは、依存関係管理を通じてリスクのある、古い、または安全でない依存関係とコンポーネントを処理するための要件を含みます。

また、サンドボックス化、カプセル化、コンテナ化、ネットワーク分離などのアーキテクチャレベルの技法を使用して、「危険な操作」や「リスクのあるコンポーネント」 (前のセクションで定義) の使用による影響を軽減し、リソースを必要とする機能の過剰使用による可用性の損失を防ぐことも含みます。

| # | 説明 | レベル |
| :---: | :--- | :---: |
| **15.2.1** | アプリケーションは、文書化された更新および修復の時間枠に違反していないコンポーネントのみを含む。 | 1 |
| **15.2.2** | アプリケーションは、これに関する文書化されたセキュリティ上の決定と戦略に基づいて、時間のかかる機能やリソースを必要とする機能による可用性の損失に対する防御策を実装している。 | 2 |
| **15.2.3** | 本番環境にはアプリケーションの機能に必要な機能のみを含み、テストコード、サンプルスニペット、開発機能などの不要な機能を公開していない。 | 2 |
| **15.2.4** | サードパーティコンポーネントとそのすべての推移的依存関係は、内部所有であるか外部ソースであるかに関わらず、想定されたリポジトリから組み込まれており、依存関係攪乱攻撃のリスクはない。 | 3 |
| **15.2.5** | アプリケーションは、「危険な機能」を含む、あるいは「リスクのあるコンポーネント」とみなされるサードパーティライブラリを使用していると文書化されているアプリケーションのパーツに対して、追加の保護を実装している。これは、サンドボックス化、カプセル化、コンテナ化、ネットワークレベルの分離などの技法を含み、アプリケーションの一部分を侵害した攻撃者がアプリケーションの他の部分に侵入するのを遅らせたり阻止する。 | 3 |

## V15.3 防御的コーディング

このセクションでは、型ジャグリング、プロトタイプ汚染など、特定の言語における安全でないコーディングパターンの使用に起因する脆弱性の種類について説明します。一部はすべての言語には関係しないかもしれない一方で、他のものは言語固有の修正があったり、特定の言語やフレームワークが HTTP パラメータなどの機能を処理する方法に関係するかもしれません。また、アプリケーションの更新を暗号的に検証しないことのリスクも考慮します。

また、オブジェクトを使用してデータ項目を表現し、外部 API を介してこれらを受け入れ、返す際に関連するリスクも考慮します。この場合、アプリケーションは書き込み不可のデータフィールドがユーザ入力 (マスアサインメント) によって変更されないことと、API が返されるデータフィールドについて選択的であることを確保する必要があります。フィールドへのアクセスがユーザのパーミッションに依存する場合、これは認可の章にあるフィールドレベルのアクセス制御要件のコンテキストで考慮する必要があります。

| # | 説明 | レベル |
| :---: | :--- | :---: |
| **15.3.1** | アプリケーションはデータオブジェクトからフィールドの必要なサブセットのみを返す。たとえば、個別のフィールドの中にはユーザーがアクセスすべきではないものがあるため、データオブジェクト全体を返すべきではない。 | 1 |
| **15.3.2** | アプリケーションバックエンドが外部 URL を呼び出す場合、意図した機能でない限りリダイレクトに従わないように設定されている。 | 2 |
| **15.3.3** | アプリケーションには、コントローラやアクションごとに許可されるフィールドを制限することで、マスアサインメント攻撃から保護する対策がある。たとえば、そのアクションの一部となることを意図していないフィールド値を挿入または更新することはできない。 | 2 |
| **15.3.4** | すべてのプロキシとミドルウェアコンポーネントは、エンドユーザが操作できない信頼できるデータフィールドを使用してユーザのオリジナル IP アドレスを正しく転送し、アプリケーションと Web サーバは、動的 IP、VPN、企業のファームウェアによってオリジナル IP アドレスが信頼できない可能性があることを考慮して、ログ記録や、レート制限などのセキュリティ決定にこの正しい値を使用している。 | 2 |
| **15.3.5** | アプリケーションは変数が正しい型であることを明示的に保証し、厳密な等価演算と比較演算を実行している。これは、アプリケーションコードが変数の型について仮定することによって引き起こされる型ジャグリングや型コンフュージョンの脆弱性を回避するためである。 | 2 |
| **15.3.6** | JavaScript コードはオブジェクトリテラルの代わりに Set() や Map() を使用するなど、プロトタイプ汚染を防ぐ方法で記述している。 | 2 |
| **15.3.7** | アプリケーションが HTTP 変数汚染攻撃に対する防御策を備えている。特にアプリケーションフレームワークがリクエストパラメータのソース (クエリ文字列、ボディパラメータ、クッキー、ヘッダフィールド) を区別しない場合はこの防御が必要である。 | 2 |

## V15.4 安全な同時並行性

競合状態、チェック時間と使用時間 (TOCTOU) の脆弱性、デッドロック、ライブロック、スレッド枯渇、不適切な同期などの同時並行性の問題は、予期しない動作やセキュリティリスクにつながる可能性があります。このセクションではこれらのリスクを緩和するためのさまざまな技法と戦略を説明します。

| # | 説明 | レベル |
| :---: | :--- | :---: |
| **15.4.1** | マルチスレッドコード内の共有オブジェクト (キャッシュ、ファイル、複数のスレッドによってアクセスするメモリ内オブジェクトなど) は、スレッドセーフな型と、ロックやセマフォなどの同期メカニズムを使用して安全にアクセスして、競合状態やデータ破損を回避している。 | 3 |
| **15.4.2** | リソースの存在やパーミッションなどのリソースの状態のチェックとそれらに依存するアクションは、単一のアトミック操作として実行され、チェック時間と使用時間 (TOCTOU) の競合状態を防いでいる。たとえば、ファイルを開く前にそのファイルが存在するかどうかをチェックしたり、ユーザのアクセスを付与する前に検証している。 | 3 |
| **15.4.3** | ロックは一貫して使用され、互いに待機したり無限に再試行することによってスレッドがスタックすることを避けている。また、ロックロジックはリソースの管理を担当するコード内にとどめ、ロックが外部のクラスやコードによって不注意に、あるいは悪意を持って変更されないようにしている。 | 3 |
| **15.4.4** | リソース割り当てのポリシーは、スレッドプールを活用するなどしてリソースへの公平なアクセスを確保し、優先度の低いスレッドが妥当な時間枠内で処理を進められるようにすることで、スレッドの枯渇を防いでいる。 | 3 |

## 参考情報

詳しくは以下の情報を参照してください。

* [OWASP Prototype Pollution Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Prototype_Pollution_Prevention_Cheat_Sheet.html)
* [OWASP Mass Assignment Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Mass_Assignment_Cheat_Sheet.html)
* [OWASP CycloneDX Bill of Materials Specification](https://owasp.org/www-project-cyclonedx/)
* [OWASP Web Security Testing Guide: Testing for HTTP Parameter Pollution](https://owasp.org/www-project-web-security-testing-guide/stable/4-Web_Application_Security_Testing/07-Input_Validation_Testing/04-Testing_for_HTTP_Parameter_Pollution)
