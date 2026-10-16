# Seedance 1.5 Pro API (`seedance-1.5-pro`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-ad-ceramic-mug-thumb.jpg" width="820" alt="Sample video frame generated through APIMart">
</p>

> **from $0.0104 per second** on the APIMart route (billed by resolution tier) — `https://api.apimart.ai/v1` の OpenAI 互換エンドポイント1つだけ、月額プランは不要です。*(2026-10-12 時点)*

**[APIキー](https://go.apimart.ai/k-fd7c31)** · **[料金](https://go.apimart.ai/k-cbf0c6)** · **[モデルページ](https://go.apimart.ai/k-67c959)** · [⚡ 60秒クイックスタート](#quickstart)

**チームが APIMart 経由で Seedance 1.5 Pro（`seedance-1.5-pro`）を使う理由**

- **キー1つでカタログ全体。**同じ `https://api.apimart.ai/v1` の base URL と `Authorization` ヘッダーで Seedance 1.5 Pro（`seedance-1.5-pro`）と画像・動画・言語モデルなど 300+ のモデルにアクセスできます。切り替えるのは `model` フィールドだけで、クライアント側の変更は不要です。
- **最低 $1 から、従量課金。**サブスクリプションも、事前に規模を見積もるプリペイドプランもありません。$1 からチャージして、呼び出した分だけ使えます。
- **課金内容はレスポンスで返ります。**完了したタスクはすべて請求額（`cost` / `credits_cost`）を報告し、`failed` になったタスクは返金されます。
- **非同期前提の設計。**送信して `task_id` を受け取り、`GET /v1/tasks/{id}` をポーリングするだけ。バッチ処理もリトライも特別な統合ではなく、普通のキュー処理です。

<!-- /conv-kit:v1 -->

**別名:** シードダンス · シーダンス

Seedance 1.5 Pro is ByteDance's AI video model with smoother motion, 4K quality, stability and precise control.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Text-to-video / image-to-video | `seedance-1.5-pro` | per generated second, by resolution | see the [model page](https://go.apimart.ai/k-67c959) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/videos/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| per generated second | — | see the live pricing page |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-cbf0c6) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | the shot description; camera and subject motion belong here |
| `duration` | integer | route default | clip length in seconds — billing is per generated second |
| `resolution` | string | route default | 480P / 720P / 1080P or the route’s own tiers |
| `size` / `aspect_ratio` | string | `16:9` | output framing |
| `image_urls` | string[] | — | first frame, first + last frame, or style references, where supported |
| `seed` | integer | — | fix the seed to reproduce a take |

The table lists the fields accepted by the APIMart `videos/generations` endpoint. Check the [model page](https://go.apimart.ai/k-67c959) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/videos/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"seedance-1.5-pro","prompt":"A bamboo forest at sunrise, slow push-in","duration":5,"resolution":"720P","size":"16:9"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/videos/generations", headers=HEADERS, timeout=60, json={
    "model": "seedance-1.5-pro", "prompt": "A bamboo forest at sunrise, slow push-in", "duration": 5,
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["videos"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**Seedance 1.5 Pro のモデル id は何ですか？**

`seedance-1.5-pro` です。上のルート表を参照してください。

**課金方法は？**

**from $0.0104 per second** on the APIMart route (billed by resolution tier)。完了したタスクは `cost` フィールドに正確な金額を報告し、失敗したタスクは返金されます。

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-fd7c31) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [seedance-1-5-pro-prompts-ja](https://github.com/gb34rq50qe4yd8/seedance-1-5-pro-prompts-ja)
- **Same model, other languages** — 9 more language builds: 中文 [seedance-1-5-pro-api-cn](https://github.com/ukvv6hjermhm/seedance-1-5-pro-api-cn) · Deutsch [seedance-1-5-pro-api-de](https://github.com/dezrqh1/seedance-1-5-pro-api-de) · Español [seedance-1-5-pro-api-es](https://github.com/b92zsn5u/seedance-1-5-pro-api-es) · Français [seedance-1-5-pro-api-fr](https://github.com/vo0dayvjw/seedance-1-5-pro-api-fr) · Relay/gateway [seedance-1-5-pro-api-gateway](https://github.com/y1i87cdabvzwil/seedance-1-5-pro-api-gateway) · Bahasa `seedance-1-5-pro-api-id` · 한국어 [seedance-1-5-pro-api-ko](https://github.com/degdwrds5w97ji/seedance-1-5-pro-api-ko) · Português [seedance-1-5-pro-api-pt](https://github.com/jif7hyqckmlct/seedance-1-5-pro-api-pt) · Русский [seedance-1-5-pro-api-ru](https://github.com/a1dbdgioyt/seedance-1-5-pro-api-ru)
- **More models in this language** — [midjourney-api-ja](https://github.com/apimartt1q/midjourney-api-ja) · [gemini-2-5-flash-image-preview-api-ja](https://github.com/ic7skkh/gemini-2-5-flash-image-preview-api-ja) · [nano-banana-api-ja](https://github.com/e4ylndl3g-v/nano-banana-api-ja) · [gemini-3-1-flash-image-preview-api-ja](https://github.com/gv4ju/gemini-3-1-flash-image-preview-api-ja) · [nano-banana-2-api-ja](https://github.com/lpbxkrzbfel/nano-banana-2-api-ja) · [gemini-3-pro-image-preview-api-ja](https://github.com/zzsxs8r/gemini-3-pro-image-preview-api-ja)
- **Topic hubs** — every build of this model, all languages: [seedance-1-5-pro](https://github.com/topics/seedance-1-5-pro) · [seedance-1-5-pro-api](https://github.com/topics/seedance-1-5-pro-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-67c959> | `apimart.ai/ja/model/doubao-seedance-1-5-pro` |
| Live pricing | <https://go.apimart.ai/k-cbf0c6> | `apimart.ai/ja/pricing` |
| Get an API key | <https://go.apimart.ai/k-fd7c31> | `apimart.ai/ja/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Seedance 1.5 Pro is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
