# Document Understanding Evaluation サンプルの説明

## 謝辞

このプロジェクトは、多くの人々の支援と励ましによって実現しました。特に、以下の方々に深い感謝を表します。

- **隈元**さんには、参考にさせていただいた[プロジェクト](https://github.com/masaki-kumamoto/DocumentUnderstandingEvaluationTemplate)に関する様々な情報をご提供いただきました。ありがとうございました。

また、このプロジェクトに貢献してくださったすべての協力者に心から感謝いたします。

---

## 最新の更新情報

- **AI inside DX Suite OCR への対応 (2024-07-26)**: AI inside DX Suite OCR へ対応しました。詳細は[変更ログ](https://github.com/hnamaizawa/DocumentUnderstandingEvaluation/commit/d4b85279e3ccd3c577d92e2b0ab6c5da0eebe416)をご覧ください。

- **日付型や数値型の項目に日本語などのテキストが設定されていた場合の対処を追加 (2024-07-11)**: 日付型や数値型の項目に日本語などのテキストが設定されていた場合にエラーが発生しワークフロー処理が終了してしまう問題へ対応しました。詳細は[変更ログ](https://github.com/hnamaizawa/DocumentUnderstandingEvaluation/commit/ebb89e9009669a4e68e8b920f1f776bc23c8767b)をご覧ください。

- **Cogent Labs SmartReadへの対応、インテリジェントキーワード分類器の改善 (2024-06-14)**: Cogent Labs SmartReadへ対応し、インテリジェントキーワード分類器の学習データ不足の問題を改善しました。詳細は[変更ログ](https://github.com/hnamaizawa/DocumentUnderstandingEvaluation/commit/7e2836085f22df7f74428b4c2cff1e66a44e27a8)をご覧ください。

- **インテリジェントキーワード分類器、フォーム抽出器を利用する機能を追加 (2024-05-13)**: インテリジェントキーワード分類器、フォーム抽出器に対応しました。詳細は[変更ログ](https://github.com/hnamaizawa/DocumentUnderstandingEvaluation/commit/b3dc9b5ab608d6d327ba9448a8da0afa9c9c9bff)をご覧ください。

- **FAQ の追加 (2024-03-19)**: 本 README の最後へ[よくある質問](https://github.com/hnamaizawa/DocumentUnderstandingEvaluation?tab=readme-ov-file#faq---%E3%82%88%E3%81%8F%E3%81%82%E3%82%8B%E8%B3%AA%E5%95%8F)を追記しました。

---

## 前提
本テンプレートは[こちらのプロジェクト](https://github.com/masaki-kumamoto/DocumentUnderstandingEvaluationTemplate)のテンプレートを用いて UiPath のプロジェクトを生成後に動作確認を実施したものです。

テンプレートから生成された Windows レガシープロジェクトを Windows プロジェクトへ変換後に以下の修正を行いました。

- UiPath Document OCR の部分を UiPath Extended Languages OCR へ変更  
`assets\InvokeWorkflows\01_DocumentUnderstanding\DU01_GetDigitizeDocumentInfoByOCR.xaml` の一番下の OCR 定義を Extended Languages OCR へ変更しました。

- 利用可能な 3rd Party OCRエンジンを Cogent Labs SmartRead OCR、Clova OCR、および MS Read OCR のみに変更し、他の OCR エンジンは削除しました。

- Input フォルダへサンプルデータを含め、`DU_GetExtractionResult.xaml` 内のアクティビティを有効にするだけで簡単な動作確認が行えるようにしました。


## 事前準備
本テンプレートを Clone 後に UiPath Studio からプロジェクトを開き、以下の作業を実施します。
詳細はプロジェクト直下に含まれる [ReadMe.pdf](https://github.com/hnamaizawa/DocumentUnderstandingEvaluation/blob/main/ReadMe.pdf) ファイルを参照してください。  
また、本テンプレートの基本的な考え方を理解する際はこちらの[説明動画](https://www.youtube.com/watch?v=snEatRjWHjg)をご覧ください。

- `CustomDUActivities.1.0.x.nupkg` を導入
  - DU 評価テンプレートは Cogent Labs SmartRead OCR と Clova OCR に対応したカスタムアクティビティを利用しているため、最初に `CustomDUActivities.1.0.x.nupkg` を導入します。
  - カスタムアクティビティのソースコードが含まれるリポジトリは[こちら](https://github.com/hnamaizawa/Document-Processing-Code-Samples.git)ですが、本サンプルには事前にビルドされた nupkg ファイルを含めているため、そのファイルをご自分の環境へ導入します。

- `Config.xlsx` の以下の項目を設定
  - `DuSettings` シートの `DU_ApiKey` へ DU を動かすために必要な API Key を設定します。
  - `ActionSettings` シートの `AC_AssignUserEmail` へ Action Center でタスクをアサインしたいユーザーのメールアドレスを設定します。
  - 必要に応じて `OcrSettings` シートの Cogent Labs SmartRead OCR、Clova OCR、および MS Read OCR の環境変数へ Endpoint、API-Key などを設定します。

- `DU_GetExtractionResult.xaml` の以下の項目を設定
  - 分類器を使用する場合は `Classification + Extraction` を有効にし、分類器を利用しない場合は `Extraction Only` を有効にします。
  - 使用しない分類器、抽出器のアクティビティを削除します。

## 実行手順
本サンプルでは WF を1回実行することで複数の OCR エンジンの読み取り結果が確認可能です。

1. `01_ExtractDocumentsData.xaml` を実行  
Output フォルダへ幾つか Excel ファイルが生成されます。

2. Action Center でアサインされたタスクを開き、Extended Languages OCR 読み取り結果を修正  
ここで修正した内容をベースに各 OCR エンジンの読み取り結果が比較されるため、**正しいデータへ修正する**ことが重要です。

4. `02_CopyActualValuesToReport.xaml` を実行  
Output フォルダの Excel ファイルが更新され、2. で修正した内容と各 OCR エンジンの読み取り結果を比較した結果が出力されます。  
（補足1）実行の最初にフォルダ選択ダイアログが表示されるため、処理が開始できない場合はダイアログが裏に表示されていないかご確認ください。  
（補足2）DU_Evaluation ファイルの `Summary` シートの右側に精度（Accuracy）が出力されています。  
（補足3）DU_Evaluation ファイルの帳票ファイル名のシートに抽出データ（ExtractedValue）と想定するデータ（ActualValue）が対となって整理されています。

## 補足説明

- `DU01_GetDigitizeDocumentInfoByOCR.xaml` の `ApplyOcrOnPdf` 定義に Yes を設定しています。必要に応じて変更ください。

---

## FAQ - よくある質問

#### Q: タクソノミーマネージャーを使おうとした際に `To run this application, you must install missing frameworks for .NET.` のメッセージが出力されました。どのように対処すれば良いでしょうか？
**A:** 以下のようにパッケージ＆依存関係パッケージをバージョンアップすることで解決できたと報告がありました。参考にしてみてください。
~~~
IntelligentOCR.Activities: 6.5.3 -> 6.14.1
UiPath.OCR.Activities: 3.11.0 -> 3.17.1
PDF.Activities: 3.10.1 -> 3.16.0
ML.Activities: 1.17.1 -> 1.24.0
~~~

#### Q: 長いファイル名の帳票を読み取ろうとした際にエラーが発生します。どうすれば良いでしょうか？
**A:** 出力結果 Excel のシート名に帳票のファイル名を利用しているため、30文字以上とならないようにファイル名を調整してください。  


