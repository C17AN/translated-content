---
title: "Permissions-Policy: ambient-light-sensor"
slug: Web/HTTP/Reference/Headers/Permissions-Policy/ambient-light-sensor
original_slug: Web/HTTP/Headers/Permissions-Policy/ambient-light-sensor
---

{{HTTPSidebar}} {{SeeCompatTable}}

HTTP の {{HTTPHeader("Permissions-Policy")}} ヘッダーにおける `ambient-light-sensor` ディレクティブは、現在の文書が {{domxref('AmbientLightSensor')}} インターフェイスを通じて端末の周囲の環境における明るさの量に関する情報を収集することを許可するかどうかを制御します。

## 構文

```
Permissions-Policy: ambient-light-sensor <allowlist>;
```

- \<allowlist>
  - : この機能を許可するオリジンのリストです。 [`Permissions-Policy`](/ja/docs/Web/HTTP/Reference/Headers/Permissions-Policy#%E6%A7%8B%E6%96%87) を参照してください。

## 既定のポリシー

`ambient-light-sensor` の既定の許可リストは `'self'` です。

## 仕様書

{{Specifications}}

## ブラウザーの互換性

{{Compat}}

## 関連情報

- {{HTTPHeader("Permissions-Policy")}} ヘッダー
- [機能ポリシー](/ja/docs/Web/HTTP/Guides/Permissions_Policy)
- [機能ポリシーの使用](/ja/docs/Web/HTTP/Guides/Feature_Policy/Using_Feature_Policy)
