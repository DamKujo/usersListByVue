<template>
  <div id="app">
    <button class="btn" @click="openModal">Добавить пользователя</button>
    <div class="table">
      <div class="row header">
          <div class="cell">Имя</div>
          <div class="cell">Номер телефона</div>
      </div>
      <NewUser
      v-for="(user, index) in users"
      :index="index"
      :user="user"
      :name="user.name"
      :phone="user.phone"
      :childs="user.children"
      :key="user.id"
      :is-child="user.isChild"
      />
    </div>

    <div v-if="isModalOpen" class="modal">
      <div class="modal-content">
        <span @click="closeModal" class="close">×</span>
        <h2>Добавить пользователя</h2>
        <form @submit.prevent="addUser" class='form'>
          <div>
            <input type="text" v-model="newUser.name" placeholder="Имя" required />
          </div>
          <div>
            <input type="text" v-model="newUser.phone" placeholder="Номер телефона. Начните с +" required />
          </div>
          <div>
            <select v-model="newUser.parentId">
              <option value="">Выберите родителя</option>
              <option v-for="user in allUsers" :key="user.id" :value="user.id">{{ user.name }}</option>
            </select>
          </div>
          <button v-if="newUser.name != '' && checkUser(newUser.phone) == true" class="btn" type="submit">Сохранить</button>
          <button v-else disabled class="btn" type="submit">{{ this.error }}</button>
        </form>
      </div>
    </div>
  </div>
</template>

<script>

import NewUser from './NewUser.vue';
export default {
  data() {
    return {
      users: [],
      allUsers: [],
      newUser: { name: '', phone: '', parentId: '', children: [] },
      isModalOpen: false,
      error: 'Вы не ввели имя!'
    };
  },
  components:{
    NewUser: () => import('./NewUser.vue')
  },
  methods: {
    openModal() {
      this.isModalOpen = true;
    },
    closeModal() {
      this.isModalOpen = false;
      this.resetForm();
    },
    resetForm() {
      this.newUser = { name: '', phone: '', parentId: '' };
    },
    addUser() {
      const newId = Date.now();
      const newUser = { 
        id: newId, 
        name: this.newUser.name, 
        phone: this.newUser.phone, 
        children: [],
        isChild: false
      };
      if (this.newUser.parentId) {
        newUser.isChild = true
        const parent = this.allUsers.find(user => user.id === this.newUser.parentId)
        parent.children.push(newUser)
        this.allUsers.push(newUser)
      } else {
        this.users.push(newUser)
        this.allUsers.push(newUser)
      }
      this.saveUsers()
      this.closeModal()
    },
    checkUser(number){
      const numberRegex = /^(\s*)?(\+)?([- _():=+]?\d[- _():=+]?){10,14}(\s*)?$/;
      if(numberRegex.test(Number(number))){
        this.error = 'Вы не ввели имя!';
        return true
      }
      this.error = 'Вы ввели некорректный номер!';
      return false
    },
    saveUsers() {
      localStorage.setItem('users', JSON.stringify(this.users))
      localStorage.setItem('allUsers', JSON.stringify(this.allUsers))
    },
    loadUsers() {
      const storedUsers = localStorage.getItem('users');
      if (storedUsers) {
        this.users = JSON.parse(storedUsers)
      }
    }
  },
  mounted() { 
    
    this.loadUsers()
  }
}
</script>

<style>
/* other styles */
.table {
  display: table;
  width: 60%;
  border-collapse: collapse;
  margin: 0 auto;
}

.row {
  display: table-row;
}

.cell {
  display: table-cell;
  padding: 15px;
  border: 1px solid #dddddd;
  text-align: left;
}
.cell h4{
  margin: 0;
  font-weight: 600;
}
.header {
  background-color: #4CAF50;
  color: white;
}
/* end */
.form{
  display: flex;
  flex-direction: column;
  width: 70%;
  justify-content: space-between;
  align-items: center;
  margin: 0 auto;
}
.form div{
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  margin-bottom: 10px;
  padding: 5px;
}
.form label{
  margin-right: 25px;
}
.form input, form select{
  width: 250px;
  padding: 15px 5%;
  font-family: 'Trebuchet MS', 'Lucida Sans Unicode', 'Lucida Grande', 'Lucida Sans', Arial, sans-serif;
  font-weight: 300;
  margin-bottom: 5px;
  border: 0;
  background-color: #888;
  color: #000000;
  outline: none;
  font-size: 15px;
}
.form input::placeholder{
  color: #c9c9c9;
}
.children{
  font-weight: 300;
  background-color: #c3c3c3;
}
.btn{
  width: 200px;
  background: #7d7d7d;
  color: #000000;
  font-weight: 600;
  font-size: 18px;
  border-radius: 10px;
  border: 2px solid #000000;
  padding: 10px 15px;
  cursor: pointer;
  transition: transform 500ms ease;
  margin-bottom: 20px;
}
.btn:disabled{
  cursor: not-allowed;
  background-color: darkred;
  color: #fff;
}
.btn:hover{
  transform: scale(1.1) translateY(-3px);
}
.modal {
  display: block;
  position: fixed;
  z-index: 1000;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0,0,0,0.5);
}
.modal-content {
  border-radius: 10px;
  background-color: #fff;
  margin: 14% auto;
  padding: 20px;
  border: 1px solid #888;
  width: 60%;
  min-height: 200px;
}
.close {
  color: #aaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
  cursor: pointer;
}
</style>
