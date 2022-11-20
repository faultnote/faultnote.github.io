+++
title = "생산성을 높이는 코딩 테크닉"
slug = "use-ide-cleverly-1"
cover = "https://www.jetbrains.com/webstorm/img/overview/webstorm-overview.png"
date = "2020-11-14"
author = "soomtong"
tags = ["editor", "webstorm"]
keywords = ["ide", "webstorm"]
description = "IDE 를 통해 생산성을 높이는 방법, 웹스톰 기준"
showFullContent = false
readingTime = true
+++

> IDE 를 통해 생산성을 높이는 방법, 웹스톰 기준

생각대로 코드가 술술 나오는 경지에 이르려면 연습도 필요하지만 도구의 도움을 받으면 좀 더 생산적인 개발을 할 수 있다. 좋은 에디터를 잘 사용하는 것이 무척 중요하다.

NodeJS 코드를 기준으로 하지만 대부분 비슷하다.

코드 샘플은 아래 같은 것으로 가정한다.

```js
asyncRouteFn(async (req, res) => {
    const { type } = paramObject(req, OperationConfigFilter);
    const categoryInfo = await findOperationConfigByTypes({ site: [Site.XXX], type });
    const categoryInfoAssetIds = extractCategoryInfoAssetIds(categoryInfo);
    const categoryImageAssets = await getAssetsByIds(categoryInfoAssetIds);
    return ok(res, { data: composeCategoryInfoWithAssets(categoryInfo, categoryImageAssets) });
})
```

### 함수 생성

1. 함수 이름을 작성하고 전달할 파라미터까지 작성한 후 
2. `opt+return` 또는 `alt+enter` 를 통해 함수 본문 생성
![](/posts/images/use-cleverly/ide-cleverly-1.png)
3. 필요에 따라 const 바인드로 변경 또는 다른 곳으로 이동 
![](/posts/images/use-cleverly/ide-cleverly-2.png)

### 함수 참조

1. 함수 생성의 1 번과 마찬가지로 타이핑을 진행
2. `opt+return` 또는 `alt+enter` 를 통해 함수 참조 생성
![](/posts/images/use-cleverly/ide-cleverly-3.png)
3. 타입에 맞는 참조문 자동 생성
