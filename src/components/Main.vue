<script setup lang="ts">
import { reactive, ref, onMounted } from 'vue'
import Swal from 'sweetalert2'

defineProps<{ title: string }>()

interface IDataItem {
  id: number
  name: string
  image: string
  email: string
  phone: string
}

const userData = reactive<{ list: IDataItem[] }>({
  list: []
})

const sheetId = "1LX8B-H-g9qzetX6YOq_GbrXP-NIui81fV9fovUKykT8"
const tableName = "table1"
const apiKey = "AIzaSyA1dDhrQyh_WA_BtkCOYlinW1SYJPwTSsI"

onMounted(async () => {
  await fetch(`https://sheets.googleapis.com/v4/spreadsheets/${sheetId}/values/${tableName}?alt=json&key=${apiKey}`).then(response => response.json()).then((response: { values: string[] }) => {
    // 第一筆為表格 header，若筆數小於等於 1 代表不存在資料
    if (response.values.length <= 1) {
      userData.list = []
      return
    }
    
    // 塞資料
    for(let i = 1; i < response.values.length; i++) {
      userData.list.push({
        id: parseInt(response.values[i][0]),
        name: response.values[i][1],
        image: response.values[i][2],
        email: response.values[i][3],
        phone: response.values[i][4]
      })
    }
  }).catch((e: any) => {
    console.warn(e)
    userData.list = []
  })
})

function copyToClipboard(text: string) {
  Swal.fire({
    title: "成功複製到剪貼簿",
    icon: "success",
    confirmButtonColor: "green"
  })
  navigator.clipboard?.writeText && navigator.clipboard.writeText(text);
}
</script>

<template>
  <h1>{{ title }}</h1>

  <div class="descriptionWrapper">
    <p>
      筆記：<a href="">Wayne's blog | 偉恩的部落格 | 技術博客</a>
    </p>
  </div>

  <hr />

  <div class="listWrapper">
    <div v-for="(item, index) in userData.list" :key="index" class="card">
      <div class="avatarWrapper">
        <img class="cardImg" :src="item.image" :alt="`image-${item.name}`">
      </div>
      <h3 class="cardTitle">{{ item.name }}</h3>
      <ul class="infoWrapper">
        <li>
          E-mail：
          <a href="javascript:void(0)" @click="copyToClipboard(item.email)">{{ item.email }}</a>
        </li>
        <li>
          Phone：
          <a href="javascript:void(0)" @click="copyToClipboard(item.phone)">{{ item.phone }}</a>
        </li>
      </ul>
    </div>
  </div>

  <p>
    Check out：
    <a href="https://github.com/wjdesign/google-sheet-db" target="_blank">
      https://github.com/wjdesign/google-sheet-db
    </a>
  </p>
  <p class="copyright">copyright © 2020 - 2022 <a href="https://wayneblog.ga/">Wayne's blog | 偉恩的部落格 | 技術博客</a>, LICENSED UNDER CC BY-NC-SA 4.0</p>
</template>

<style lang="scss" scoped>

.listWrapper {
  display: flex;
  flex-wrap: wrap;
  width: 100%;
  margin-bottom: 100px;

  .card {
    display: flex;
    flex-direction: column;
    flex: 0 0 25%;
    transition-duration: 0.5s;

    &:hover {
      transform: scale(103%, 103%);

      .cardImg {
        box-shadow: 0 10px 5px rgba(0, 0, 0, 0.2);
      }
    }

    .avatarWrapper {
      width: 100%;

      .cardImg {
        width: 100%;
        display: block;
        margin-right: auto;
        margin-left: auto;
        border-radius: 50%;
        aspect-ratio: 1/1;
        transition-duration: 0.5s;
      }
    }

    .infoWrapper {
      padding: 0;
      list-style: none;
      text-align: left;

      li {
        a {
          transition-duration: 0.3s;
        }

        &:hover {
          a {
            color: #5aaaee;
          }
        }
      }
    }
  }
}

.copyright {
  color: #888;
}

@media screen and (max-width: 767px) {
  h1 {
    font-size: 32px;
    word-break: break-all;
  }

  .listWrapper {
    .card {
      flex: 0 0 50%;

      &:hover {
        transform: scale(100%, 100%);

        .cardImg {
          box-shadow: none;
        }
      }
    }
  }
}

@media screen and (max-width: 420px) {
  h1 {
    font-size: 32px;
    word-break: break-all;
  }

  .listWrapper {
    .card {
      flex: 0 0 100%;

      &:hover {
        transform: scale(100%, 100%);

        .cardImg {
          box-shadow: none;
        }
      }

      .infoWrapper {
        text-align: center;
      }
    }
  }
}
</style>
