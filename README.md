# faceMask-usingJavaScript-ml5js
ml5js와 JavaScript를 이용하여 이미지로부터 눈,코,입을 인식하는 프로그램입니다.

# Code
## sketch.js
```jsx
let img, parts;
let options = {withLandmarks: true, withDescriptors: false};

function preload() {
  img = loadImage('face.png');
}

function setup() {
  createCanvas(img.width*2, img.height);
  faceapi = ml5.faceApi(options, modelReady);
  background(255);
  image(img, img.width, 0);
  image(img, 0, 0);
}

function modelReady() {
  faceapi.detectSingle(img, gotResults);
}

function gotResults(err, results) {
  if (err) {
    console.error(err);
    return;
  }
  console.log(results);
  parts = results.parts;
  noFill();
  stroke(0, 0, 255);
  strokeWeight(3);
  drawFace();
}

function drawFace() {
  stroke(255, 0, 255);

  //입술그리기
  fill(255,0,55);
  beginShape();
  for(let i=0; i<parts.mouth.length; i++){
    vertex(parts.mouth[i]._x, parts.mouth[i]._y);
  }
  vertex(parts.mouth[0]._x, parts.mouth[0]._y);
  endShape();
  fill(255);
  beginShape();
  for(let i=parts.mouth.length/2; i<parts.mouth.length; i++){
    vertex(parts.mouth[i]._x, parts.mouth[i]._y);
  }
  vertex(parts.mouth[0]._x, parts.mouth[0]._y);
  endShape();

  

  //코 그리기    
  noFill();
  stroke(0);
  beginShape();
  for(let i=0; i<parts.nose.length; i++){
    vertex(parts.nose[i]._x, parts.nose[i]._y);
  }
  endShape();
  
  //눈썹 그리기  
  stroke(100);
  strokeWeight(8);
  beginShape();
  for(let i=0; i<parts.leftEyeBrow.length; i++){
    vertex(parts.leftEyeBrow[i]._x, parts.leftEyeBrow[i]._y);
  }
  endShape();

  beginShape();
  for(let i=0; i<parts.rightEyeBrow.length; i++){
    vertex(parts.rightEyeBrow[i]._x, parts.rightEyeBrow[i]._y);
  }
  endShape();
}
```

# Analysis,Description of faceApi

## faceApi 객체

```jsx
let img, parts,detections;
const options = {withLandmarks: true, withDescriptors: false};

function preload() {
  img = loadImage('face.png');
}

function setup() {
  //setting
  createCanvas(500, 500);
  img.resize(width*0.8, height*0.8);
  
  //create faceapi
  const faceapi = ml5.faceApi(options, modelReady); 
}
```

ml5js에서 지원하는 faceApi객체를 생성한다. 아래와 같은 매개변수를 가진다.

- option : detect option이다.  option은 아래와 같이 withLandmarks,withDescriptors가 있다.

```jsx
const options = {withLandmarks: true, withDescriptors: false};
```

- modelReady

> 대상모델이 로드되거나 준비되었다면 true를, 아니라면 false를 리턴
> 

## modelReady()

```jsx
function modelReady() {
  // print face api's info to console log
  console.log('face recognition ready!')
  console.log(faceapi)
  // call faceapi's detectSingle() <- img,gotResults
  faceapi.detectSingle(img, gotResults);
}
```

- console에 faceapi에 객체 정보를 출력
- faceapi.detectSingle(img, gotResults);
    
    **Inputs**
    
    - img
    
    : If given an image, this is the image that the face detection will be applied
    
    - gotResults
    
    Function. A function to handle the results of `.makeSparkles()`
    . Likely a function to do something with the results of makeSparkles.
    
    **Outputs**
    
    - **Array**
    
    : Returns an array of objects. Each object contains `{alignedRect, detection, landmarks, unshiftedLandmarks}`
    

## gotResults(err, results)

```jsx
function gotResults(err, results) {
  //error handling
  if (err) {
    console.error(err);
    return;
  }
  // print results to console
  console.log(results);
  // insert parts of face by given results
  detections = results;
  parts = results.parts;
  
  background(255);
  image(img, 0,0, width*0.8, height*0.8);
  if(detections){
    drawBox(detections)
    drawFace(detections)
  }  
  strokeWeight(2);
  drawFace();
}
```

- error 발생 시 콘솔에 error 출력
- 콘솔에 결과값 출력
- image 그리고, 결과값이 존재한다면
    - drawBox(detections) 호출
    - drawFace(detections) 호출

## drawBox(detections)

```jsx
function drawBox(detections){
    const alignedRect = detections.alignedRect;
    const {_x, _y, _width, _height} = alignedRect._box;
    noFill();
    stroke(161, 95, 251);
    strokeWeight(2)
    rect(_x, _y, _width, _height)
}
```

## drawFace(detections)

