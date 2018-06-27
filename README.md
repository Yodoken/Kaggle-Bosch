# Kaggle勉強会 - Bosch 顛末

## 最初に何をしたか
まずは能見さんから紹介された佐藤さんの動画を見た。
それに感化され、気付いたらGCPでCompute Engine立てていた(アカウントは持っていた)。
しかし料金の安いPreenptive Instanceの立てかた分からず(T T)。

## 環境
### 仮想マシンのスペック
- CPU : Intel Haswell
- RAM : 52MByte
- Zone : us-east-1b
- Storage : 64GB SSD永続化ディスク

### セットアップ
- Anacondaをinstall、Jupter Notebookをググりながら自宅から接続できるようにセットアップ。
- GCPには1年間、3万円超のクレジット付属している。
- 念のため、こまめに終了している。
- GCPは起動が早い。

## データ分析
### 導入編
まずは、submissionを目標に、Pandas.read_csv()でデータを一気読みするも、読み込みが終わらず。

### 項目絞り込み
train_date.csvおよびtrain_numeric.csvについて最初の20,000件を読み出し、Joinした上で
RandomForestで学習し、FeatureImportanceが0以外の項目を抽出。

### 初めてのSubmission
上記特定のFeatureのみ全行読み出し、Response=0の項目をResponse=1と同じ数までダウンサンプリング、
LightGBMにて学習。train_test_split()したtestのMCC=0.28と出たため、いざsubmision...
しかし送信後にエラーが出る。行数が1行だけ多いとの警告。
修正後、無事成功、最初のsubmissionではPublicが0.02757。周りの参加者は0.2とか0.3出ました、
と言っている中1桁少ない！
Tryを繰り返すが、ローカルでMCCは比較的高いのにsubmissionの結果は0.05未満の状態が続き、
その後何日か苦しむ。

### 突破
数日後、ふとSubmissionの結果が0.14825に！
心当たりはRes=0側のダウンサンプリング比率を変えたことだが、原因は不明。もはや再現もできず。

その後、ダウンサンプリングの数をResponse=1の件数の3倍〜20倍で変化させてsubmitしたところ、
倍数が大きくなるにつれて成績が向上。
最高値Public:0.15057が出たのは20倍。14倍程度のところでほぼ頭打ちになる曲線。

### 全列での学習
read_csv()でチャンクサイズを指定し、10,000行ずつ読みだしてはダウンサンプリングを繰り返し、
118万件弱のデータを読みきった。LightGBMにて学習、submit。ところがpublic=0.04356と、
性能はガタ落ち。次元の呪い？ <- 今ここ。

