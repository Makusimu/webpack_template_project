# WEBPACK TEMPLATE PROJECT

## Table of contents

- [About](#about)
- [Configuration](#configuration)
  - [CSS](#css)
  - [Typescript](#typescript)

## About

Шаблон проекта для Webpack в связке с Typescript, SASS/SCSS, Postcss

## Configuration

#### 1. Устанавливаем webpack

```
npm i webpack webpack-cli -D
```

#### 2. Конфигурируем webpack

**webpack.config.js:**
```js
const path = require('path')

module.exports = 
{
  // Указываем точку входа (стартовый файл)
  entry: './src/index.js',

  // Указываем режим работы (production/development)
  mode: 'development',

  // Указываем выходную директорию и файл
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  },
}
```

#### 3. Плагин **html-webpack-plugin**

Данный плагин обеспечивает генерацию index.html файла в выходной директории с включенными ссылками на собранные бандлы

устанавливаем плагин

```
npm i html-webpack-plugin -D
```

Добавляем в конфигурацию webpack информацию о плагине  
**webpack.config.js:**
```js
// Загружаем модуль с данным плагином
const HtmlWebpackPlugin = require('html-webpack-plugin')

module.exports = 
{
  ...
  output: {
    path: path.resolve(__dirname, 'dist'),
    // чтобы избежать проблем с кешированием нашего js кода в браузере - к названию выходного файла добавляем хеш сгенерированный на основе содержимого
    filename: 'bundle-[contenthash].js'
  },
  // Добавляем раздел плагинов в конфигурацию
  plugins: [
    // Создаем объект плагина
    new HtmlWebpackPlugin(),
  ],
}
```

#### 4. Плагин **clean-webpack-plugin**

Данный плагин обеспечивает очистку выходной директории, перед сборкой новых файлов. Это нужно чтобы не засорять директорию файлами из старых сборок.

```
npm i clean-webpack-plugin -D
```

Добавляем в конфигурацию webpack информацию о плагине  
**webpack.config.js:**
```js
// Загружаем модуль с данным плагином
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

module.exports = 
{
  ...
  plugins: [
    ...
    // Создаем объект плагина
    new CleanWebpackPlugin()
  ],
}
```


### CSS

#### 1. Добавляем поддержку CSS.

```
npm i css-loader mini-css-extract-plugin -D
```
- **css-loader** - модуль webpack, который добавляет поддержку css файлов
- **mini-css-extract-plugin** - плагин webpack, который обеспечивает сборку всех стилей в один бандл со стилями

Также может быть интересен еще один модуль:
- **style-loader** - модуль webpack, помещает все стили в элемент \<style\>, чтобы они применялись к элементам DOM (отдельный файл не создается), можно применять во время разработки, чтобы webpack следил за изменением стилей и автоматически перезагружал страницу.

Изменяем конфигурацию webpack  
**webpack.config.js:**
```js
// Загружаем модуль с данным плагином
const MiniCssExtractPlugin = require('mini-css-extract-plugin');

module.exports = 
{
  ...
  // Робавляем раздел с описанием модулей
  module: {
    // Добавляем раздел с правилами обработки файлов
    rules: [
      {
        // Описываем фильтр типов файлов, указываем что данным модулем будем обрабатывать файлы с расширением css
        test: /\.css$/,
        // Указываем массив обработчиков (порядок имеет значение)
        use: [
          // 2. Обеспечивает сборку обработанных css файлов в единый бандл
          MiniCssExtractPlugin.loader,
          // 1. Обеспечивает обработку css файлов
          'css-loader'
        ], 
      },
    ]
  },
  ...
  plugins: [
    ...
    // Создаем объект плагина
    new MiniCssExtractPlugin(),
  ],
}
```

#### 2. Плагин **css-minimizer-webpack-plugin**

Данный плагин обеспечивает минификацию полученного css бандла.

```
npm i css-minimizer-webpack-plugin -D
```

Добавляем в конфигурацию webpack информацию о плагине

**webpack.config.js:**
```js
// Загружаем модуль с данным плагином
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');

module.exports = 
{
  ...
  // Добавляем раздел с описанием плагинов оптимизации
  optimization: {
    minimizer: [
      // Создаем объект плагина
      new CssMinimizerPlugin()
    ],
  },
}
```

#### 3. Добавляем поддержку PostCSS

```
npm i postcss-loader -D
```

Изменяем конфигурацию webpack  
**webpack.config.js:**
```js
module.exports = 
{
  ...
module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          // 3. Обеспечивает сборку обработанных css файлов в единый бандл
          MiniCssExtractPlugin.loader,
          // 2. Обеспечивает обработку css файлов
          'css-loader',
          // 1. Добавляем обработчик postccs
          'postcss-loader'
        ], 
      },
    ]
  },
}
```

Добавляем обработчики для postcss
```
npm i autoprefixer -D
```
- **autoprefixer** - обеспечивает добавление вендорных префиксов для css стилей *{prop: value;} => {prop:value; -webkit-prop: value; -moz-prop: value;}*

Добавляем файл конфигурации для postcss  
**postcss.config.js:**
```js
module.exports = {
  plugins: [
    require('autoprefixer')
  ]
}
```

#### 3. Добавляем поддержку SASS/SCSS

```
npm i sass sass-loader -D
```

- **sass** - препроцессор для sass/scss файлов
- **sass-loader** - модуль webpack, который добавляет поддержку scss/sass файлов

Изменяем конфигурацию webpack  
**webpack.config.js:**
```js
module.exports = 
{
  ...
module: {
    rules: [
      {
        // меняем фильтр поддерживаемых файлов css, sass, scss
        test: /\.(sa|sc|c)ss$/,
        use: [
          // 4. Обеспечивает сборку обработанных css файлов в единый бандл
          MiniCssExtractPlugin.loader,
          // 3. Обеспечивает обработку css файлов
          'css-loader',
          // 2. Добавляем обработчик postccs
          'postcss-loader'
          // 1. Обеспечивает обработку scss/sass файлов
          'sass-loader'
        ], 
      },
    ]
  },
}
```

### Typescript

```
npm i typescript ts-loader @types/node -D
```

- **typescript** - компилятор typescript
- **ts-loader** - модуль webpack, который добавляет поддержку ts файлов
- **@types/node** - библиотека заголовочных файлов, обеспечивающая поддержку популярных js библиотек (пример: jQuery) в typescript

Изменяем конфигурацию webpack  
**webpack.config.js:**
```js
module.exports = 
{
  module: {
    rules: [
      // Добавляем новое правило обработки файлов
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        // Исключаем обработку файлов npm пакетов
        exclude: /node_modules/,
      },
    ]
  },
  // Добавляем раздел разрешения модулей
  resolve: {
    // Связываем расширения js, ts и tsx (видимо для того чтобы ts-loader их тоже обрабатывал)
    extensions: ['.tsx', '.ts', '.js'],
  },
}
```

Добавляем конфигурацию typescript  
**tsconfig.json**
```js
{
  "compilerOptions": {

    "target": "ESNEXT",                          /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017','ES2018' or 'ESNEXT'. */
    "module": "ESNext",                     /* Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', or 'ESNext'. */
    "lib": [                                  /* Specify library files to be included in the compilation. */
      "esnext",
      "dom"
    ],

    "declaration": true,                      /* Generates corresponding '.d.ts' file. */
    "sourceMap": true,                        /* Generates corresponding '.map' file. */

    "outDir": "./dist/obj",                       /* Redirect output structure to the directory. */

    "strict": true,                           /* Enable all strict type-checking options. */

    "moduleResolution": "node",               /* Specify module resolution strategy: 'node' (Node.js) or 'classic' (TypeScript pre-1.6). */

    "typeRoots": [                            /* List of folders to include type definitions from. */
      "node_modules/@types"
    ],

    "esModuleInterop": true,                   /* Enables emit interoperability between CommonJS and ES Modules via creation of namespace objects for all imports. Implies  allowSyntheticDefaultImports'. */
    
    "experimentalDecorators": true,          /* Enables experimental support for ES7 decorators. */
    "emitDecoratorMetadata": true            /* Enables experimental support for emitting type metadata for decorators. */
  },
  "exclude": [
    "node_modules",
    // нужно для того чтобы исключить компиляцию только что собранных файлов
    "./dist/**/*"
  ]
}
```