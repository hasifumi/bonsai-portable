# bonsai-portable

llama.cpp の実行ファイルと Bonsai LLM をポータブルに使うための構成です。

---

## モデルのダウンロード先

| モデル | サイズ | Hugging Face URL |
|---|---|---|
| Bonsai-1.7B | ~248MB | https://huggingface.co/prism-ml/Bonsai-1.7B-gguf |
| Bonsai-4B | ~570MB | https://huggingface.co/prism-ml/Bonsai-4B-gguf |
| Bonsai-8B | ~1.2GB | https://huggingface.co/prism-ml/Bonsai-8B-gguf |

> 開発元（prism-ml）のコレクション一覧: https://huggingface.co/collections/prism-ml/bonsai

### ダウンロード方法

各ページの「Files and versions」タブから `.gguf` ファイルを直接ダウンロードするか、
`huggingface-cli` を使う：

```bash
# huggingface_hub のインストール（初回のみ）
pip install huggingface_hub

# 例: Bonsai-1.7B をダウンロード
huggingface-cli download prism-ml/Bonsai-1.7B-gguf --include "*.gguf" --local-dir .

# 例: Bonsai-4B をダウンロード
huggingface-cli download prism-ml/Bonsai-4B-gguf --include "*.gguf" --local-dir .

# 例: Bonsai-8B をダウンロード
huggingface-cli download prism-ml/Bonsai-8B-gguf --include "*.gguf" --local-dir .
```

---

## フォルダ構成

```
bonsai-portable/
├── llama-cli.exe        # 対話・推論用 CLI
├── llama-server.exe     # HTTP API サーバー
├── Bonsai-1.7B.gguf     # モデル（.gitignore で除外、各自ダウンロード）
└── README.md
```

---

## セットアップ

```bash
# 1. このリポジトリをクローン
git clone https://github.com/hasifumi/bonsai-portable.git
cd bonsai-portable

# 2. モデルをダウンロード（上記参照）
huggingface-cli download prism-ml/Bonsai-1.7B-gguf --include "*.gguf" --local-dir .
```

---

## 使い方

### 対話モード（llama-cli）

```bash
# Bonsai-1.7B で対話
./llama-cli.exe -m Bonsai-1.7B.gguf -cnv --chat-template chatml -p "あなたは優秀なアシスタントです。"

# スレッド数を指定（CPUコア数に合わせて調整）
./llama-cli.exe -m Bonsai-1.7B.gguf -cnv --chat-template chatml -t 8
```

### HTTP API サーバー（llama-server）

```bash
# サーバー起動（デフォルト: http://localhost:8080）
./llama-server.exe -m Bonsai-1.7B.gguf

# ポートを指定して起動
./llama-server.exe -m Bonsai-1.7B.gguf --port 8080

# curlでテスト
curl http://localhost:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{
    "model": "bonsai",
    "messages": [{"role": "user", "content": "日本の首都は？"}]
  }'
```

### よく使うオプション

| オプション | 説明 | 例 |
|---|---|---|
| `-m` | モデルファイルのパス | `-m Bonsai-1.7B.gguf` |
| `-t` | スレッド数 | `-t 8` |
| `-c` | コンテキスト長 | `-c 4096` |
| `-ngl` | GPU オフロード層数（GPU使用時） | `-ngl 99` |
| `--port` | サーバーのポート番号 | `--port 8080` |

---

## 動作環境

- Windows 10/11（llama-cli.exe / llama-server.exe）
- GPU なしの CPU 推論でも動作可能
- CUDA GPU がある場合は `-ngl 99` で高速化
