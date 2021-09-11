# Part1. Vue 시작하기 
Vue.js API : https://kr.vuejs.org/v2/guide/
CDN :   
```js
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```
## 컴포넌트
작고 독립적이며 재사용할 수 있는 컴포넌트로 구성된 대규모 애플리케이션을 구축할 수 있게 해주는 추상적 개념입니다  
컴포넌트 이름 지정시 2개의 단어 이상으로 지정(ex my-comp)
컴포넌트 구현 시 data 속성은 함수로 구현 하여야 한다.  
자세한 예제는 해당 component > index.html 참고
```js
// todo-item 이름을 가진 컴포넌트를 정의합니다
Vue.component('todo-item', {
  template: '<li>할일 항목 하나입니다.</li>'
})

var app = new Vue(...)
```
### 데이터 전달(props)
하위 속성과 상위 속성 사이의 단방향 바인딩을 형성합니다
```js
Vue.component('my-vm3',{
      template: '<div>{{ msg }} </div>',
      props: {
       msg:{
         type:[String,Number], //특정 타입으로 만 들어와야 함.
         default: 'Default!!', //값이 지정 안되어있으면
         required: true,// true 이면 필수로 데이터가 들어가야 함 
         validator:function(value){//유효성 검사 (함수로 할당 되어야 함 )
          return value === 'Hello'//msg값이 Hello가 아니면 에러 
         }
       }
      }
    })
```
### 사용자 지정 이벤트($emit)
비 부모-자식간 통신 할 때 사용
```html
<div id="app4">
  <my-vm4 :my-msg="message" @my-event="updateMessage"> </my-vm4>
</div>
```
```js
 Vue.component('my-vm4',{
      template: '<div @click="updateMsg">{{ myMsg }} </div>',
      props: {
       myMsg:String       
      },
      methods:{
        updateMsg(){
          this.$emit('my-event','Good')//`my-event`이름의 이벤트가 전달 될 때 2번째 인수의 값도 같이 전달.
        }
      }
    })

    const vm4 = new Vue({
      el:'#app4',
      data(){
        return{
          message: 'Hello'
        }
      },
      methods:{
        updateMessage(value){
          this.message = value
        }
      }
    })
```
### 컴포넌트 - Slot
하위 컴포넌트 템플릿에 최소한 하나의 `<slot>` 콘텐츠가 포함되어 있지 않으면 부모 콘텐츠가 삭제 됩니다.   
속성이 없는 슬롯이 하나 뿐인 경우 전체 내용 조각이 DOM의 해당 위치에 삽입되어 슬롯 자체를 대체합니다.
<br/>

---

<br/>

# Part2. Vue 가이드 문서 따라가기
## 인스턴스
Ex) const vm = new Vue()
### 라이프 사이클
<img src="https://kr.vuejs.org/images/lifecycle.png" alt="" height="1000" width="500">   

options 속성이나 콜백에 created: () => console.log(this.a) 이나 vm.$watch('a', newValue => this.myMethod()) 와 같은   
`화살표 함수 사용을 지양하기 바랍니다`. 화살표 함수는 this를 가지지 않기 때문에 화살표 함수에서의 this는 다른 변수로 취급되거나,  
렉시컬하게 호출한 변수를 발견할 때까지 부모 스코프에서 해당 변수를 찾습니다. 이 때문에   
`Uncaught TypeError: Cannot read property of undefined` 또는   
`Uncaught TypeError: this.myMethod is not a function`와 같은 오류가 발생하게 됩니다.   
Ex
```html
<div id="app">
    <div ref="msg">{{msg}}</div>
    <div ref="div">
    </div>
  </div>
```
```js
  const vm = new Vue({
      el: '#app',
      data:{
        msg:'Hellow Vue.'
      },
      //라이프 사이클 예제
      beforeCreate(){
        console.log('BeforeCreate!!')
      },
      created(){
        console.log('Create!!')
      },
      beforeMount(){
        console.log('BeforeMount!!',this.$refs.div)
      },
      mounted(){
        console.log('Mounted!!',this.$refs.div)
      },
      beforeUpdate(){
        console.log('BefoeUpdate!!',this.$refs.msg.innerText)
      },
      updated(){
        console.log('Updated!!',this.$refs.msg.innerText)
      },
      beforeDestroy(){
        console.log('BeforeDestroy!!')
      },
      destroyed(){
        console.log('Destroyed!!')
      }
    })    
```
## 템플릿 문법
v-once : 한번만 랜더링(변경이 안댐)
v-html : 원시HTML 태그 삽입 가능
Vue 보간 안에 JavaScript 표현식만 사용 가능. 로직은 사용 불가능.   
Ex) {{number+2 + new Date()}}
v-bind:class='className'(:class='className') : DOM 에서 클래스 동적 변경 가능
v-on:click == @click

## Computed
오리지날 데이터 만으로는 무엇가를 할 수 없을 때 Computed를 사용 하여 
데이터를 재가공 하여 사용.
### ComputedCaching
computedCaching.html 예제 참조
반복적으로 함수를 호출하면 호출 할 때 마다 연산이되어 부화를 줄수 있지만,   
캐싱을 사용 하면 반복적인 호출 시 기존 연산된 결과값만을 가져옵니다.
### Getter or Setter
```js
 const vm = new Vue({
      el: '#app',
      data:{
        msg3:'Computed Get or Set',
      },
      computed:{
       
        //reversedMsg(){
         // return this.msg3.split('').reverse().join('')        
         //밑에 reversedMsg get()와 동일
        //}
        reversedMsg:{
          //Getter
          get(){
            return this.msg3.split('').reverse().join('')  
          },
          //Setter
          set(value){
            this.msg3 = value
          }
        }
      }
   })    
```

