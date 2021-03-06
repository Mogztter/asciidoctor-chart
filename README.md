# :bar_chart: Asciidoctor Chart Extension

![Travis build status](https://img.shields.io/travis/Mogztter/asciidoctor-chart/master.svg)

An extension for [Asciidoctor.js](https://github.com/asciidoctor/asciidoctor.js) to render charts.

## Install

### Node.js

Install the dependencies:

    $ npm install asciidoctor.js asciidoctor-chart

Create a file named `chart.js` with the following content and run it:

```js
const asciidoctor = require('asciidoctor.js')()
const chart = require('asciidoctor-chart')

const input = `
[chart,line]
....
January,February,March
28,48,40
65,59,80
....`

chart.register(asciidoctor.Extensions)
console.log(asciidoctor.convert(input)) // <1>

const registry = asciidoctor.Extensions.create()
chart.register(registry)
console.log(asciidoctor.convert(input, {'extension_registry': registry})) // <2>
```
<1> Register the extension in the global registry
<2> Register the extension in a dedicated registry

**IMPORTANT**

To effectively render the chart in your HTML page you will need to add the Chartist CSS and JavaScript:


```html
<!DOCTYPE html>
<html>
  <head>
    <link href="node_modules/chartist/dist/chartist.min.css" rel="stylesheet">
  </head>
  <body>
    <!-- Site content goes here !-->
    <script src="node_modules/chartist/dist/chartist.min.js"></script>
  </body>
</html>
```

You will also need to add this JavaScript (after `chartist.min.js`) to generate all the charts in your `<body>`:

```js
document.body.querySelectorAll('div.ct-chart').forEach((node) => {
  const options = {
    height: node.dataset['chartHeight'],
    width: node.dataset['chartWidth'],
    colors: node.dataset['chartColors'].split(',')
  }
  const dataset = Object.assign({}, node.dataset)
  const series = Object.values(Object.keys(dataset)
    .filter(key => key.startsWith('chartSeries-'))
    .reduce((obj, key) => {
      obj[key] = dataset[key]
      return obj
    }, {})).map(value => value.split(','))
  const data = {
    labels: node.dataset['chartLabels'].split(','),
    series: series
  }
  Chartist[node.dataset['chartType']](node, data, options)
})
```
You can also render a chart from a `csv` file.

Create a file named `sales.csv` with the following content:

```csv
January,February,March
28,48,40
65,59,80
```

And use the following syntax:

```adoc
chart::sales.csv[bar,800,500]
```

### Browser

Install the dependencies:

    $ npm install asciidoctor.js asciidoctor-chart

Create a file named `chart.html` with the following content and open it in your browser:

```html
<html>
  <head>
    <script src="node_modules/asciidoctor.js/dist/browser/asciidoctor.js"></script>
    <script src="node_modules/asciidoctor-chart/dist/browser/asciidoctor-chart.js"></script>
    <link href="node_modules/chartist/dist/chartist.min.css" rel="stylesheet">
  </head>
  <body>
    <div id="content"></div>
    <script>
      var input = '[chart,line]\n' +
        '....\n' +
        'January,February,March\n' +
        '28,48,40\n' +
        '65,59,80\n' +
        '....'

      var asciidoctor = Asciidoctor()
      var chart = AsciidoctorChart

      const registry = asciidoctor.Extensions.create()
      chart.register(registry)
      var result = asciidoctor.convert(input, {'extension_registry': registry})
      document.getElementById('content').innerHTML = result
    </script>
    <script src="node_modules/chartist/dist/chartist.min.js"></script>
    <script>
      document.body.querySelectorAll('div.ct-chart').forEach((node) => {
        const options = {
          height: node.dataset['chartHeight'],
          width: node.dataset['chartWidth'],
          colors: node.dataset['chartColors'].split(',')
        }
        const dataset = Object.assign({}, node.dataset)
        const series = Object.values(Object.keys(dataset)
          .filter(key => key.startsWith('chartSeries-'))
          .reduce((obj, key) => {
            obj[key] = dataset[key]
            return obj
          }, {})).map(value => value.split(','))
        const data = {
          labels: node.dataset['chartLabels'].split(','),
          series: series
        }
        Chartist[node.dataset['chartType']](node, data, options)
      })
    </script>
  </body>
</html>
```
<1> Register the extension in the global registry
<2> Register the extension in a dedicated registry

## Usage

You can configure the type (`line` or `bar`), the height and the width in pixel:

*Using positional attributes*

```
// in this order: type, width, height
chart::sales.csv[bar,800,500]
```

*Using named attributes*

```
chart::sales.csv[height=500,width=800,type=line]
```

By default the chart will be a  600px * 400px line chart.

## How ?

This extension is using [Chartist.js](https://gionkunz.github.io/chartist-js/) to render responsives charts.
