# VOICEVOX CORE

[VOICEVOX](https://voicevox.hiroshiba.jp/) の音声合成コア。  
[Releases](https://github.com/Hiroshiba/voicevox_core/releases) にビルド済みのコアライブラリ（.so/.dll/.dylib）があります。

（エディターは [VOICEVOX](https://github.com/Hiroshiba/voicevox/) 、
エンジンは [VOICEVOX ENGINE](https://github.com/Hiroshiba/voicevox_engine/) 、
全体構成は [こちら](https://github.com/Hiroshiba/voicevox/blob/main/docs/%E5%85%A8%E4%BD%93%E6%A7%8B%E6%88%90.md) に詳細があります。）

## 依存関係

### Windows と Linux の場合

[CUDA 11.1](https://developer.nvidia.com/cuda-11.1.0-download-archive) と [CUDNN](https://developer.nvidia.com/cudnn) のインストールと [LibTorch](https://pytorch.org/) のダウンロードが必要です。

### macOS の場合

パッケージマネージャーの [Homebrew](https://brew.sh/index_ja) を用いて `brew install libtorch` コマンドで LibTorch を導入する方法が便利です（CUDA の macOS サポートは現在終了しているため、VOICEVOX CORE の macOS 向けコアライブラリも CUDA, CUDNN を利用しない CPU 版のみの提供となります）。

## API

[core.h](./core.h) をご参照ください。

## サンプルの実行

まず [Releases](https://github.com/Hiroshiba/voicevox_core/releases) からコアライブラリが入った zip をダウンロードしておきます。

### Python 3

#### ソースコードから実行

```bash
cd example/python

# example/python のディレクトリにコアライブラリが入った zip ファイルを展開

# Windowsの場合、DLLからLIBファイルの作成
./makelib.bat core

# macOSの場合、libcore_cpu.dylib へのシンボリックリンク libcore.dylib を作成
ln -s libcore_cpu.dylib libcore.dylib

# 環境構築
pip install -r requirements.txt
python setup.py install  # Linux と macOS の場合は先頭に `LIBRARY_PATH="$LIBRARY_PATH:."` が必要

# # うまく行かないときは毎回以下を実行すると良いかも
# python setup.py clean
# rm -r build *.cpp

# 実行（Windowsの場合）
PATH="$PATH:$HOME/libtorch/lib/" python run.py \
    --text "これは本当に実行できているんですか" \
    --speaker_id 1

# 実行（Linuxの場合）
LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/libtorch/lib/" python run.py \
    --text "これは本当に実行できているんですか" \
    --speaker_id 1

# 実行（macOSの場合）
LD_LIBRARY_PATH="$LD_LIBRARY_PATH:." python run.py \
    --text "これは本当に実行できているんですか" \
    --speaker_id 1

# 引数の紹介
# --text 読み上げるテキスト
# --speaker_id 話者ID
# --use_gpu GPUを使う
# --f0_speaker_id 音高の話者ID（デフォルト値はspeaker_id）
# --f0_correct 音高の補正値（デフォルト値は0。+-0.3くらいで結果が大きく変わります）
```

「ImportError: DLL load failed: 指定されたモジュールが見つかりません。」というエラーが出た場合は libtorch のパスが間違っているかもしれません。

#### Docker から

<details>

```bash
# イメージのビルド
docker build -t voicevox_core example/python

# コンテナの起動(音声を保存しておくボリュームを作成)
docker run -it -v ~/voicevox:/root/voice voicevox_core bash

# テスト音声 `おはようございます-1.wav` を生成
python run.py --text おはようございます --speaker_id 1
mv *.wav ~/voice
exit

# 音声の再生
aplay ~/voice/おはようございます-1.wav
```

</details>

### その他の言語

サンプルコードを実装された際はぜひお知らせください。こちらに追記させて頂きます。

## 事例紹介

**[VOICEVOX ENGINE SHARP](https://github.com/yamachu/VoicevoxEngineSharp) [@yamachu](https://github.com/yamachu)** ･･･ VOICEVOX ENGINE の C# 実装  
**[Node VOICEVOX Engine](https://github.com/y-chan/node-voicevox-engine) [@y-chan](https://github.com/y-chan)** ･･･ VOICEVOX ENGINE の Node.js/C++ 実装  

## ライセンス

サンプルコードおよび [core.h](./core.h) は [MIT LICENSE](./LICENSE) です。

[Releases](https://github.com/Hiroshiba/voicevox_core/releases) にあるビルド済みのコアライブラリは別ライセンスなのでご注意ください。