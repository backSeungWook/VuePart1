# Part1. Vue 시작하기 
Vue.js API : https://kr.vuejs.org/v2/guide/
CDN :   
```js
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```
## 컴포넌트
작고 독립적이며 재사용할 수 있는 컴포넌트로 구성된 대규모 애플리케이션을 구축할 수 있게 해주는 추상적 개념입니다
```js
// todo-item 이름을 가진 컴포넌트를 정의합니다
Vue.component('todo-item', {
  template: '<li>할일 항목 하나입니다.</li>'
})

var app = new Vue(...)
```

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
