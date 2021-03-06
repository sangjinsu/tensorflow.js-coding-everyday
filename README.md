# tensorflow.js 생활 코딩

### 느낀점

>  tensorflow.js 를 작년 9월에 처음 사용해 봤다. 그 때 생활코딩에서는 파이썬 파트 밖에 없어서 자바스크립트로 변환하는 과정을 어렵게 적용했는데 danfo.js 를 사용해 쉽게 변환할 수 있다는 점이 놀라웠다. 머신러닝 모델을 학습시켜 로컬 스토리지에 저장해 클라이언트에서 사용하거나 node.js 를 사용해 모델을 사용해 보는 프로젝트를 하고 싶어졌다 

## 지도학습의 작업순서

1. 과거 데이터 준비
2. 모델의 모양 생성
3. 데이터로 모델을 학습(fit)
4. 모델 이용

## 나의 모델 만들기

1. 데이터를 tensor 형식으로 변경한다
2. 모델의 모양을 만든다. 입출력 방식을 정한다
3. 데이터로 모델을 학습한다. epoch 설정 => 몇 번 학습할 것인가?

```js
 // 1. 과거의 데이터를 준비합니다.
 const 온도 = [20, 21, 22, 23]
 const 판매량 = [40, 42, 44, 46]
 const 원인 = tf.tensor(온도)
 const 결과 = tf.tensor(판매량)
```

```js
// 2. 모델의 모양을 만듭니다.
const X = tf.input({ shape: [1] })
const Y = tf.layers.dense({ units: 1 }).apply(X)
const model = tf.model({ inputs: X, outputs: Y })
const compileParam = {
    optimizer: tf.train.adam(), // 효율적으로 모델을 만드는 방법
    loss: tf.losses.meanSquaredError, // 얼마나 모델이 잘 만들어졌는지 측정
}
model.compile(compileParam)
```

```js
// 3. 데이터로 모델을 학습시킵니다.
// const fitParam = { epochs: 100 }
const fitParam = {
    epochs: 3000,
    callbacks: {
    onEpochEnd: function (epoch, logs) {
        console.log('epoch', epoch, logs)
    },
  },
} // loss 추가 예제
model.fit(원인, 결과, fitParam).then(function (result) {
   // 4. 모델을 이용합니다.
   // 4.1 기존의 데이터를 이용
   const 예측한결과 = model.predict(원인)
   예측한결과.print()
})
```

---

## 모델의 정체

- y = ax + b
- a = weight 가중치
- b = bias 편향
- `var weights = model.getWeights()` - 가중치와 편향 구하기

## 모델 저장과 불러오기

- [모델 저장하기 및 로드하기](https://www.tensorflow.org/js/guide/save_load?hl=ko)

---

## 여러개의 변수

- 독립 변수
- 종속 변수

```js
 // 2. 모델의 모양을 만듭니다.
      const X = tf.input({ shape: [12] }) // 독립 변수
      const Y = tf.layers.dense({ units: 2 }).apply(X) // 종속 변수
```

- 데이터 형태

  - input

    ```js
    [0.00632, 18, 2.31, 0, 0.538, 6.575, 65.2, 4.09, 1, 296, 15.3, 396.9]
    ```

  - output

    ```js
    [4.98, 24]
    ```

    

## visualization

- tensorflowjs vis

- 모델 요약

  ```js
  const surface = { name: 'Model Summary', tab: 'Model Inspection' }
        tfvis.show.modelSummary(surface, model)
  ```

- 모델 실시간 학습

  ```js
  tfvis.show.history(surface, history, ['loss', 'acc'])
  ```

## 딥러닝

- ANN 인공신경망 Artificial Neural Network
- layer :값을 나타내는 node 들의 집합
- input/output layer : 입력/출력 값을 나타내는 레이어
- hidden layer : 입출력 레이어 사이에 있는 레이어  

```js
	  const X = tf.input({ shape: [13] })
      // hidden layer 추가
      const H = tf.layers.dense({ units: 13, activation: 'relu' }).apply(X)
      const H2 = tf.layers.dense({ units: 13, activation: 'relu' }).apply(H)
      const Y = tf.layers.dense({ units: 1 }).apply(H2)
```

---

## Classification

- 각 클래스 가능성의 합을 1로 만들어 주는 `softmax`

  ```js
  const Y = tf.layers
              .dense({ units: 3, activation: 'softmax' })
              .apply(H)
  ```

- 분류에서 손실을 계산하는 `categoricalCrossentropy`

  ```js
            const compileParam = {
              optimizer: tf.train.adam(),
              loss: 'categoricalCrossentropy',
              metrics: ['accuracy'],
            }
  ```

---

## Danfo.js

### Series

```js
const s = new dfd.Series([20, 21, 22, 23])
s.print()
// 통계 정보 출력
s.describe().print()
```

### Plot

```js
s.plot('s_table').table() // 테이블
s.plot('s_line').line()   // 직선
```

### DataFrame

```js
const d1 = new dfd.DataFrame([
  { 온도: 20, 요일: '월', 판매량: 40 },
  { 온도: 21, 요일: '월', 판매량: 42 },
  { 온도: 22, 요일: '월', 판매량: 44 },
  { 온도: 23, 요일: '월', 판매량: 46 },
])
d1.print()

//             객체 방식
const d2 = new dfd.DataFrame({
  온도: [20, 21, 22, 23],
  요일: ['월', '월', '월', '월'],
  판매량: [40, 42, 44, 46],
})
d2.print()
```

### DataFrame from CSV

```js
dfd .read_csv('https://gist.githubusercontent.com/egoing/43799f48b55b5eb2a8b37652eab72318/raw/3a207dd85d0b74968426e65ae2cee4cb1d4aef4f/lemon.csv')
  .then(function (data) {
  		data.print()
})
```

### Select and Query

```js
const s2 = new dfd.Series([20, 21, 22, 23])
console.log(s2, d1['온도'], d1) // s2와 d1['온도']는 Series이다.

d1.loc({ columns: ['온도', '판매량'] }).print()
d1.loc({ rows: [1, 3] }).print()
d1.loc({ columns: ['온도', '판매량'], rows: [1, 3] }).print()

d1.query({ column: '온도', is: '>', to: 20 }).print()
d1.query({ column: '온도', is: '>', to: 20 })
  .query({ column: '판매량', is: '<', to: 46 })
  .print()
```

### Column and Row

```js
      //            열 추가
      d1.addColumn({ column: '비', value: [1, 0, 1, 0] })
      d1.print()

      //             행삭제
      d1.drop({ axis: 0, index: [0] }).print()
      //             열삭제
      d1.drop({ axis: 1, columns: ['온도'] }).print()
```

### Transform

```js
      //             to Array
      d1.to_json().then(function (json) {
        console.log(JSON.parse(json))
      })
      // to Tensor
      d1.tensor.print()
```

