````
<template>
  <div>
    <todo-list></todo-list>
  </div>
</template>
<script>
  import todoList from '../components/todoList.vue'
  export default{
    data(){
      return{}
    },
    components:{
        todoList
    }
  }
</script>
````
````
<template>
  <div>
     <label>请输入</label>
     <input type="text" v-model="inputItem">
     
     <ul class="task">
       <li v-for="(item,index) in inputList">
         <span>{{ item.content}}</span>
         <button class="del" @click="deleteItem(index)">删除</button>
       </li>
     </ul>
     <p v-if="!inputList.length">暂无内容</p>
  </div>
</template>
<script>
  export default{
      data(){
        return {
            inputItem:'',
            inputList:[]
        }
      },
      methods:{
        addItem: function(){
          let item = {
            content:this.inputItem
          }
          this.inputList.push(inputItem)
        },
        deleteItem: function(index){ 
          this.inputList.splice(index,1)
        }
      }
  }
</script>
````