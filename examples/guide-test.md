## 개요

해당 문서는 TOAST UI Chart 4.0 버전 업데이트에 대한 마이그레이션 가이드로, 3.x 버전을 사용하는 사용자가 4.0 버전으로 업데이트할 때 필요한 모든 변경 사항을 기술한다.

TOAST UI Chart(이하 '차트'로 표기)는 4.0 버전에서 `canvas` 기반으로 차트가 개발되었으며, 모든 의존성을 제거하고 트리 쉐이킹을 지원하여 사용자에게 가벼운 개발 환경을 제공한다. 업데이트 시 주의해야 할 사항들이 많으므로 마이그레이션 가이드의 전체 내용을 숙지하길 바란다. 목차는 다음과 같으며, 실제 적용 시에는 '변경 사항' 항목의 내용을 순서대로 진행하길 권장한다.


## 목차

- [변경 사항](#변경-사항)
  * [차트 사용 방식](#차트-사용-방식)
  * [차트 생성 방식](#차트-생성-방식)
  * [테마 적용 방식](#테마-적용-방식)
  * [데이터 라벨 적용 방식](#데이터-라벨-적용-방식)
  * [툴팁 적용 방식](#툴팁-적용-방식)
  * [축 관련 옵션](#축-관련-옵션)
  * [Pie 차트 시리즈 옵션](#Pie-차트-시리즈-옵션)
  * [이름 변경](#이름-변경)
  * [그 외](#그-외)
* [제거된 기능](#제거된-기능)
  1. [Bower 지원 중단](#1.-Bower-지원-중단)
  2. [제거된 API](#2.-제거된-API)
  3. [차트](#3.-맵-차트)

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

추가로, 차트 4.0 버전에서는 앱에서 사용하는 차트 모듈만 가져와 사용할 수 있다. 필요한 차트 모듈을 로드하여 좀 더 가벼운 앱을 만들 수 있다. 이와 관련하여 차트 생성 방식도 변경되었는데, [차트 생성 방식](#차트-생성-방식)에서 설명한다.

```js
import { BarChart } from '@toast-ui/chart'; /* ES6 모듈 방식 */
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
const chart = tui.Chart.barChart(/* */);
```

**v4.0**

```js
const chart = toastui.Chart.barChart(/* */);
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

차트 4.0에서는 의존성 모듈들을 모두 제거하였다. CDN을 사용하여 개발하는 경우에는 더이상 사용하지 않는 의존성을 제거해야 한다.

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

### 차트 생성 방식
3.x 버전에서 차트를 생성하기 위해서는 정적 함수에 차트가 그려질 요소(`el`), 차트 데이터(`data`), 옵션을 매개변수에 차례로 넘겨주어 작성하였다.
4.0 버전에서는 정적 함수를 사용하는 방법 외에도 생성자 함수를 통해 생성할 수 있으며, 매개변수는 요소, 데이터, 옵션을 **객체**로 넘겨주도록 변경되었다.

**v3.x**
```js
import Chart from 'tui/chart';

const chart = Chart.barChart(el, data, options);
```

**v4.0**
```js
// 1. 정적 함수를 통해 생성
import Chart from '@toast-ui/chart';

const chart = Chart.barChart({el, data, options});

// 2. 생성자 함수를 통해 생성
import { BarChart } from '@toast-ui/chart';

const chart = new BarChart({el, data, options});
```

### 테마 적용 방식
3.x 버전에서는 `registerPlugin`으로 테마를 등록하고 옵션에 테마명을 입력하여 사용하였다. 4.0 버전에서는 옵션에 바로 테마를 정의하는 방식으로 새롭게 변경되었다. 훨씬 더 직관적이고 다양한 테마 스타일을 적용할 수 있다.

**v3.x**

```js
const theme = {
  chart: {/* */},
  title: {/* */},
  xAxis: {/* */},
  yAxis: {/* */},
  plot: {/* */},
  series: {/* */},
  legend: {/* */}
};

Chart.registerTheme('newTheme', theme);

const options = {
  ...
  theme: 'newTheme'
};
```
> 3.x 버전에서 지원하는 테마 속성은 [v3.x 테마 적용 방법](https://github.com/nhn/tui.chart/blob/v3.11.2/docs/wiki/theme.md)에서 확인할 수 있다.

**v4.0**
```js
const options = {
  ...
  theme: {
    chart: {/* */},
    title: {/* */},
    xAxis: {/* */},
    yAxis: {/* */},
    plot: {/* */},
    series: {/* */},
    legend: {/* */},
    tooltip: {/* */},
    exportMenu: {/* */}
  }
};
```

전역 스타일, 차트 제목, 축, 플롯, 범례, 툴팁, 내보내기 메뉴에 대한 테마는 [공통 컴포넌트 테마](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-theme.md)에서 확인할 수 있으며, 시리즈에 대한 테마는 각 차트 별 가이드에서 확인할 수 있다.

* [Bar 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-bar.md#시리즈-theme)
* [Column 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-column.md#시리즈-theme)
* [Bullet 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-bullet.md#시리즈-theme)
* [BoxPlot 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-boxplot.md#시리즈-theme)
* [Line 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-line.md#시리즈-theme)
* [Area 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-area.md#시리즈-theme)
* [Heatmap 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-heatmap.md#시리즈-theme)
* [Treemap 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-treemap.md#시리즈-theme)
* [Bubble 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-bubble.md#시리즈-theme)
* [Scatter 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-scatter.md#시리즈-theme)
* [Radar 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-radar.md#시리즈-theme)
* [Pie 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-pie.md#시리즈-theme)
* [NestedPie 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-nestedPie.md#시리즈-theme)
* [ColumnLine 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-columnLine.md#시리즈-theme)
* [LineArea 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-lineArea.md#시리즈-theme)
* [LineScatter 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-lineScatter.md#시리즈-theme)

4.0 버전에서는 툴팁과 내보내기 메뉴에 테마를 적용할 수 있으며, 시리즈에서 데이터 라벨 테마도 적용할 수 있다.

3.x 버전에서 `series` 옵션으로 지원하던 스타일 관련 속성은 4.0 버전에서 `theme` 속성에 포함되었다.

| 차트 타입 | v3.x | v4.0 |
| --- | --- | --- |
| Area 차트 | `series.areaOpacity` | `theme.series.areaOpacity` |
| Area, Line 차트 | `series.pointWidth` | `theme.series.lineWidth` |
| Bar, Column 차트 | `series.barWidth` | `theme.series.barWidth` |

### 데이터 라벨 적용 방식
차트 3.x에서는 시리즈에 값을 표현할 때 옵션이 매우 제한적이며 스타일을 변경할 수 없었다. 차트 4.0 버전에서는 사용자가 데이터 라벨의 위치를 지정하고 출력 형식을 지정하며, 다양하게 스타일링 할 수 있도록 옵션을 구체화하였다.

**v3.x**
```js
const options = {
  series: {
    showLabel: true
  }
};
```

**v4.0**
```js
const options = {
  series: {
    dataLabels: {
      visible: true,
      offsetX: 0,
      offsetY: 0,
      formatter: (value) => `$${value}`
      ...
      /* 차트 시리즈 별 데이터 라벨 옵션 */
    }
  },
  theme: {
    series: {
      dataLabels: {/* */}
    }
  }
};
```

차트 시리즈 별 지원하는 `dataLabels` 옵션이 조금씩 다르다. 각 차트 별 **dataLabels 가이드**를 참고한다.

* [Bar 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-bar.md#datalabels)
* [Column 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-column.md#datalabels)
* [Bullet 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-bullet.md#datalabels)
* [BoxPlot 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-boxplot.md#datalabels)
* [Line 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-line.md#datalabels)
* [Area 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-area.md#datalabels)
* [Heatmap 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-heatmap.md#datalabels)
* [Treemap 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-treemap.md#datalabels)
* [Pie 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-pie.md#datalabels)
* [NestedPie 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-nestedPie.md#datalabels)
* [ColumnLine 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-columnLine.md#datalabels)
* [LineArea 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-lineArea.md#datalabels)
* [LineScatter 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-lineScatter.md#datalabels)


### 툴팁 적용 방식

#### `tooltip.suffix` -> `tooltip.formatter`
#### `tooltip.template` 옵션

#### `tooltip.grouped` -> 시리즈 별 `eventDetectType`

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
};
```

**v4.x**
```ts
chart 4
const options = {
  xAxis: {
    date: boolean | { format: string }
  }
};
```

#### chart.format, xAxis.prefix, xAxis.suffix, yAxis.prefix, yAxis.suffix -> formatter


#### labelMargin, maxWidth -> width, height

### 이름 변경
동작은 v3.x와 같지만 4.0 버전에서 이름이 변경된 옵션, 메소드, 커스텀 이벤트는 다음과 같다.

**옵션**

| v3.x | v4.0 | 설명 |
| --- | --- | --- |
| `series.allowSelect` | `series.selectable` | 시리즈 선택 기능 사용 여부 |
| `series.shifting` | `series.shift` | shift 사용 여부 |
| `chartExportMenu` | `exportMenu` | 내보내기 옵션 |

**메소드**

| v3.x | v4.0 | 설명 |
| --- | --- | --- |
| `showSeriesLabel` | `showSeriesDataLabel` | 데이터 라벨 표시 |
| `hideSeriesLabel` | `hideSeriesDataLabel` | 데이터 라벨 숨기기 |

**커스텀 이벤트**

| v3.x | v4.0 | 설명 |
| --- | --- | --- |
| `'changeCheckedLegends'` | `clickLegendCheckbox` | 범례에 체크박스 클릭 시 발생 |
| `'selectLegend'` | `clickLegendLabel` | 범례에 라벨 클릭 시 발생 |

### Pie 차트 시리즈 옵션
### 그 외

#### `chart.width`와 `chart.height` 옵션에 `'auto'` 타입 추가
`auto`로 설정해주면 `window.resize` 이벤트 리스너가 등록되며, 차트 컨테이너의 크기가 변경되면, 자동으로 컨테이너 크기에 맞춰 다시 렌더링한다.
#### 레이아웃 적용
플롯, 축, 범례

* plot.width, plot.height 설정
* Axis.width, plot.height 설정

#### `responsive` 옵션
#### 새로운 인스턴스 메서드
차트 4.0 버전에서는 사용자 편의를 위한 새로운 인스턴스 메소드를 제공한다. 추가된 메소드 목록은 다음과 같다.

```js
const chart = new LineChart({ el, data, options });

chart.addSeries(/* */);
chart.setOptions(/* */);
chart.updateOptions(/* */);
chart.getOptions();
chart.showTooltip(/* */);
chart.hideTooltip();
```

| 메소드명 | 설명 | 링크 |
| --- | --- | --- |
| `addSeries` | 새로운 시리즈를 추가 | [가이드](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-api.md#addseries) |
| `setOptions` | 차트 옵션 전체 변경 | [가이드](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-api.md#setoptions) |
| `updateOptions` | 기존 차트 옵션 변경 | [가이드](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-api.md#updateoptions) |
| `getOptions` | 차트에 적용된 옵션 반환 | [가이드](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-api.md#getoptions) |
| `showTooltip` | 시리즈 툴팁 표시 | [가이드](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-api.md#showtooltip) |
| `hideTooltip` | 툴팁 숨기기 | [가이드](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-api.md#hidetooltip) |

#### 새로운 커스텀 이벤트
차트 4.0 버전에서는 사용자 편의를 위한 새로운 커스텀 이벤트를 제공한다. 추가된 커스텀 이벤트 목록은 다음과 같다.

```js
const chart = Chart.lineChart({el, data, options});

chart.on('hoverSeries', (info) => {/* */});
chart.on('unhoverSeries', (info) => {/* */});
chart.on('zoom', (dataRange) => {/* */});
chart.on('resetZoom', () => {/* */});
```

| 커스텀 이벤트명 | 설명 | 차트 타입 |
| --- | --- | --- |
| `'hoverSeries'` | 시리즈 데이터에 마우스를 올릴 때 이벤트 발생 | 모든 차트 |
| `'unhoverSeries'` | `hoverSeries` 이벤트 발생 후 마우스가 떠날 때 이벤트 발생 | 모든 차트 |
| `'zoom'` | zoom 발생 시 이벤트 발생 | Line, Area, LineArea, Treemap 차트 |
| `'resetZoom'` | zoom 초기화 시 이벤트 발생 | Line, Area, LineArea, Treemap 차트 |


## 제거된 기능

### 1. Bower 지원 중단

차트 4.0 버전부터는 [Bower](https://bower.io/)를 지원하지 않는다. Bower 지원 중단과 함께 깃헙 리포지터리에서 `production` 브랜치도 제거되었다. 기존에 `production` 브랜치의 `dist` 폴더에서 번들 파일을 직접 가져와 사용했던 경우 주의해야 한다. 노드 환경에서 개발을 권장하나, 이전과 같이 Bower를 사용하던 방식으로 개발하고 싶은 경우 CDN을 사용하면 된다.

### 2. 제거된 API
#### 옵션 제거
| 차트 타입 | 옵션명 |
| --- | --- |
| 모든 차트 | `legend.maxWidth` |
| Bar, Column 차트 | `series.colorByPoint`, `series.stackType` |

#### 메소드 제거
| 타입 | 메소드명 |
| --- | --- |
| 정적 메소드 | `registerPlugin`, `registerTheme`, `registerMap` |
| 인스턴스 메소드 |  `setTooltipAlign`, `resetTooltipAlign`, `resetTooltipOffset`, `resetTooltipPosition` |

#### 커스텀 이벤트
* `afterShowTooltip`, `beforeHideTooltip`, `beforeShowTooltip`

### 3. 차트

| 차트 타입 | 설명 |
| --- | --- |
| Map 차트 | 새로운 **TOAST UI Map Chart**로 대체 예정 |
| PieDonut 차트 | 개선된 [NestedPie 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-nestedPie.md)로 대체 |



