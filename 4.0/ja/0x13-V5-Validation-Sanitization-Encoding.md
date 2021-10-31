# V5 バリデーション、サニタイゼーション、エンコーディング

## 管理目標

最も一般的な Web アプリケーションセキュリティ脆弱性は、出力エンコーディングなしで直接使用する前に、クライアントまたは環境からの入力を正しく妥当性確認できていないことです。この脆弱性は、クロスサイトスクリプティング (XSS) 、SQL インジェクション、インタプリタインジェクション、ロケール/Unicode 攻撃、ファイルシステム攻撃、バッファオーバーフローなど、Web アプリケーションにおける重大な脆弱性のほとんどすべてにつながります。

検証対象のアプリケーションが以下の上位要件を満たすことを確認します。

* 入力バリデーションと出力エンコーディングアーキテクチャはインジェクション攻撃を防ぐために合意されたパイプラインを持っています。
* 入力データは厳密な型付け、妥当性確認、範囲や長さのチェック、または最低でもサニタイズまたはフィルタリングされています。
* 出力データは可能な限りインタプリタに近いデータのコンテキストごとにエンコードまたはエスケープされています。

現代の Web アプリケーションアーキテクチャでは、出力エンコーディングがこれまで以上に重要です。特定のシナリオでは堅牢な入力バリデーションを提供することは困難であるため、パラメータ化されたクエリ、自動エスケープテンプレートフレームワークなどの安全な API の使用や、注意深く選択された出力エンコーディングがアプリケーションのセキュリティにとって重要です。

## V5.1 入力バリデーション

