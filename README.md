# tensorflow.js 생활 코딩

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





