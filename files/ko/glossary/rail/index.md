---
title: RAIL
slug: Glossary/RAIL
l10n:
  sourceCommit: ada5fa5ef15eadd44b549ecf906423b4a2092f34
---

{{GlossarySidebar}}

**RAIL** 은 **Response, Animation, Idle, and Load** 의 축약어로, 2015년 Google Chrome 팀이 창안한 브라우저 내 사용자 경험과 성능에 중점을 둔 성능 모델입니다. RAIL의 성능의 핵심은 '사용자에게 초점을 맞추세요. 최종 목표는 사이트가 특정 장치에서 빠르게 작동하도록 만드는 것이 아니라 사용자를 행복하게 만드는 것'입니다. 상호 작용에는 페이지 로드, 유휴, 입력에 대한 응답, 스크롤 및 애니메이션의 4단계가 있습니다. 약어순으로 주요 원칙은 아래와 같습니다.

- **응답(Response)**
  - : **100ms** 이내에 사용자 입력을 승인하여 즉시 사용자에게 응답합니다.
- **애니메이션(Animation)**
  - : 애니메이션을 적용할 때, 각 프레임을 **16ms** 미만으로 렌더링하여 일관성을 유지하고 버벅거림을 방지하세요.
- **유휴(Idle)**
  - : 기본 JavaScript 스레드를 사용하는 경우, **50ms** 미만의 시간 동안 청크로 작업하여 사용자 상호작용을 위한 스레드를 확보합니다.
- **로드(Load)**
  - : **5초** 이내에 상호작용 가능한 콘텐츠를 제공합니다.

## 같이 보기

- [권장 웹 성능 타이밍: 얼마나 길면 너무 길까요](/ko/docs/Web/Performance/Guides/How_long_is_too_long)
