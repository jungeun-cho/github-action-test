## 개요

해당 문서는 TOAST UI Chart 4.0 버전 업데이트에 대한 마이그레이션 가이드로, 3.x 버전을 사용하는 사용자가 4.0 버전으로 업데이트할 때 필요한 모든 변경 사항을 기술한다.

TOAST UI Chart(이하 '차트'로 표기)는 4.0 버전에서 `canvas` 기반으로 차트가 개발되었으며, 모든 의존성을 제거하고 트리 쉐이킹을 지원하여 사용자에게 가벼운 개발 환경을 제공한다. 업데이트 시 주의해야 할 사항들이 많으므로 마이그레이션 가이드의 전체 내용을 숙지하길 바란다. 목차는 다음과 같으며, 실제 적용 시에는 '변경 사항' 항목의 내용을 순서대로 진행하길 권장한다.


## 목차

- [변경 사항](#변경-사항)
  1. [차트 사용 방식](#차트-사용-방식)
  2. [테마 적용 방식](#테마-적용-방식)

* [제거된 기능](#제거된-기능)
  1. [Bower 지원 중단](#Bower-지원-중단)

## 변경 사항
### 1. 차트 사용 방식

이번 메이저 버전 업데이트에서 가장 큰 변화로, 차트를 사용할 때 필요한 이름 및 의존성 정보가 변경되었다. 앱에서 사용하고 있는 차트의 버전만 업데이트해서는 안되며, 아래 변경 사항들을 숙지하여 마이그레이션을 진행해야 한다.

#### 패키지 이름 변경

차트는 npm과 CDN 두 가지 방식으로 사용할 수 있다. 이때 사용하는 패키지 이름이 변경되었다. 차트 4.0 버전에서 [스코프드 패키지(Scoped package)](https://docs.npmjs.com/using-npm/scope.html)를 적용하여 다음과 같이 `tui-chart`에서 `@toast-ui/chart`로 패키지 이름이 변경되었다. 아래는 npm 커맨드를 사용한 차트 설치 예제이다.

**v3.x**

```sh
$ npm install tui-chart
$ npm install tui-chart@<version>
```

**v4.0**

```sh
$ npm install @toast-ui/chart
$ npm install @toast-ui/chart@<version>
```

차트 3.x 버전은 `tui-chart`, 4.0 이상 버전은 `@toast-ui/chart`로만 사용할 수 있으므로, 특정 버전을 사용해 설치할 때 주의해야 한다. 또한 차트 중복 사용을 방지하기 위해, 반드시 기존에 설치된 `tui-chart`는 제거한 다음 4.0 버전을 설치해야 한다. 설치가 정상적으로 완료되면 다음과 같이 `package.json` 파일의 의존 정보가 업데이트된다.

```diff
{
  "dependencies": {
-  "tui-chart": "^3.11.2",
+  "@toast-ui/chart": "^4.0.0"
  }
}
```

패키지 이름이 변경되면서 모듈을 가져오는 방식도 변경되었다. 변경된 패키지 이름으로 차트 모듈을 가져와야 한다.

**v3.x**

```js
const Chart = require('tui-chart'); /* CommonJS 방식 */
```

```js
import Chart from 'tui-chart'; /* ES6 모듈 방식 */
```

**v4.0**

```js
const Chart = require('@toast-ui/chart'); /* CommonJS 방식 */
```

```js
import Chart from '@toast-ui/chart'; /* ES6 모듈 방식 */
```

차트에서 제공하는 모든 번들 파일(자바스크립트, CSS)은 변경된 패키지 이름이 포함된 경로로 가져와야 한다. 예를 들어, 차트에서 사용하는 CSS 번들 파일을 가져올 경우에 `@toast-ui/chart` 이하 경로로 가져온다.

```js
// ...
import '@toast-ui/chart/dist/toastui-chart.css';
```

#### 네임스페이스 변경

TOAST UI 제품군의 네임스페이스 정책이 변경됨에 따라, 차트도 4.0 버전부터는 기존에 사용하던 `tui` 대신 `toastui`를 네임스페이스로 사용한다.

브라우저 환경에서 네임스페이스로 차트를 사용하는 경우, 다음과 같이 변경된 네임스페이스로 차트 생성자 함수(`toastui.Chart`)를 가져와야 한다.

**v3.x**

```js
const chart = new tui.Chart(/* */);
```

**v4.0**

```js
const chart = new toastui.Chart(/* */);
```

네임스페이스 변경과 관련해 차트의 번들 파일명 및 CDN 경로 정보도 변경되었는데, 자세한 설명은 아래 내용에서 설명한다.

#### 번들 파일 변경

차트는 여러 개발 상황을 고려하여 다양한 종류의 번들 파일을 제공하는데, 4.0 버전에서 기능을 분리하고 의존성을 정리하면서 번들 파일 종류가 변경되었다. 개발 환경에 따라서도 파일을 다르게 제공하므로, 아래 내용을 참조해 조건에 맞는 번들 파일을 선택하면 된다. 네임스페이스와 마찬가지로 번들 파일명의 프리픽스도 `tui-`에서 `toastui-`로 변경된 것에 유의한다.

아래는 자바스크립트 번들 파일에 대한 변경 정보다.

**v3.x**

> 참고 : https://github.com/nhn/tui.chart/tree/v3.11.2/dist

| 번들 종류 | 파일명 | 최소화 버전(`*.min`) 제공 여부 |
| :--- | :--- | :--- |
| 기본 | `tui-chart.js` | Y |
| 전체 의존성 모듈 포함 | `tui-chart-all.js` | Y |
| babel-polyfill 포함 | `tui-chart-polyfill.js` | Y |

**v4.0**
                  |
| 번들 종류 | 파일명 | 최소화 버전(`*.min`) 제공 여부 |
| :--- | :--- | :--- |
| 기본 | `toastui-chart.js` | Y |
| babel-polyfill 포함 | `toastui-chart-polyfill.js` | Y |

차트 스타일을 적용할 때 필요한 CSS 파일 정보도 변경되었다.

**v3.x**

- 차트 사용

```js
import 'tui-chart/dist/tui-chart.css';
```

**v4.0**

- 차트 사용

```js
import '@toast-ui/chart/dist/toastui-chart.css';
```


제공하는 CSS 파일은 위와 같이 `toastui-chart.css`이며, 최소화 버전을 제공한다. npm과 CDN으로 제공되는 파일 종류는 동일하다.

#### 의존성 정보 변경

차트 4.0에서는 의존성 모듈들을 제거하였다. CDN을 사용하여 개발하는 경우에는 더이상 사용하지 않는 의존성을 제거해야 한다.

**v3.x**

- [브라우저 개발 환경에서 추가해야 할 의존성 파일 목록](https://github.com/nhn/tui.chart/blob/v3.11.2/docs/wiki/getting-started.md)

**v4.0**

```html
<head>
  ...
  <link
    rel="stylesheet"
    href="https://uicdn.toast.com/chart/latest/toastui-chart.min.css"
  />
  ...
</head>
<body>
  ...
  <script src="https://uicdn.toast.com/chart/latest/toastui-chart.min.js"></script>
  ...
</body>
```




### 2. 레이아웃 적용
플롯, 축, 범례

### 3. 테마 적용 방식
* v3.x https://github.com/nhn/tui.chart/blob/v3.11.2/docs/wiki/theme

#### boxPlot, bullet, bar, column
`series.barWidth` -> `theme.series.barWidthRatio`

#### area
`areaOpacity` -> theme로 이동

### 4. 데이터 라벨 적용 방식

`series.showLabel` -> `series.dataLabels`

### 5. 툴팁 적용 방식

#### `tooltip.suffix` -> `tooltip.formatter`
#### `tooltip.template` 옵션

#### `tooltip.grouped` -> 시리즈 별 `eventDetectType`

### Pie 차트 series 옵션 변경
### PieDonut -> NestedPie

### `responsive` 옵션


### 변경된 옵션

#### `series.shifting` -> `series.shift`

#### chart.width, chart.height -> 'auto' 타입 추가

#### plot.width, plot.height 설정



### 축 관련 옵션
#### tickInterval, labelInterval -> tick.interval, label.interval

#### min, max -> scale.min, scale.max, scale.stepSize
#### xAxis 날짜 형식

**v3.x**
```js
const options = {
  xAxis: {
    type: 'datetime',
    dateFormat: string
  }
}
```

**v4.x**
```ts
chart 4
const options = {
  xAxis: {
    date: boolean | { format: string }
  }
}
```

#### chart.format, xAxis.prefix, xAxis.suffix, yAxis.prefix, yAxis.suffix -> formatter


#### labelMargin, maxWidth -> width, height

### 네이밍 변경
#### `series.pointWidth` -> `series.lineWidth` (area, line 차트만 가능)
#### `series.allowSelect` -> `series.selectable`
#### `legend.maxWidth` -> `legend.width`
#### `chartExportMenu` -> `exportMenu`
#### showSeriesLabel, hideSeriesLabel -> showSeriesDataLabel, hideSeriesDataLabel
#### on... changeCheckedLegends -> clickLegendCheckbox
#### selectLegend -> clickLegendLabel

### 새로운 인스턴스 메서드
* setOptions
* updateOptions
* getOptions
* zoom 가능한 차트
  * onZoom, resetZoom
### 새로운 커스텀 이벤트
hoverSeries
unhoverSeries
zoom
resetZoom

### Map Chart 지원
* 플러그인으로 제공
## 제거된 기능

### 1. Bower 지원 중단

차트 4.0 버전부터는 [Bower](https://bower.io/)를 지원하지 않는다. Bower 지원 중단과 함께 깃헙 리포지터리에서 `production` 브랜치도 제거되었다. 기존에 `production` 브랜치의 `dist` 폴더에서 번들 파일을 직접 가져와 사용했던 경우 주의해야 한다. 노드 환경에서 개발을 권장하나, 이전과 같이 Bower를 사용하던 방식으로 개발하고 싶은 경우 CDN을 사용하면 된다.

- all  번들 제거, polyfill 번들 지원 여부

### 제거된 API
#### 메소드 제거
* `registerPlugin`, `registerTheme`, `setTooltipAlign`, `resetTooltipAlign`

#### 커스텀 이벤트
* `afterShowTooltip`, `beforeHideTooltip`, `beforeShowTooltip`

#### 옵션 제거
* bar, column `colorByPoint`, `stackType`

### 맵 차트 제거
* 고도화 된 TOAST UI Map Chart로 새롭게 등장될 예정이다.



