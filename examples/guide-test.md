## 개요

TOAST UI Chart(이하 '차트'로 표기)는 4.0 버전에서 `canvas` 기반으로 차트가 개발되었으며, 모든 의존성을 제거하고 트리 쉐이킹을 지원하여 사용자에게 가벼운 개발 환경을 제공한다. 업데이트 시 주의해야 할 사항들이 많으므로 마이그레이션 가이드의 전체 내용을 숙지하길 바란다.

## 목차

- [변경 사항](#변경-사항)
  1. [설치](#1-설치)
  2. [생성](#2-생성)
  3. [테마](#3-테마)
  4. [툴팁 옵션](#4-툴팁-옵션)
  5. [축 옵션](#5-축-옵션)
  6. [데이터 라벨 옵션](#6-데이터-라벨-옵션)
  7. [NestedPie 차트(구 Pie&Donut 콤보 차트)](#7-nestedpie-차트구-piedonut-콤보-차트)
  8. [이름 변경](#8-이름-변경)
  9. [실시간 업데이트 개선](#9-실시간-업데이트-개선)
  10. [`chart.width`와 `chart.height` 옵션에 `'auto'` 타입 추가](#10-chartwidth와-chartheight-옵션에-auto-타입-추가)
  11. [반응형 옵션](#11-반응형-옵션)
  12. [레이아웃 설정](#12-레이아웃-설정)
  13. [Pie 차트 시리즈 옵션 변경](#13-Pie-차트-시리즈-옵션-변경)
  14. [새로운 인스턴스 메서드](#14-새로운-인스턴스-메서드)
  15. [새로운 커스텀 이벤트](#15-새로운-커스텀-이벤트)
  16. [Map 차트](#16-Map-차트)
* [제거된 기능](#제거된-기능)
  1. [Bower 지원 중단](#1-bower-지원-중단)
  2. [제거된 API](#2-제거된-api-목록)

## 변경 사항
### 1. 설치

이번 메이저 버전 업데이트에서 차트를 사용할 때 필요한 이름 및 의존성 정보가 변경되었다. 패키지 이름, 네임스페이스, 반들 파일명 등이 변경되었기 때문에 앱에서 사용하고 있는 차트의 버전만 업데이트해서는 안 되며, 아래 변경 사항들을 숙지하여 마이그레이션을 진행해야 한다.

#### 패키지 이름 변경

4.0 버전에서는 [스코프드 패키지(Scoped package)](https://docs.npmjs.com/using-npm/scope.html)가 적용되어 `tui-chart`에서 `@toast-ui/chart`로 패키지 이름이 변경되었다. 아래는 npm 명령어를 사용한 차트 설치 예제이다.

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

따라서, 모듈을 파일에 포함할 때 또한 변경된 이름으로 가져와야 한다.

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

또한, 차트 4.0은 필요한 차트 모듈만을 로드하여 앱을 좀 더 가볍게 만들 수 있다. 필요한 모듈만 가져오는 방법은 아래와 같다.

```js
import { BarChart } from '@toast-ui/chart'; /* ES6 모듈 방식 */
```

#### 네임스페이스 변경

TOAST UI 제품군의 네임스페이스 정책이 변경됨에 따라, 차트도 4.0 버전부터는 기존에 사용하던 `tui` 대신 `toastui`를 네임스페이스로 사용한다.

브라우저 환경에서 네임스페이스로 차트를 사용하는 경우, 다음과 같이 변경된 네임스페이스로 차트 생성자 함수(`toastui.Chart`)를 가져와야 한다.

**v3.x**

```js
const chart = tui.Chart.barChart(el, data, options);
```

**v4.0**

```js
const chart = toastui.Chart.barChart({el, data, options});
```

#### 번들 파일 변경

차트 4.0에서는 기능을 분리하고 의존성을 정리하면서 번들 파일 종류가 변경되었다. 의존성이 사라지면서 의존성이 포함된 번들이 제거되었으며, 번들 파일명의 프리픽스도 `tui-`에서 `toastui-`로 변경되었다.

**v3.x**

> 참고 : https://github.com/nhn/tui.chart/tree/v3.11.2/dist

| 번들 종류 | 파일명 | 압축 버전(`*.min`) 제공 여부 |
| :--- | :--- | :--- |
| 기본 | `tui-chart.js` | Y |
| 전체 의존성 모듈 포함 | `tui-chart-all.js` | Y |
| babel-polyfill 포함 | `tui-chart-polyfill.js` | Y |

**v4.0**

| 번들 종류 | 파일명 | 압축 버전(`*.min`) 제공 여부 |
| :--- | :--- | :--- |
| 기본 | `toastui-chart.js` | Y |
| babel-polyfill 포함 | `toastui-chart-polyfill.js` | Y |

<br>
차트 스타일을 적용할 때 필요한 CSS 파일 정보도 변경되었다.

**v3.x**

```js
import 'tui-chart/dist/tui-chart.css';
```

**v4.0**

```js
import '@toast-ui/chart/dist/toastui-chart.css';
```


제공하는 CSS 파일은 위와 같이 `toastui-chart.css`이며 압축 버전을 제공한다. npm과 CDN으로 제공되는 파일 종류는 동일하다.

#### 의존성 정보 변경

차트 4.0에서는 의존성 모듈들을 모두 제거하였다. CDN을 사용하여 개발하는 경우에는 더 이상 사용하지 않는 의존성을 제거해야 한다.

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

### 2. 생성
3.x 버전에서 차트를 생성하기 위해서는 정적 함수에 차트가 그려질 요소(`el`), 차트 데이터(`data`), 옵션을 매개변수에 차례로 넘겨주어 작성하였다.

**v3.x**
```js
import Chart from 'tui/chart';

const chart = Chart.barChart(el, data, options);
```

4.0 버전에서는 정적 함수를 사용하는 방법 외에도 생성자 함수를 통해 생성할 수 있으며, 매개변수는 요소, 데이터, 옵션을 **객체**로 넘겨주도록 변경되었다.

**v4.0**
```js
// 1. 정적 함수를 통해 생성
import Chart from '@toast-ui/chart';

const chart = Chart.barChart({el, data, options});

// 2. 생성자 함수를 통해 생성
import { BarChart } from '@toast-ui/chart';

const chart = new BarChart({el, data, options});
```

#### 차트 이름 변경
차트 4.0 버전에서는 정적 함수를 사용하여 차트를 생성할 때 콤보 차트를 포함하여 몇 개의 차트들이 이름이 변경되었다. 다음은 3.x 버전과 4.0 버전의 차트 생성 함수명을 정리한 표이다.

* 일반 차트

  | v3.x | v4.0 - 정적 함수 | v4.0 - 생성자 함수 | 이름 변경 여부 |
  | --- | --- | --- | --- |
  | barChart | barChart | BarChart |  |
  | columnChart | columnChart | ColumnChart |  |
  | bulletChart | bulletChart | BulletChart |  |
  | boxplotChart | boxPlotChart | BoxPlotChart | Y |
  | lineChart | lineChart | LineChart |  |
  | areaChart | areaChart | AreaChart |  |
  | heatmapChart | heatmapChart | HeatmapChart |  |
  | treemapChart | treemapChart | TreemapChart |  |
  | bubbleChart | bubbleChart | BubbleChart |  |
  | scatterChart | scatterChart | ScatterChart |  |
  | radialChart | radarChart | RadarChart | Y |
  | pieChart | pieChart | PieChart |  |

* 콤보 차트

  3.x 버전에서 콤보 차트를 만들어 주기 위해서는 정적 함수 `comboChart`를 사용했다. 4.0 버전에서는 지원하는 콤보 차트의 이름을 명확히 하였다.

  | v3.x | v4.0 - 정적 함수 | v4.0 - 생성자 함수 | 이름 변경 여부 |
  | --- | --- | --- | --- |
  | comboChart | nestedPieChart | NestedPieChart | Y |
  | comboChart | columnLineChart | ColumnLineChart | Y |
  | comboChart | lineAreaChart | LineAreaChart | Y |
  | comboChart | lineScatterChart | lineScatterChart | Y |

NestedPie 차트를 제외한 나머지 콤보 차트는 3.x 버전에서의 데이터와 사용 옵션이 같다. NestedPie 차트는 [NestedPie 차트(구 Pie-Donut 콤보 차트)](#7-nestedpie-차트구-piedonut-콤보-차트) 섹션에서 자세히 설명한다.

### 3. 테마
차트 3.x에서는 정적 메서드를 통해 테마를 등록하고 수정할 수 있었다. 차트 4.0에서는 옵션을 통해 테마를 정의하며 `setOptions`를 통해 테마를 변경할 수 있다. 툴팁과 내보내기 메뉴에 테마를 적용할 수 있으며, 시리즈에서 데이터 라벨 테마도 적용할 수 있다.

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

**v4.0**
```js
const options = {
  ...
  theme: {
    chart: {/* 차트 전역 스타일 */},
    title: {/* 차트 제목 스타일 */},
    xAxis: {/* X축 스타일 */},
    yAxis: {/* Y축 스타일 */},
    plot: {/* 플롯 스타일 */},
    series: {/* 시리즈 스타일 */},
    legend: {/* 범례 스타일 */},
    tooltip: {/* 툴팁 스타일 */},
    exportMenu: {/* 내보내기 메뉴 스타일 */}
  }
};

const chart = Chart.barChart({el, data, options});

chart.setOptions({
  ...
  theme: {
    series: {
      theme: {
        chart: {/* 차트 전역 스타일 */},
        title: {/* 차트 제목 스타일 */},
        xAxis: {/* X축 스타일 */},
        yAxis: {/* Y축 스타일 */},
        plot: {/* 플롯 스타일 */},
        series: {/* 시리즈 스타일 */},
        legend: {/* 범례 스타일 */},
        tooltip: {/* 툴팁 스타일 */},
        exportMenu: {/* 내보내기 메뉴 스타일 */}
      }
    }
  }
})
```

전역 스타일, 차트 제목, 축, 플롯, 범례, 툴팁, 내보내기 메뉴에 대한 테마는 [공통 컴포넌트 테마](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-theme.md)에서 확인할 수 있으며, 시리즈에 대한 테마는 각 차트별 가이드에서 확인할 수 있다.

* [Bar](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-bar.md#시리즈-theme)
, [Column](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-column.md#시리즈-theme)
, [Bullet](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-bullet.md#시리즈-theme)
, [BoxPlot](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-boxplot.md#시리즈-theme)
, [Line](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-line.md#시리즈-theme)
, [Area](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-area.md#시리즈-theme)
, [Heatmap](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-heatmap.md#시리즈-theme)
, [Treemap](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-treemap.md#시리즈-theme)
, [Bubble](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-bubble.md#시리즈-theme)
, [Scatter](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-scatter.md#시리즈-theme)
, [Radar](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-radar.md#시리즈-theme)
, [Pie](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-pie.md#시리즈-theme)
, [NestedPie](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-nestedPie.md#시리즈-theme)
, [ColumnLine](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-columnLine.md#시리즈-theme)
, [LineArea](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-lineArea.md#시리즈-theme)
, [LineScatter](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-lineScatter.md#시리즈-theme)

3.x 버전에서 `series` 옵션으로 지원하던 스타일 관련 속성은 4.0 버전에서 `theme` 속성에 포함되었다.

| 차트 타입 | v3.x | v4.0 |
| --- | --- | --- |
| Area 차트 | `series.areaOpacity` | `theme.series.areaOpacity` |
| Area, Line 차트 | `series.pointWidth` | `theme.series.lineWidth` |
| Bar, Column 차트 | `series.barWidth` | `theme.series.barWidth` |


### 4. 툴팁 옵션

#### 데이터값 출력 형식 변경

차트 3.x 버전에서는 `chart.format` 옵션과 `tooltip.suffix` 옵션을 통해 데이터 형식을 변경해 줄 수 있었다. 차트 4.0에서는 사용자가 유연하게 출력 형식을 다룰 수 있도록 함수 형태의 매개변수를 사용하도록 변경되었다.

**v3.x**

```js
const options = {
  chart: { format: '1,000' }
  tooltip: { suffix:  '℃' },
};
```


**v4.0**

```js
const options = {
  tooltip: {
    formatter: (value) => {
      const temp = Number(value.toFixed(2));
      let icon = '☀️';
      if (temp < 0) {
        icon = '❄️';
      } else if (temp > 25) {
        icon = '🔥';
      }

      return `${icon} ${value} ℃`;
    },
  },
};
```

#### 템플릿 적용 방식

3.x 버전에서는 툴팁에 템플릿을 설정할 때 넘겨받는 매개변수 데이터가 매우 제한적이었다. 4.0에서는 툴팁으로 보여주어야 할 데이터 모델과 차트 내부에서 정의된 기본 툴팁 템플릿(`header`, `body`) 그리고 테마 정보까지 함께 넘겨주어 사용자가 템플릿을 작성하는 데 편의를 제공한다.

**v3.x**
```js
const options = {
  tooltip: {
    template: (category, item, categoryTimestamp) => {
      const head = `<div>${category}</div>`;
      const body = `<div>${item.value}:${item.legend}</div>`;
      return `${head}${body}`;
    }
  }
};
```

**v4.0**
```js
const options = {
  tooltip: {
    template: (model, defaultTooltipTemplate, theme) => {
      const { body, header } = defaultTooltipTemplate;
      const { background } = theme;

      return `
        <div style="
          background: ${background};
          width: 140px;
          padding: 0 5px;
          text-align: center;
          color: white;
          ">
            <p>🎊 ${model.category} 🎊</p>
            ${body}
        </div>
      `;
    }
  }
};
```

#### 마우스 이벤트 탐지 방식

차트 3.x에서는 `tooltip.grouped` 옵션을 통해 데이터를 그룹화시켜 마우스를 통해 데이터를 탐지할 수 있었다. 차트 4.0에서는 `series.eventDetectType`을 제공해 좀 더 다양한 마우스 이벤트 탐지 방법을 제공한다. 차트별 사용 가능한 옵션은 아래 표를 참고하자.

| 차트 타입 | 탐지 타입 | 기본값 |
| --- | --- | --- |
| Line, Area 차트 | `'near'`, `'nearest'`, `'grouped'`, `'point'` | `'nearest'` |
| Bar, Column, Bullet, BoxPlot | `'grouped'`, `'point'` | `'point'` |
| ColumnLine 차트  | `'grouped'`, `'point'` | `'grouped'` |


### 5. 축 옵션
#### 축 눈금 및 라벨 간격 조절
3.x 버전에서는 `tickInterval`, `labelInterval` 옵션을 제공했다. `tickInterval`에는 `'auto'`만 입력할 수 있으며 `labelInterval` 옵션으로 라벨 간격만 조절할 수 있었다. 4.0 버전에서는 `tick.interval`, `label.interval`을 통해 라벨과 틱의 간격을 조절할 수 있다. 추가로, 새로운 `scale` 옵션을 지원하고, `scale.min`, `scale.max`, `scale.stepSize` 옵션을 사용해 좀 더 정교하게 축의 눈금과 라벨 간격을 제어할 수 있다.

**v3.x**
```js
const options = {
  xAxis: {
    min: 0,
    max: 9000,
    tickInterval: 'auto',
    labelInterval: 3
  }
}
```

**v4.0**
```js
const options = {
  xAxis: {
    tick: {
      interval: 3
    },
    label: {
      interval: 6
    },
    scale: {
      min: 0,
      max: 9000,
      stepSize: 1000 // or 'auto'
    }
  }
}
```

#### xAxis 라벨 날짜 형식 변경

3.x 버전에서는 X축에 날짜 형식의 라벨을 표시하려면 `type: 'datetime'`과 `dateFormat` 옵션을 지정해주어야 했다. 4.0 버전에서 날짜 형식을 위한 옵션은 `date`로 변경되었다.

**v3.x**
```js
const options = {
  xAxis: {
    type: 'datetime',
    dateFormat: 'YYYY-MM-DD'
  }
};
```

**v4.0**
```ts
const options = {
  xAxis: {
    date: { format: 'YYYY-MM-DD' } // or true
  }
};
```


### 6. 데이터 라벨 옵션
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
      /* 차트 시리즈별 데이터 라벨 옵션 */
    }
  },
  theme: {
    series: {
      dataLabels: {/* */}
    }
  }
};
```

차트 시리즈별 지원하는 `dataLabels` 옵션이 조금씩 다르다. 각 차트별 **dataLabels 가이드**를 참고한다.

다음은 데이터 라벨 기능을 지원하는 차트 목록이다.

* [Bar](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-bar.md#datalabels)
, [Column](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-column.md#datalabels)
, [Bullet](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-bullet.md#datalabels)
, [BoxPlot](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-boxplot.md#datalabels)
, [Line](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-line.md#datalabels)
, [Area](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-area.md#datalabels)
, [Heatmap](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-heatmap.md#datalabels)
, [Treemap](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-treemap.md#datalabels)
, [Pie](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-pie.md#datalabels)
, [NestedPie](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-nestedPie.md#datalabels)
, [ColumnLine](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-columnLine.md#datalabels)
, [LineArea](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-lineArea.md#datalabels)
, [LineScatter](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-lineScatter.md#datalabels)

### 7. NestedPie 차트(구 Pie&Donut 콤보 차트)

3.x 버전에서 Pie & Donut 콤보 차트는 4.0 버전에서 NestedPie 차트로 변경되었다. 3.x 버전에서는 최대 표현할 수 있는 중첩된 파이 시리즈가 2개로 제한되었으며, 차트 데이터에 사용되는 시리즈 별칭도 `'pie1'`, `'pie2'`로 고정되어 사용되었다. 4.0 버전에서는 이러한 불편함을 개선하고 여러 개의 중첩된 파이 차트를 생성할 수 있게 되었으며, 필요에 따라 데이터를 그룹화하여 보여줄 수 있다.

**v3.x**
```js
const data = {
  categories: ['Browser'],
  seriesAlias: {
    pie1: 'pie',
    pie2: 'pie'
  },
  series: {
    pie1: [/* */],
    pie2: [/* */]
  }
};

const options = {
  series: {
    pie1: {/* */},
    pie2: {/* */}
  }
};

Chart.combochart(el, data, options);
```
> [v3.x Pie&Donut 콤보 차트 생성 가이드](https://github.com/nhn/tui.chart/blob/v3.11.2/docs/wiki/chart-types-pie-donut-combo.md)


**v4.0**

```js
const data = {
  series: [
    {
      name: 'browsers',
      data: [/* */],
    },
    {
      name: 'versions',
      data: [/* */],
    },
    {
      name: 'details',
      data: [/* */],
    }
  ],
};

const options = {
  series: {
    browsers: {/* */},
    versions: {/* */},
    details: {/* */}
  }
};

Chart.nestedPieChart({el, data, options});
```

`parentName`을 지정하여 데이터의 부모 시리즈를 설정해주면 그룹 데이터로 표현할 수 있다. `parentName`은 series 배열에서 이전 시리즈의 `name`(`series.data.name`) 값을 사용한다.

```js
const data = {
  categories: ['A', 'B'],
  series: [
    {
      name: 'browsers',
      data: [
        {
          name: 'Chrome',
          data: 50,
        },
        ...
      ],
    },
    {
      name: 'versions',
      data: [
        {
          name: 'Chrome 64',
          parentName: 'Chrome',
          data: 40,
        },
        {
          name: 'Chrome 63',
          parentName: 'Chrome',
          data: 10,
        },
        ...
      ],
    },
    {
      name: 'details',
      data: [
        {
          name: 'Chrome 64 - 1',
          parentName: 'Chrome 64',
          data: 25,
        },
        {
          name: 'Chrome 64 - 2',
          parentName: 'Chrome 64',
          data: 15,
        },
        {
          name: 'Chrome 63 - 1',
          parentName: 'Chrome 63',
          data: 7,
        },
        {
          name: 'Chrome 63 - 2',
          parentName: 'Chrome 63',
          data: 3,
        },
        ...
      ],
    },
  ],
};
```

NestedPie 차트의 자세한 설명은 [NestedPie 차트](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-nestedPie.md) 가이드를 참고한다.

### 8. 이름 변경
동작은 v3.x와 같지만 4.0 버전에서 이름이 변경된 옵션, 메서드, 커스텀 이벤트는 다음과 같다.

* 옵션

  | v3.x | v4.0 | 설명 |
  | --- | --- | --- |
  | `xAxis.min` | `xAxis.scale.min` | X축 최솟값 설정 |
  | `xAxis.max` | `xAxis.scale.max` | X축 최댓값 설정 |
  | `yAxis.min` | `yAxis.scale.min` | Y축 최솟값 설정 |
  | `yAxis.max` | `yAxis.scale.max` | Y축 최댓값 설정 |
  | `series.allowSelect` | `series.selectable` | 시리즈 선택 기능 사용 여부 |
  | `series.shifting` | `series.shift` | shift 사용 여부 |
  | `chartExportMenu` | `exportMenu` | 내보내기 옵션 |

* 메서드

  | v3.x | v4.0 | 설명 |
  | --- | --- | --- |
  | `showSeriesLabel` | `showSeriesDataLabel` | 데이터 라벨 표시 |
  | `hideSeriesLabel` | `hideSeriesDataLabel` | 데이터 라벨 숨기기 |

* 커스텀 이벤트

  | v3.x | v4.0 | 설명 |
  | --- | --- | --- |
  | `'changeCheckedLegends'` | `clickLegendCheckbox` | 범례에 체크 박스 클릭 시 발생 |
  | `'selectLegend'` | `clickLegendLabel` | 범례에 라벨 클릭 시 발생 |

### 9. 실시간 업데이트 개선

3.x 버전에서 `series.shifting: true`와 `addData` 메서드를 사용해 차트 데이터를 업데이트할 수 있었다. 이 기능은 3.x 버전에서 Line과 Area 차트에서만 동작하였다. 4.0 버전에서는 Line과 Area 외에도 Column, Heatmap, LineArea, ColumnLine 차트에서도 사용할 수 있게 되었다. 사용법은 [Live Update](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-liveUpdate-options.md) 가이드를 참고한다.

### 10. `chart.width`와 `chart.height` 옵션에 `'auto'` 타입 추가
3.x 버전에서는 `chart.width`와 `chart.height` 옵션을 필수적으로 숫자 값을 입력해 주어야 한다. 4.0 버전에서는 이 옵션을 입력해주지 않으면 컨테이너 크기에 맞춰 차트가 렌더링 된다. 숫자 외 `auto`로 설정해주면 차트 내부적으로 `window.resize` 이벤트 리스너가 등록되며, 차트 컨테이너의 크기가 변경되면 자동으로 컨테이너 크기에 맞춰 다시 렌더링한다.

### 11. 반응형 옵션
3.x 버전에서는 차트의 크기가 변경되었을 때 옵션을 변경하는 API가 따로 존재하지 않아 차트를 반응형으로 렌더링하기 어려웠다. 4.0 버전에서는 이를 개선하기 위해 새로운 `responsive` 옵션을 제공한다. 사용법은 [responsive 옵션](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-responsive-options.md) 가이드를 참고한다.

### 12. 레이아웃 설정
3.x 버전에서는 `yAxis.maxWidth`, `legend.maxWidth` 옵션을 제공하여 Y축과 범례의 최대 너비만 지정할 수 있었으며 고정된 너비를 정해줄 수 없었다. 4.0 버전에서는 이를 개선하여 X축, Y축, 플롯 영역의 크기 및 범례의 너비를 지정할 수 있다. 여러 차트를 사용하는 경우 각 컴포넌트의 너비를 고정하고 싶을 때 유용하게 사용할 수 있다. 사용법은 [레이아웃 설정](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-layout-options.md) 가이드를 참고한다.


### 13. Pie 차트 시리즈 옵션 변경
4.0 버전에서는 Pie 차트의 기능이 개선되고 옵션이 정리되었다. 3.x 버전에서는 `radiusRange` 옵션을 `%`를 포함한 문자열 타입으로만 설정할 수 있었다. 4.0 버전에서는 숫자 타입으로도 설정할 수 있으며 반지름이 절댓값으로 계산된다. 또한 새로운 `clockwise` 옵션이 추가되었으며 시리즈가 그려지는 애니메이션 방향을 정할 수 있다. 3.x 버전의 `showLegend`, `labelAlign` 옵션은 4.0 버전의 `dataLabels` 옵션으로 통합되었다.

**v3.x**

```js
const options = {
  series: {
    radiusRange: ['60%', '100%'],
    startAngle: -90,
    endAngle: 90,
    showLabel: true,
    showLegend: true,
    labelAlign: 'outer'
  }
};
```

**v4.0**
```js
const options = {
  series: {
    radiusRange: {
      inner: 120, // or '60%',
      outer: 200  // or '100%'
    },
    angleRange: {
      start: -90,
      end: -90
    },
    clockwise: true,
    dataLabels: {
      visible: true,
      pieSeriesName: {
        visible: true,
        anchor: 'outer',
      }
    }
  }
};
```
> [v4.0 Pie 차트 생성 가이드](https://github.com/nhn/tui.chart/blob/next/docs/ko/chart-pie.md)


### 14. 새로운 인스턴스 메서드
차트 4.0 버전에서는 사용자 편의를 위한 새로운 인스턴스 메서드를 제공한다. 추가된 메서드 목록은 다음과 같다.

```js
const chart = new LineChart({ el, data, options });

chart.addSeries(/* */);
chart.setOptions(/* */);
chart.updateOptions(/* */);
chart.getOptions();
chart.showTooltip(/* */);
chart.hideTooltip();
```

| 메서드명 | 설명 | 링크 |
| --- | --- | --- |
| `addSeries` | 새로운 시리즈를 추가 | [가이드](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-api.md#addseries) |
| `setOptions` | 차트 옵션 전체 변경 | [가이드](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-api.md#setoptions) |
| `updateOptions` | 기존 차트 옵션 변경 | [가이드](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-api.md#updateoptions) |
| `getOptions` | 차트에 적용된 옵션 반환 | [가이드](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-api.md#getoptions) |
| `showTooltip` | 시리즈 툴팁 표시 | [가이드](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-api.md#showtooltip) |
| `hideTooltip` | 툴팁 숨기기 | [가이드](https://github.com/nhn/tui.chart/blob/next/docs/ko/common-api.md#hidetooltip) |

### 15. 새로운 커스텀 이벤트
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

### 16. Map 차트

3.x 버전에서는 Map 차트가 포함되었지만 4.0 버전에서는 별도의 SVG 기반 라이브러리로 제공된다.

| v3.x | v4.0 |
| --- | --- |
| TOAST UI Chart에 포함 | 별도의 SVG 기반 라이브러리로 제공(예정) |

## 제거된 기능

### 1. Bower 지원 중단

차트 4.0 버전부터는 [Bower](https://bower.io/)를 지원하지 않는다. Bower 지원 중단과 함께 깃헙 저장소에서 `production` 브랜치도 제거되었다. 기존에 `production` 브랜치의 `dist` 폴더에서 번들 파일을 직접 가져와 사용했던 경우 주의해야 한다. 노드 환경에서 개발을 권장하나, 이전과 같이 Bower를 사용하던 방식으로 개발하고 싶은 경우 CDN을 사용하면 된다.

### 2. 제거된 API 목록
마지막으로, 차트 4.0 버전에서 제거된 API를 정리한 목록이다.

#### 메서드
| 타입 | 메서드명 |
| --- | --- |
| 정적 메서드 | `registerPlugin`, `registerTheme`, `registerMap` |
| 인스턴스 메서드 |  `setTooltipAlign`, `resetTooltipAlign`, `resetTooltipOffset`, `resetTooltipPosition` |

#### 커스텀 이벤트
* `afterShowTooltip`, `beforeHideTooltip`, `beforeShowTooltip`