## Watch
특정한 데이터를 감시하고 있다가 변경 시 어떠한 로직을 작동 할것인지 에 대해서 작성.   
비동기 처리에 유리 하게 처리 가능.
```js
 const vm = new Vue({
      el: '#app',
      data:{
        msg3:'Watch Testing',
      },
      computed:{
        reversedMsg:{
          //Getter
          get(){
            return this.msg3.split('').reverse().join('')  
          },
          //Setter
          set(value){
            this.msg3 = value
          }
        }
      },
      watch:{
        msg3(newMsg){
          console.log('New Data '+newMsg)
        },
        //데이터 뿐만 아니라 가공된 데이터도 가능
        reversedMsg(newMsg){
          console.log('New reversedMsg '+newMsg)
        }
      }
    })
    //콘솔 창에 vm.reversedMsg = '변경 데이터' or  vm.msg3 = '변경 데이터'
    //테스트 가능.
```
## 렌더링
### 조건부 show or v-if
v-if, v-else-if, v-else : true/false 여부에 따라  DOM에서 제거 됨(요소가 제거)  
일반적으로는 v-if를 사용. 매우 자주 바꾸기를 원한다면 v-show 런타임 시 조건이 바뀌지 않으면 v-if를 권장.
### 리스트
- ### 배열(Array)
  수정, 삭제, 삽입 시 기본적으로 자바 스크립트에 배열 변이 메소드들 사용.
 변이 메소드   
  - push()
  - pop()
  - shift()
  - unshift()
  - splice()
  - sort()
  - reverse()   
    콘솔을 열고 이전 예제의 items 배열로 변이 메소드를 호출하여 재생할 수 있습니다.  
    예: example1.items.push({ message: 'Baz' })
- ### 객체(Object)
  index사용 시 3번째 인수에 index 2번째인수는 객체가 들어가고 첫번째 인수는 사용자 정의 이름 이 들어간다.
  ```html
  <ul class="heropy">
    <li v-for="(value,key , index) in heropy" :key="index">
    {{ value }}
    </li>
  </ul>
  ```
  기존 개체 데이터는 반응성을 가지고 정의 되어있지 않은 개체데이터는 반응성을 가지고 있지 않음.
  ```js
  //개체 데이터 동적으로 추가 하는법 
  //얕은 복사 assign == vue.set 
  Object.assign({},vm.heropy, {homepage: 'geropy.blog'})
  ```
- ### Vue.set
  반응성을 띤 데이터 및 개체 데이터 주입 방법.
  ```js
  // 1:갱신 될 배열  2: 인덱스 번호 3: 추가 내용
  this.$set(this.computedtodos,3,{title:'야식먹기!!!!'}) 
  // 1:갱신 될 개체데이터  2: 추가할 프로퍼티 이름 3: 프로퍼티 실제 값
  this.$set(this.computedtodos,3,{title:'야식먹기!!!!'}) 
  ```

## 이벤트 핸들링
- ### 메소드 이벤트 핸들러
메소드 여러개 사용시 매개변수가 없어도 () 넣어주어야 함.
한개 사용시 매개변수 없으면 () 생략 가능.
```html
@click="clickMethod1(eventTds.title , $event); clickMethod2();"
```
- ### 이벤트 수식어
이벤트 핸들러 내부에서 event.preventDefault() 또는 event.stopPropagation()를 호출하는 것은 매우 보편적인 일입니다. 메소드 내에서 쉽게 이 작업을 할 수 있지만, DOM 이벤트 세부 사항을 처리하는 대신 데이터 로직에 대한 메소드만 사용할 수 있으면 더 좋을 것입니다.

이 문제를 해결하기 위해, Vue는 v-on 이벤트에 이벤트 수식어를 제공합니다. 수식어는 점으로 표시된 접미사 입니다.

`이벤트 버블링`은 특정 화면 요소에서 이벤트가 발생했을 때 해당 이벤트가 더 상위의 화면 요소들로 전달되어 가는 특성을 의미합니다

.stop : 이벤트 버블링를 막음.
.prevent : a 태그나 submit 태그는 누르게 되면 href 를 통해 이동하거나 , 창이 새로고침하여 실행됩니다.
preventDefault 를 통해 이러한 동작을 막아줄 수 있습니다.
.capture : 버블링의 반대방향으로 
.self : 이벤트가 발생한 해당 요소에서만
.once : 이벤트를 한번만 발생.
.passive : 스크롤 이벤트 ,터치 이벤트 충돌 개선 
```html
<!-- stop, prevent ... 등 넣을수 있음. ex) @click.stop -->
<div class="child" @click.self="clickHandler"></div>
```
- ### 키수식어
키보드 이벤트를 청취할 때, 종종 공통 키 코드를 확인해야 합니다. Vue는 키 이벤트를 수신할 때 v-on에 대한 키 수식어를 추가할 수 있습니다.
체인링(합성)도 가능함.

https://kr.vuejs.org/v2/guide/events.html#%ED%82%A4-%EC%88%98%EC%8B%9D%EC%96%B4

## 폼 입력 바인딩 
v-model : 양반향 데이터 바인딩 제공
### 한글 사용
### 수식어
lazy == change
trim: 앞뒤 공백제거
number: 숫자 데이터로 변환
```html
<input type="text" v-model.lazy="message2">
```