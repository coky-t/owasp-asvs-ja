# V8 データ保護

## 管理目標

堅実なデータ保護には機密性、完全性、可用性 (CIA) という三つの重要な要素があります。この標準規格では、堅牢化され十分な保護が施されているサーバなど、信頼できるシステムでデータ保護が実施されていることを前提としています。

アプリケーションはすべてのユーザデバイスが何らかの方法で危殆化されていると想定する必要があります。共有コンピュータ、スマホ、タブレットなどのセキュアでないデバイスにアプリケーションが機密情報を転送または保存する場合、アプリケーションはこれらのデバイスに保存されたデータが暗号化され、容易かつ不正に入手、改竄、開示されないようにする責任があります。

検証対象のアプリケーションが以下の上位のデータ保護要件を満たすことを確認します。

* 機密性: データは転送時および保存時のいずれにおいても不正な閲覧や開示から保護されるべきです。
* 完全性: データは認可されていない攻撃者による偽造、改竄、削除から保護されるべきです。
* 可用性: データは認可されたユーザに必要に応じて利用可能であるべきです。

## V8.1 一般的なデータ保護

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **8.1.1** | アプリケーションは機密データをロードバランサやアプリケーションキャッシュなどのサーバコンポーネントにキャッシュされないように保護している。 | | ✓ | ✓ | 524 |
| **8.1.2** | サーバ上で保存されている機密データのすべてのキャッシュや一時コピーが、不正なアクセスから保護されているか、認可されたユーザが機密データにアクセスした後に削除または無効化される。 | | ✓ | ✓ | 524 |
| **8.1.3** | アプリケーションが、隠しフィールド、Ajax 変数、Cookie 、ヘッダ値など、リクエスト内のパラメータ数を最小限に抑えている。 | | ✓ | ✓ | 233 |
| **8.1.4** | アプリケーションが、IP 、ユーザ、1時間または1日あたりの合計数、またはアプリケーションにとって意味のあることなど、異常な数の要求を検出および警告できる。 | | ✓ | ✓ | 770 |
| **8.1.5** | 重要なデータの定期的なバックアップが実行され、データのテスト復元が実行されている。 | | | ✓ | 19 |
| **8.1.6** | データの盗難や破損を防ぐために、バックアップがセキュアに保存されている。 | | | ✓ | 19 |

## V8.2 クライアント側のデータ保護

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **8.2.1** | 機密データが現代のブラウザでキャッシュされないように、アプリケーションは十分なキャッシュ防止ヘッダを設定している。 | ✓ | ✓ | ✓ | 525 |
| **8.2.2** | [修正] ブラウザストレージ (localStorage, sessionStorage, IndexedDB, Cookie など) に保存されているデータに、セッショントークンを除いて、機密データが含まれていない。 | ✓ | ✓ | ✓ | 922 |
| **8.2.3** | クライアントやセッションが終了した後、ブラウザ DOM などのクライアントストレージから認証データがクリアされる。 | ✓ | ✓ | ✓ | 922 |

## V8.3 機密性の高い個人データ

このセクションは、特に大量の場合に、機密データを許可なく作成、読み取り、更新、削除することから保護するのに役立ちます。

このセクションへの準拠は V4 アクセス制御、特に V4.2 への準拠を意味します。例えば、許可されていない更新や機密性の高い個人情報の開示から保護するには V4.2.1 を順守する必要があります。完全に網羅するにはこのセクションと V4 に従ってください。

注意: オーストラリアのプライバシー原則 APP-11 や GDPR などのプライバシー規制や法令は、機密性の高い個人情報の保存、使用、および転送の実装にアプリケーションがどのようにアプローチする必要があるかに直接影響します。これには厳しいペナルティから簡単なアドバイスまであります。地域の法令や規制を調べ、必要に応じて資格のあるプライバシーの専門家または弁護士に相談してください。

| # | 説明 | L1 | L2 | L3 | CWE |
| :---: | :--- | :---: | :---:| :---: | :---: |
| **8.3.1** | 機密データが HTTP メッセージボディまたはヘッダでサーバに送信され、HTTP verb のクエリ文字列パラメータには機密データが含まれていない。 | ✓ | ✓ | ✓ | 319 |
| **8.3.2** | [修正, 8.3.9 へ分割] ユーザが自分のデータをオンデマンドで削除する方法を持っている。 | ✓ | ✓ | ✓ | 212 |
| **8.3.3** | 提供された個人情報の収集および使用に関して明確な表現でユーザに提供されている、およびそれが何らかの方法で使用される前にユーザが提供されたオプトインでそのデータの使用に同意をしている。 | ✓ | ✓ | ✓ | 285 |
| **8.3.4** | アプリケーションにより作成および処理されるすべての機密データを特定している。機密データを処理する方法に関するポリシーが整っている。 ([C8](https://owasp.org/www-project-proactive-controls/#div-numbering)) | ✓ | ✓ | ✓ | 200 |
| **8.3.5** | データが関連するデータ保護規制の下で収集されている場合やアクセスのログ記録が必要な場合には、機密データへのアクセスが (機密データ自体がログ記録されることなく) 監査されている。 | | ✓ | ✓ | 532 |
| **8.3.6** | メモリダンプ攻撃を軽減するために、メモリに保持された機密情報は不要になればすぐにゼロまたはランダムデータを使用して上書きされる。 | | ✓ | ✓ | 226 |
| **8.3.7** | 暗号化を必要とする機密情報や個人情報は、機密性と完全性の両方を提供する承認済みアルゴリズムを使用して暗号化されている。 ([C8](https://owasp.org/www-project-proactive-controls/#div-numbering)) | | ✓ | ✓ | 327 |
| **8.3.8** | 機密性の高い個人情報は、古いデータや期限切れのデータが自動的、定期的、または状況に応じて削除される、データ保持分類の対象となっている。 | | ✓ | ✓ | 285 |
| **8.3.9** | [追加, 8.3.2 から分割] ユーザが自分のデータをオンデマンドでエクスポートする方法を持っている。 | | ✓ | ✓ | |
| **8.3.10** | [追加] ユーザーが保存に同意しない限り、ユーザーが送信したファイルのメタデータから機密情報が削除されている。 | ✓ | ✓ | ✓ | 212 |

データ保護を検討する際には、主に一括抽出、一括変更、過度の使用について考慮すべきです。例えば、多くのソーシャルメディアシステムではユーザは1日に新しい友人を100人しか追加できませんが、これらの要求がどのシステムから来たのかは重要ではありません。銀行のプラットフォームでは、1000ユーロを超える資金を外部の機関に転送することは、1時間当たり5つの取引まででブロックすることが期待されています。各システムの要件は大きく異なることがあるため、「異常」を判断するには脅威モデルとビジネスリスクを考慮する必要があります。重要な基準はそのような異常な大量アクションを検出、阻止、または可能であればブロックする能力です。

## 参考情報

詳しくは以下の情報を参照してください。

* [Consider using Security Headers website to check security and anti-caching headers](https://securityheaders.io)
* [OWASP Secure Headers project](https://owasp.org/www-project-secure-headers/)
* [OWASP Privacy Risks Project](https://owasp.org/www-project-top-10-privacy-risks/)
* [OWASP User Privacy Protection Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/User_Privacy_Protection_Cheat_Sheet.html)
* [European Union General Data Protection Regulation (GDPR) overview](https://edps.europa.eu/data-protection_en)
* [European Union Data Protection Supervisor - Internet Privacy Engineering Network](https://edps.europa.eu/data-protection/ipen-internet-privacy-engineering-network_en)