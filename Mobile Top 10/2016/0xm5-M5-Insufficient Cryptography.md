# M5：不十分な暗号化

<table>
 <tr>
  <th>脅威エージェント</th>
  <th>攻撃経路</th>
  <th colspan="2">セキュリティ上の弱点</th>
  <th>技術的な影響</th>
  <th>ビジネスへの影響</th>
 </tr>
 <tr>
  <td align="center" width="20%">アプリ依存 </td>
  <td align="center" width="15%">攻撃難易度<br>容易</td>
  <td align="center" width="15%">蔓延度<br>中</td>
  <td align="center" width="15%">検出難易度<br>普通</td>
  <td align="center" width="17.5%">影響度<br>重大</td>
  <td align="center" width="17.5%">アプリ / ビジネス依存</td>
 </tr>
 <tr>
  <td>以下のような脅威エージェントが存在します。 <br> 不適切に暗号化されたデータに物理的にアクセスできる攻撃者、もしくは、攻撃者のために動作するモバイルマルウェア</td>
  <td>以下のような攻撃経路が存在します。 <br> デバイスへの物理的なアクセスやネットワークトラフィックキャプチャを用いたデータの解読、もしくは暗号化されたデータにアクセスすることのできるデバイス上のマルウェア</td>
  <td colspan="2">攻撃者はこの脆弱性を悪用し、暗号化アルゴリズムの弱点や暗号化プロセスの欠陥をついて、暗号化されたコードや機密データをオリジナルの形に解読します。</td>
  <td>本脆弱性によって、モバイルデバイスから機密データを不正に取得できるようになります。</td>
  <td>本脆弱性は様々なビジネスへの影響をもたらす可能性があります。通常、脆弱な暗号は以下の結果につながります。
  <ul>
   <li> プライバシーの侵害</li>
   <li> 情報の窃取</li>
   <li> コードの盗用</li>
   <li> 知的財産の窃取</li>
   <li> 風評被害</li>
  </ul>
  </td>
 </tr>
</table>


## 脆弱性有無の確認
暗号を利用するほとんどのモバイルアプリケーションにおいて、暗号が安全でない方法で使用されています。主に二つの方法によって暗号化が損なわれており、モバイルアプリケーションにおいては顕著といえます。
1つ目は、モバイルアプリケーションの暗号化・復号に伴う処理について根本的な欠陥があり、それを攻撃者により悪用され、機密データを解読される場合です。2つ目は、モバイルアプリケーションが、脆弱性の存在する暗号化・復号アルゴリズムを実装または使用しており、それを直接攻撃者によって解読される場合です。以下のサブセクションでは、これらの二つシナリオをより詳しく解説します。
 
### 組み込みコードにおける暗号化プロセスへの依存
デフォルトでは、iOSアプリケーションはコードの暗号化によりリバースエンジニアリングから(理論上は)保護されています。iOSのセキュリティモデルは、非Jailbreak環境で実行するために、アプリケーションが暗号化され、また信頼できるソースによって署名されることを要求しています。起動時に、iOSによって署名が検証された後、iOSアプリローダーはメモリ上でアプリケーションを復号しコードを実行します。この機能により、理論上は攻撃者によるiOSモバイルアプリケーションへのバイナリ攻撃を防止することができます。

ClutchModやGBDのようなフリーツールを使用することで、攻撃者はJailbreakしたデバイスに暗号化されたアプリケーションをダウンロードし、iOSローダーがメモリに読み込み復号した時点で(ローダーが実行する直前)、復号されたアプリケーションのスナップショットを取得することができます。攻撃者はスナップショットを取得しディスクに保存すると、攻撃者はIDA ProやHopperのようなツールをして容易にアプリの静的/動的解析をし、さらに加えてバイナリ攻撃を行うことができます。

組み込みコードにおける暗号化アルゴリズムを回避することは、ささいなことです。ですから、モバイルOSによって提供されている組み込みコードの暗号化を攻撃者はいつでも回避することができると想定してください。リバースエンジニアリング防止のレイヤーを追加するために必要なステップについては、M9を参照してください。

### 不適切な鍵管理プロセス
鍵の取り扱いの誤用については、最良のアルゴリズムを使用したかどうかは問題ではありません。多くの人が適切な暗号化アルゴリズムを間違って使用しており、独自のプロトコルで実装してしまっています。以下に、問題となる例をいくつか挙げます。
 - 攻撃者が読み取り可能なディレクトリに暗号化されたコンテンツと鍵を保存している。
 - 攻撃者が取得可能な鍵を生成している。
 - バイナリ内でハードコードされた鍵の使用を避ける。
 - 鍵はバイナリ攻撃で傍受される可能性がある。バイナリ攻撃を防ぐための情報はM10を参照すること。
 
### カスタム暗号化プロトコルの作成及び使用
モバイルやその他において、独自の暗号化アルゴリズムやプロトコルを作成して使用することほど、暗号化の誤った使用方法はありません。

セキュリティコミュニティによって堅牢であると認められた最新のアルゴリズムを常に使用し、可能な限りモバイルプラットフォームが提供する最新の暗号化APIを使用してください。バイナリ攻撃の結果、バイナリ内でハードコードされた鍵の暗号化に使用している共通ライブラリを、攻撃者が特定する可能性があります。暗号化に関する非常に高いセキュリティ要件が要求される場合、ホワイトボックス暗号の使用を検討すべきです。共通ライブラリの悪用につながるバイナリ攻撃を防ぐためには、M10を参照してください。
 
### 安全でない又は非推奨のアルゴリズムの使用
暗号アルゴリズム及びプロトコルには、重大な脆弱性が存在するものや、現在のセキュリティ要件と比較して不十分なものもあります。これらは使用するべきではありません。これには以下のものが含まれます。
 - RC4
 - MD4
 - MD5
 - SHA1


## 防止方法
機密データを扱う場合は、以下を実施することが最重要です。
 - 可能な限り、モバイルデバイスに機密データを保存することを避けること
 - 少なくとも今後10年間は使われ続ける暗号標準を適用すること
 - 推奨アルゴリズムに関するNISTのガイドラインに従うこと(参考資料のその他を参照してください)


## 攻撃シナリオの例


## 参考資料
### OWASP
 - [OWASP Cryptographic Storage Cheat Sheet](https://www.owasp.org/index.php/Cryptographic_Storage_Cheat_Sheet)
 - [OWASP Key Management Cheat Sheet](https://www.owasp.org/index.php/Key_Management_Cheat_Sheet)
 
### 外部リンク
 - [NIST Encryption Guidelines](http://csrc.nist.gov/publications/drafts/800-175/sp800-175b_draft.pdf)
