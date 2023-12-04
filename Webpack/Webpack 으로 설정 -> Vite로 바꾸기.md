이전 회사에서는 gulp를 사용하여 번들링했었는데, webpack으로 바뀌어 내가 원하는 환경을 세팅해보려고 한다.

## 폴더 구조
```js
dist
├── js
└── css
    └── style.css
src
├── css
│   ├── scss
│   │   ├── common // common, reset, font 등
│   │   ├── mixins // mixin 함수
│   │   └── pages // 페이지 별 scss
│   └── main.scss // @import 
├── img
│   └── page1.html
├── js
│   ├── app
│   └── index.js
└── index.html
```

## 웹팩 5와 ES 모듈 사용
전체 코드
```javascript
import { dirname, resolve } from 'path';

import { CleanWebpackPlugin } from 'clean-webpack-plugin';
import CssMinimizerPlugin from 'css-minimizer-webpack-plugin';
import HtmlWebpackPlugin from 'html-webpack-plugin';
import MiniCssExtractPlugin from 'mini-css-extract-plugin';
import TerserPlugin from 'terser-webpack-plugin';
import { fileURLToPath } from 'url';

const __dirname = dirname(fileURLToPath(import.meta.url));
const isProduction = process.env.NODE_ENV === 'production';

export default {
  mode: isProduction ? 'production' : 'development',
  entry: './src/js/index.js',
  output: {
    filename: 'js/main.js', // 빌드 결과 파일 이름
    path: resolve(__dirname, 'dist') // 결과물이 위치할 경로
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: resolve(__dirname, 'src', 'index.html')
    }),
    // 컴파일 + 번들링 CSS 파일이 저장될 경로와 이름 지정
    new MiniCssExtractPlugin({ filename: 'css/style.css' }),
    /**
     * 빌드 시 기존 파일을 지우고 새롭게 빌드한다.
     * output 경로에 있는 모든 파일이 자동 삭제 된다.
     */
    new CleanWebpackPlugin()
  ],
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: [MiniCssExtractPlugin.loader, 'css-loader', 'sass-loader']
      },
      {
        test: /\.(png|jpe?g|gif)$/i, // 이미지 파일을 처리하는 로더
        use: [
          {
            loader: 'file-loader',
            options: {
              name: '[name].[ext]', // 결과물 파일의 이름 형식
              outputPath: 'img', // 결과물을 저장할 경로
              publicPath: 'img' // 결과물을 참조하는 URL의 기본 경로
            }
          }
        ]
      }
    ]
  },
  optimization: {
    minimize: true,
    minimizer: [
      new TerserPlugin(), // JS 파일을 minify
      new CssMinimizerPlugin() // CSS 파일을 minify
    ]
  },
  /**
   * 개발 중 실시간으로 변화를 반영하여 확인하기 위해 웹팩 개발 서버 사용.
   * 웹팩 개발 서버는 파일 변경을 감지하고 자동으로 브라우저를 새로고침하여 변화를 반영함. (실시간 확인 가능)
   *
   * 웹팩 5와 webpack-dev-server 4에서는 기존 contentBase 옵션이 static으로 변경
   */
  devServer: isProduction
    ? undefined
    : {
        static: {
          directory: resolve(__dirname, 'dist')
        },
        hot: true,
        open: true
      }
};

```
## css (scss)
```javascript
devtool: isProduction ? 'source-map' : 'eval-source-map',
```
`devtool` 모드를 사용하면 개발자도구의 소스보기 탭에서 원본 소스들이 유출되므로 설정을 꺼두도록 한다.


## images
#### 폴더 위치
```bash
src
└── img
    ├── img-001.png
    ├── img-002.png
			  ...
```
    
#### 설치
이미지 파일을 빌드 과정에 포함
```bash
npm install --save-dev file-loader image-webpack-loader
```

[설명]
1. 웹팩 로더 `file-loader` or `url-loader`
	* 비 js 파일을 처리하도록 도와준다.
	* 이미지 파일을 모듈로 사용할 수 있게 해주고, 빌드 과정에서 출력 디렉토리 파일로 옮겨줌.
2. 최적화 로더 `image-webpack-loader`
	* 이미지 최적화 및 번들에 추가


[웹팩 보다 `Gulp`가 효율적인 작업]
* 이미지 최적화
* (svg) 스프라이트 생성

#### 웹팩 설정
```javascript
export default {
  // ...
  module: {
    rules: [
      // ...
      {
        test: /\.(png|jpe?g|gif)$/i, // 이미지 파일을 처리하는 로더
        use: [
          {
            loader: 'file-loader',
            options: {
              name: '[path][name].[ext]', // 결과물 파일의 이름 형식
              outputPath: 'img', // 결과물을 저장할 경로
              publicPath: 'img', // 결과물을 참조하는 URL의 기본 경로
            },
          },
					{
            loader: 'image-webpack-loader',
            options: {
              mozjpeg: {
                progressive: true,
              },
              optipng: {
                enabled: false,
              },
              pngquant: {
                quality: [0.65, 0.90],
                speed: 4
              },
              gifsicle: {
                interlaced: false,
              },
              webp: {
                quality: 75
              }
            }
          },
        ],
      },
    ],
  },
  // ...
};
```

이렇게 설정하면 웹팩이 png, jpeg, gif 등의 이미지 파일을 처리하고, `image-webpack-loader`를 통해 이미지를 최적화한 후, 결과물을 `dist/img` 폴더로 옮기게 된다.

### 사용 방법 
1. 이미지 파일을 사용하려면 JS 코드에서 `import` 하여 이미지 파일을 불러와야 한다.
이는 `require.context`를 사용하면 여러 이미지 파일을 한 번에 `import` 할 수 있다.

```javascript
const importAll = async (r) => {
	r.keys().forEach(async (key) => {
		await r(key);
	});
};

importAll((path) => import(`../img/${path}`));
```
### s3에 올린 후 클라우드 프론트로 최적화
리소스들은 사실 번들러를 거치는 것 보다는

github action에서 리소스 폴더만 별도로 s3, cloudfront 같은 곳에 배포하고,
최적화된 리소스를 적용하는 방법이 더 좋다.

리소스는 번들러에서 제외한 후 번들 된 것들만 웹서버에 올리는 방법.



