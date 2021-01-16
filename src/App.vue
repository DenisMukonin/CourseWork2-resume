<template>
  <div class="container column">
    <form class="card card-w30">
      <div class="form-control">
        <label for="type">Тип блока</label>
        <select id="type" v-model="typeBlock">
          <option selected value="title">Заголовок</option>
          <option value="subtitle">Подзаголовок</option>
          <option value="avatar">Аватар</option>
          <option value="text">Текст</option>
        </select>
      </div>

      <div class="form-control">
        <label for="value">Значение</label>
        <textarea id="value" rows="3" v-model="textAreaValue"></textarea>
      </div>

      <app-button
          :disabled="disableButton"
          @action="addBlock"
      >Добавить</app-button>

    </form>

    <app-loader v-if="!loadingBlock"></app-loader>
    <div v-else class="card card-w70">
      <div v-if="arrayBlock.length" v-for="item in arrayBlock" :key="item.name" >
        <component
            :is="item.name"
            :text="item.text"
        ></component>
      </div>

      <h3 v-else>Добавьте первый блок, чтобы увидеть результат</h3>
    </div>
  </div>
  <div class="container">

    <p v-if="!isClose">
      <app-button
          @action="loadComments"
      >Загрузить комментарии</app-button>
    </p>

    <div v-else>
      <app-comments
          v-if="!loading"
          :commentsPeople="comments"
      ></app-comments>

      <app-loader v-else></app-loader>
    </div>

  </div>
</template>

<script>
import AppLoader from "@/AppLoader";
import AppBlockTitle from "@/AppBlockTitle";
import AppBlockAvatar from "@/AppBlockAvatar";
import AppBlockSubtitle from "@/AppBlockSubtitle";
import AppBlockText from "@/AppBlockText";
import AppButton from "@/AppButton";
import AppComments from "@/AppComments";
import axios from 'axios'

export default {
  data() {
    return {
      typeBlock: 'title',
      textAreaValue: '',
      arrayBlock: [],
      comments:[],
      loading: false,
      loadingBlock: false,
      isClose: false
    }
  },
  mounted() {
    this.loadBlocks()
  },
  computed: {
    disableButton() {
      return this.textAreaValue.length <= 3
    },
    componentName() {
      return 'app-block-' + this.typeBlock
    }
  },
  methods: {
    addBlock() {
      const dateBlock = {
        name: this.componentName,
        text: this.textAreaValue
      }

      this.arrayBlock.push(dateBlock)

      this.createBlock(dateBlock)

      this.textAreaValue = ''
      this.typeBlock = 'title'
    },
    async createBlock(dataBlock){
      await fetch('https://vuejs3-cli-http-default-rtdb.firebaseio.com/blocks.json', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(dataBlock)
      })
    },
    async loadBlocks(){
      try {
        const {data} = await axios.get('https://vuejs3-cli-http-default-rtdb.firebaseio.com/blocks.json')
        if (data) {
          this.arrayBlock = Object.keys(data).map(key => {
            return {
              name: data[key].name,
              text: data[key].text
            }
          })
        }

        this.loadingBlock = true
      } catch (e) {
      }
    },
    async loadComments() {
      this.loading = true
      try {
        const {data} = await axios.get('https://jsonplaceholder.typicode.com/comments?_limit=42.json')

        if (!data) {
          throw new Error('Список комментариев пуст')
        }

        this.comments = Object.keys(data).map(key => {
          return {
            email: data[key].email,
            body: data[key].body
          }
        })

        this.loading = false
        this.isClose = true
      } catch (e) {
        this.loading = false
        console.log(e.message)
      }
    }
  },
  components: { AppLoader, AppBlockTitle, AppBlockAvatar, AppBlockSubtitle, AppBlockText, AppButton, AppComments }
}
</script>

<style>
  .avatar {
    display: flex;
    justify-content: center;
  }

  .avatar img {
    width: 150px;
    height: auto;
    border-radius: 50%;
  }
</style>