```jsx
function drawFace() {
  // black stroke
  stroke(0);
  
	push()

		  //입술그리기
		  //fill lips with red
		  fill(255,0,0);
		  beginShape();
		  for(let i=0; i<parts.mouth.length; i++){
		    vertex(parts.mouth[i]._x, parts.mouth[i]._y);
		  }
		  vertex(parts.mouth[0]._x, parts.mouth[0]._y);
		  endShape();
		  
		  //white
		  fill(255);
		  beginShape();
		  for(let i=parts.mouth.length/2; i<parts.mouth.length; i++){
		    vertex(parts.mouth[i]._x, parts.mouth[i]._y);
		  }
		  vertex(parts.mouth[0]._x, parts.mouth[0]._y);
		  endShape();
		
		  
		
		  //코 그리기    
		  noFill();
		  beginShape();
		  for(let i=0; i<parts.nose.length; i++){
		    vertex(parts.nose[i]._x, parts.nose[i]._y);
		  }
		  endShape();
		
		  //눈 그리기
		  noFill();
		  beginShape();
		  for(let i=0; i<parts.leftEye.length; i++){
		    vertex(parts.leftEye[i]._x, parts.leftEye[i]._y);
		  }
		  vertex(parts.leftEye[0]._x, parts.leftEye[0]._y);
		  endShape();
		  // yesFill()
		  beginShape();
		  for(let i=0; i<parts.rightEye.length; i++){
		    vertex(parts.rightEye[i]._x, parts.rightEye[i]._y);
		  }
		  vertex(parts.rightEye[0]._x, parts.rightEye[0]._y);
		  endShape();
		
		  //눈썹 그리기  
		  //grey stroke
		  stroke(102,51,0);
		  //fill with white
		  fill(153,76,0);
		  beginShape();
		  for(let i=0; i<parts.leftEyeBrow.length; i++){
		    vertex(parts.leftEyeBrow[i]._x, parts.leftEyeBrow[i]._y);
		  }
		  endShape();
		
		  beginShape();
		  for(let i=0; i<parts.rightEyeBrow.length; i++){
		    vertex(parts.rightEyeBrow[i]._x, parts.rightEyeBrow[i]._y);
		  }
		  endShape();

	pop();
}
```

# **FaceApi Landmarks Demo** Code

```java
let faceapi;
let img;
let detections;

// by default all options are set to true
const detection_options = {
    withLandmarks: true,
    withDescriptors: false,
}

function preload(){
    img = loadImage('assets/frida.jpg')
}

function setup() {
    createCanvas(200, 200);
    img.resize(width, height);

    faceapi = ml5.faceApi(detection_options, modelReady)
    textAlign(RIGHT);
}

function modelReady() {
    console.log('ready!')
    console.log(faceapi)
    faceapi.detectSingle(img, gotResults)
}

function gotResults(err, result) {
    if (err) {
        console.log(err)
        return
    }
    // console.log(result)
    detections = result;

    // background(220);
    background(255);
    image(img, 0,0, width, height)
    if (detections) {
        // console.log(detections)
        drawBox(detections)
        drawLandmarks(detections)
    }
}

function drawBox(detections){
    const alignedRect = detections.alignedRect;
    const {_x, _y, _width, _height} = alignedRect._box;
    noFill();
    stroke(161, 95, 251);
    strokeWeight(2)
    rect(_x, _y, _width, _height)
}

function drawLandmarks(detections){
    noFill();
    stroke(161, 95, 251);
    strokeWeight(2)
    
    push()
    // mouth
    beginShape();
    detections.parts.mouth.forEach(item => {
        vertex(item._x, item._y)
    })
    endShape(CLOSE);

    // nose
    beginShape();
    detections.parts.nose.forEach(item => {
        vertex(item._x, item._y)
    })
    endShape(CLOSE);

    // left eye
    beginShape();
    detections.parts.leftEye.forEach(item => {
        vertex(item._x, item._y)
    })
    endShape(CLOSE);

    // right eye
    beginShape();
    detections.parts.rightEye.forEach(item => {
        vertex(item._x, item._y)
    })
    endShape(CLOSE);

    // right eyebrow
    beginShape();
    detections.parts.rightEyeBrow.forEach(item => {
        vertex(item._x, item._y)
    })
    endShape();

    // left eye
    beginShape();
    detections.parts.leftEyeBrow.forEach(item => {
        vertex(item._x, item._y)
    })
    endShape();

    pop();

}
```

# Reference

- ml5js ****Face-Api Document****

[https://learn.ml5js.org/#/reference/face-api?id=tutorials](https://learn.ml5js.org/#/reference/face-api?id=tutorials)

- **FaceApi Landmarks Demo**

[https://editor.p5js.org/ml5/sketches/FaceApi_Image_Landmarks](https://editor.p5js.org/ml5/sketches/FaceApi_Image_Landmarks)

- face-api.js github

[https://github.com/justadudewhohacks/face-api.js/blob/master/README.md#models-face-recognition](https://github.com/justadudewhohacks/face-api.js/blob/master/README.md#models-face-recognition)

- **Will you release facial expression detection for face-api?**

https://github.com/ml5js/ml5-library/issues/1039
