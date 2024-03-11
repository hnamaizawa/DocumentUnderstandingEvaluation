# DocumentUnderstanding Evaluation サンプルの説明

## 前提
本サンプルは[こちら](https://github.com/masaki-kumamoto/DocumentUnderstandingEvaluationTemplate)の環境のテンプレートを用いて UiPath のプロジェクトを生成後に動作確認を実施したものです。

テンプレートから生成された Windows レガシープロジェクトに対して以下のような対応を実施し、Windows プロジェクトとして動作することを確認しました。

1. テンプレートが用意した ML 抽出器ではなく、改めて ML 抽出器を作成し、Document Manager 上で設定したフィールド情報を取得することで WF 上のタクソノミー定義とのマッピングを実施  
（補足）基本的に本サンプルを利用する場合、最初にこの作業が必要とお考えください。
2. Windows プロジェクトへ変換
3. UiPath Document OCR の部分を CJK OCR へ変更  
`assets\InvokeWorkflows\01_DocumentUnderstanding\DU01_GetDigitizeDocumentInfoByOCR.xaml` の一番下の OCR 定義を CJK OCR へ変更しました。
4. Windows プロジェクトへ変換後に Excel ファイルの列を絞る `EX02_HideColumns xaml` を動かすとエラーが発生したため、暫定処理として処理全体を無効化して対処

## 事前準備
本サンプルを Clone 後に UiPath Studio からプロジェクトを開き、以下の作業を実施します。
詳細はプロジェクト直下に含まれる `ReadMe.pdf` ファイルをご覧ください。

- `Config.xlsx` の以下の項目を設定
  - `DuSettings` セルの `DU_ApiKey` へ DU を動かすために必要な API Key を設定します。
  - `ActionSettings` セルの `AC_AssignUserEmail` へ Action Center でタスクをアサインしたいユーザーのメールアドレスを設定します。
  - 必要に応じて `OcrSettings` セルの Google Cloud OCR と Microsoft Azure Computer Vision OCR の環境変数へ Endpoint、API-Key、Language などを設定します。
    - （補足）本サンプルでは Google Cloud OCR と Microsoft Azure Computer Vision OCR に対応した定義が用意されています。（他の OCR 用環境変数に対応した実装は定義されておりません）

- `DU_GetExtractionResult.xaml` の以下の項目を設定
  - 一番下のマシンラーニング抽出器へ ML サーバーへの Endpoint、もしくは ML スキルを指定します。
  - `抽出器を設定` をクリックし、請求書(Invoices)定義の項目をマッピングします。  
    - マッピング画面のマシンラーニング抽出子は DM で定義された項目となりますが、DM で項目を増やしたりした場合はマシンラーニング抽出器を作成し直し、最新の DM 定義を反映する作業が必要です。

- 必要に応じて `DU01_GetDigitizeDocumentInfoByOCR.xaml` を変更
  - `assets\InvokeWorkflows\01_DocumentUnderstanding\DU01_GetDigitizeDocumentInfoByOCR.xaml` の各 OCR 用定義を開き、利用したい OCR エンジン向けのアクティビティへ変更し、`Endpoint`、`API-Key`、`Language` などの環境変数を指定します。
  - 対応する環境変数がないものは直接指定してください。

## 実行手順
本サンプでは WF を1回実行することで複数の OCR エンジンの読み取り結果が確認可能です。

1. `01_ExtractDocumentsData.xaml` を実行  
Output フォルダへ幾つか Excel ファイルが生成されます。

2. Action Center でアサインされたタスクを開き、CJK OCR 読み取り結果を修正  
ここで修正した内容をベースに各 OCR エンジンの読み取り結果が比較されるため、正しいデータへ修正することが重要です。

3. `02_CopyActualValuesToReport.xaml` を実行  
Output フォルダの Excel ファイルが更新され、2. で修正した想定する内容と各 OCR エンジンの読み取り結果が比較した結果が出力されます。


## 補足説明

- Input フォルダにテスト用請求書サンプル（`請求書_2-1.pdf`） が含まれています。

- `DU01_GetDigitizeDocumentInfoByOCR.xaml` の `ApplyOcrOnPdf` 定義に Yes を設定しています。必要に応じて変更ください。

- `Config.xlsx` の `OcrSettings` セルの環境変数名を変更すると WF 側も変更する必要があるため、本サンプルではテンプレートが生成した環境変数名のまま利用しております。

