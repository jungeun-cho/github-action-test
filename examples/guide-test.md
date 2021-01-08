
#### Pie 차트 시리즈 옵션 변경
4.0 버전에서는 Pie 차트의 기능이 개선되고 옵션이 정리되었다. 3.x 버전에서는 `radiusRange` 옵션을 `%`를 포함한 문자열 타입으로만 설정할 수 있었다. 4.0 버전에서는 숫자 타입으로도 설정할 수 있으며 반지름이 절댓값으로 계산된다. 또한 새로운 `clockwise` 옵션이 추가되었으며 시리즈가 그려지는 애니메이션 방향을 정할 수 있다. 3.x 버전의 `showLegend`, `labelAlign` 옵션은 4.0 버전의 `dataLabels` 옵션으로 통합되었다.

**v3.x**

```js
const options = {
  series: {
    radiusRange: ['60%', '100%']
    startAngle: -90,
    endAngle: 90,
    showLabel: true,
    showLegend: true,
    labelAlign: 'outer'
  }
};
```
> [v3.x Pie 차트 생성 가이드](https://github.com/nhn/tui.chart/blob/v3.11.2/docs/wiki/chart-types-pie.md)

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
