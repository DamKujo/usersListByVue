<template>
  <div class="row">
    <div class="cell">
      <h4>{{ user.name }}</h4> 
      <div v-if="user.children.length == 0"></div>
      <div v-else class="box">
        <span >Детей - {{user.children.length}}</span>
        <span>↴
        <NewUser class="children plusChild" v-for="(child, indexSub) in user.children" v-bind:user="child" :index="indexSub" :key="child.id">
        </NewUser>
        </span>
      </div>
    </div>
    <div class="cell">
      <h4>{{ user.phone }}</h4>
    </div>
  </div>
</template>   

<script>
export default{
  name: 'NewUser',
  props:{
      'name':String,
      'phone': String, //Number
      'childs': Array,
      'id': Number,
      'isChild': Boolean,
      'user': Object,
      'index': Number
  }, 
  data(){
    return{
    }
  }
}
</script>

<style scoped>
.plusChild{
  padding-left: 20px;
  width: 100%;
  display: table-row-group; 
  border: 3px solid #000000; 
}
.box{
  display: flex;
  justify-content: space-between;
}
</style>