ポジティブ許可リストと強いデータ型付けを使用して、入力バリデーション制御を正しく実装すると、すべてのインジェクション攻撃の 90% 以上を排除できます。長さと範囲のチェックはこれをさらに減らすことができます。アプリケーションアーキテクチャ、デザインスプリント、コーディング、ユニットテスト、統合テストを通してセキュアな入力バリデーションを構築することが求められています。これらの項目の多くはペネトレーションテストでは見つけることができませんが、それらを実装しないことの結果は V5.3 出力エンコーディングおよびインジェクション防止要件にあります。開発者およびセキュアコードレビュー担当者は、インジェクションを防ぐためにすべての項目に L1 が要求されているものとして、このセクションを扱うことをお勧めします。

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **5.1.1** | 特にアプリケーションフレームワークがリクエストパラメータ (GET, POST, cookie, ヘッダ, 環境変数) のソースについて判別していない場合、アプリケーションが HTTP パラメータ汚染攻撃を防御している。 | ✓ | ✓ | ✓ | 235 |
| **5.1.2** | フレームワークがマスパラメータアサインメント攻撃から保護されている、またはフィールドにプライベートなどのマークをつけるなど、危険なパラメータアサインメントから保護するための対策がアプリケーションにある。 ([C5](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 915 |
| **5.1.3** | すべての入力 (HTML フォームフィールド, REST リクエスト, URL パラメータ, HTTP ヘッダ, cookie, バッチファイル, RSS フィードなど) がポジティブバリデーション (許可リスト) を使用して確認されている。 ([C5](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 20 |
| **5.1.4** | 許可された文字、長さ、パターンなど、定義されたスキーマに対して構造化データが強く型付けおよび妥当性確認されている (例えば、クレジットカード番号、電子メールアドレス、電話番号、または住所と郵便番号の一致などの二つの関連するフィールドが妥当であることの確認) 。 ([C5](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 20 |
| **5.1.5** | URL リダイレクトおよびフォワードが許可リストにある宛先のみを許可すること、または潜在的に信頼できないコンテンツにリダイレクトするときに警告を表示する。 | ✓ | ✓ | ✓ | 601 |

## V5.2 サニタイゼーションおよびサンドボックス

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **5.2.1** | WYSIWYG エディタなどからの信頼できない HTML 入力はすべて、HTML サニタイザーライブラリまたはフレームワーク機能で適切にサニタイズされている。 ([C5](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 116 |
| **5.2.2** | 許可されている文字や長さなどの安全対策を強化するために、非構造化データがサニタイズされている。 | ✓ | ✓ | ✓ | 138 |
| **5.2.3** | SMTP インジェクションや IMAP インジェクションから保護するため、ユーザ入力をメールシステムに渡す前にアプリケーションがサニタイズしている。 | ✓ | ✓ | ✓ | 147 |
| **5.2.4** | アプリケーションが eval() や他の動的コード実行機能の使用を回避している。代替手段がない場合には、含まれているユーザ入力を実行する前にサニタイズまたはサンドボックス化する必要がある。 | ✓ | ✓ | ✓ | 95 |
| **5.2.5** | 含まれているユーザ入力がサニタイズまたはサンドボックス化されていることを確認して、アプリケーションがテンプレートインジェクション攻撃から保護している。 | ✓ | ✓ | ✓ | 94 |
| **5.2.6** | ファイル名や URL 入力フィールドなどの信頼できないデータや HTTP ファイルメタデータを妥当性確認またはサニタイズする、およびプロトコル、ドメイン、パス、ポートの許可リストを使用することにより、アプリケーションが SSRF 攻撃から保護している。 | ✓ | ✓ | ✓ | 918 |
| **5.2.7** | アプリケーションはユーザが提供する Scalable Vector Graphics (SVG) スクリプト可能コンテンツ、特にインラインスクリプトによる XSS や foreignObject に関するものをサニタイズ、無効化、またはサンドボックス化する。 | ✓ | ✓ | ✓ | 159 |
| **5.2.8** | アプリケーションは、マークダウン、CSS や XSL スタイルシート、BBCode などのユーザが提供するスクリプト可能コンテンツまたは式テンプレート言語コンテンツをサニタイズ、無効化、またはサンドボックス化する。 | ✓ | ✓ | ✓ | 94 |

## V5.3 出力エンコーディングおよびインジェクション防止

使用中のインタプリタに近いまたは隣接する出力エンコーディングはあらゆるアプリケーションのセキュリティにとって重要です。通常、出力エンコーディングは永続化されませんが、一時的な使用のために適切な出力コンテキストで安全に出力を描画するために使用されます。出力のエンコードに失敗すると、セキュアではなく、インジェクション可能で、危険なアプリケーションとなります。

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **5.3.1** | 出力エンコーディングは要求されているインタプリタやコンテキストに応じたものである。例えば、具体的には HTML 値、HTML 属性、JavaScript、URL パラメータ、HTTP ヘッダ、SMTP、およびコンテキストの要求に応じて、特に信頼できない入力 (例えば、ねこ や O'Hara など、Unicode やアポストロフィを持つ名前) などにエンコーダを使用する。 ([C4](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 116 |
| **5.3.2** | すべての Unicode 文字ポイントが有効で安全に処理されるように、出力エンコーディングはユーザが選択した文字セットとロケールを保持している。 ([C4](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 176 |
| **5.3.3** | コンテキストを意識した (できれば自動化された、あるいは最悪でも手動による) 出力エスケープが反射型 XSS、ストア型 XSS、DOM ベース XSS から保護する。 ([C4](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 79 |
| **5.3.4** | データ選択またはデータベースクエリ (SQL, HQL, ORM, NoSQL など) がパラメータ化クエリ、ORM、エンティティフレームワークを使用している、またはデータベースインジェクション攻撃から保護されている。 ([C3](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 89 |
| **5.3.5** | パラメータ化またはより安全なメカニズムが存在しない場合には、SQL インジェクションから保護するために SQL エスケープを使用するなど、インジェクション攻撃から保護するためにコンテキスト固有の出力エンコーディングが使用されている。 ([C3, C4](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 89 |
| **5.3.6** | アプリケーションが JSON インジェクション攻撃、JSON eval 攻撃、および JavaScript 式評価から保護する。 ([C4](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 830 |
| **5.3.7** | アプリケーションが LDAP インジェクション脆弱性から保護する、または LDAP インジェクションを防ぐために特定のセキュリティ管理策が実装されている。 ([C4](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 90 |
| **5.3.8** | アプリケーションが OS コマンドインジェクションから保護する、およびオペレーティングシステムがパラメータ化 OS クエリを使用するか、コンテキストに応じたコマンドライン出力エンコーディングを使用する。 ([C4](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 78 |
| **5.3.9** | アプリケーションがローカルファイルインクルージョン (LFI) やリモートファイルインクルージョン (RFI) 攻撃から保護する。 | ✓ | ✓ | ✓ | 829 |
| **5.3.10** | アプリケーションが XPath インジェクションや XML インジェクション攻撃から保護する。 ([C4](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 643 |

注意: パラメータ化クエリや SQL エスケープの使用は必ずしも十分ではありません。テーブル名や列名、ORDER BY などはエスケープできません。これらのフィールドにエスケープされたユーザ指定データを含めると、クエリの失敗または SQL インジェクションを引き起こします。

注意: SVG フォーマットはほとんどすべてのコンテキストで ECMA スクリプトを明示的に許可しているため、すべての SVG XSS ベクトルを完全にブロックすることはできないかもしれません。SVG アップロードが必要な場合には、これらのアップロードされたファイルを text/plain として提供するか、アプリケーションを奪う XSS の成功を別のユーザ提供コンテンツドメインを使用して防ぐことを強くお勧めします。

## V5.4 メモリ、文字列、アンマネージコード

以下の要件はアプリケーションがシステム言語またはアンマネージコードを使用する場合にのみ適用されます。

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **5.4.1** | アプリケーションがメモリセーフな文字列、安全なメモリコピーおよびポインタ演算を使用して、スタック、バッファ、またはヒープオーバーフローを検出または防止する。 | | ✓ | ✓ | 120 |
| **5.4.2** | フォーマット文字列が潜在的に悪意のある入力を受け取らず、固定である。 | | ✓ | ✓ | 134 |
| **5.4.3** | 整数オーバーフローを防ぐために符号、範囲、および入力バリデーション技法が使用されている。 | | ✓ | ✓ | 190 |

## V5.5 逆シリアル化防止

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **5.5.1** | 悪意のあるオブジェクト生成やデータ改竄を防ぐために、シリアル化オブジェクトが整合性チェックを使用しているか、暗号化されている。 ([C5](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 502 |
| **5.5.2** | アプリケーションが XML パーサーを最も制限の厳しい構成のみを使用するよう適切に制限し、XML 外部エンティティ (XML eXternal Entity, XXE) 攻撃を防止するために外部エンティティの解決などの危険な機能を無効にする。 | ✓ | ✓ | ✓ | 611 |
| **5.5.3** | 信頼できないデータの逆シリアル化が回避されている、またはカスタムコードとサードパーティライブラリ (JSON, XML および YAML パーサーなど) の両方で保護されている。 | ✓ | ✓ | ✓ | 502 |
| **5.5.4** | ブラウザまたは JavaScript ベースのバックエンドで JSON をパースする場合には、JSON ドキュメントのパースに JSON.parse が使用されている。JSON のパースに eval() を使用してはいけない。 | ✓ | ✓ | ✓ | 95 |

## 参考情報

詳しくは以下の情報を参照してください。

* [OWASP Testing Guide 4.0: Input Validation Testing](https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/07-Input_Validation_Testing/README.html)
* [OWASP Cheat Sheet: Input Validation](https://cheatsheetseries.owasp.org/cheatsheets/Input_Validation_Cheat_Sheet.html)
* [OWASP Testing Guide 4.0: Testing for HTTP Parameter Pollution](https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/07-Input_Validation_Testing/04-Testing_for_HTTP_Parameter_Pollution.html)
* [OWASP LDAP Injection Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/LDAP_Injection_Prevention_Cheat_Sheet.html)
* [OWASP Testing Guide 4.0: Client Side Testing](https://owasp.org/www-project-web-security-testing-guide/latest/4-Web_Application_Security_Testing/11-Client_Side_Testing/)
* [OWASP Cross Site Scripting Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html)
* [OWASP DOM Based Cross Site Scripting Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/DOM_based_XSS_Prevention_Cheat_Sheet.html)
* [OWASP Java Encoding Project](https://owasp.org/owasp-java-encoder/)
* [OWASP Mass Assignment Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Mass_Assignment_Cheat_Sheet.html)
* [DOMPurify - Client-side HTML Sanitization Library](https://github.com/cure53/DOMPurify)
* [XML External Entity (XXE) Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/XML_External_Entity_Prevention_Cheat_Sheet.html)

自動エスケープの詳細については、以下を参照してください。

* [Reducing XSS by way of Automatic Context-Aware Escaping in Template Systems](https://googleonlinesecurity.blogspot.com/2009/03/reducing-xss-by-way-of-automatic.html)
* [AngularJS Strict Contextual Escaping](https://docs.angularjs.org/api/ng/service/$sce)
* [AngularJS ngBind](https://docs.angularjs.org/api/ng/directive/ngBind)
* [Angular Sanitization](https://angular.io/guide/security#sanitization-and-security-contexts)
* [Angular Security](https://angular.io/guide/security)
* [ReactJS Escaping](https://reactjs.org/docs/introducing-jsx.html#jsx-prevents-injection-attacks)
* [Improperly Controlled Modification of Dynamically-Determined Object Attributes](https://cwe.mitre.org/data/definitions/915.html)

逆シリアル化の詳細については、以下を参照してください。

* [OWASP Deserialization Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Deserialization_Cheat_Sheet.html)
* [OWASP Deserialization of Untrusted Data Guide](https://owasp.org/www-community/vulnerabilities/Deserialization_of_untrusted_data)
