<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const prodList = ref([])
const delList = ref([])
const input_content = ref('')


const addProd = () => {
	if (input_content.value.trim() === '') {
		return
	}
	prodList.value.push({
		content: input_content.value,
		createdAt: new Date().getTime()
	})
  input_content.value=''
}
const doCheck = (prod, type)=> {
      if (type === "need") {
        const delMask = prodList.value.filter((p) => p === prod)
        prodList.value = prodList.value.filter((p) => p !== prod)
        delList.value.push(...delMask);
      } else {
        const noDelMask = delList.value.filter((p) => p === prod);
        delList.value = delList.value.filter((p) => p !== prod)
        prodList.value.push(...noDelMask);
      }
    }

const removeProd = (prod) => {
	prodList.value = prodList.value.filter((p) => p !== prod)
}
const removeDel = (prod) => {
	delList.value = delList.value.filter((p) => p !== prod)
}

watch(prodList, (newVal) => {
	localStorage.setItem('prodList', JSON.stringify(newVal))
}, {
	deep: true
})
watch(delList, (newVal) => {
	localStorage.setItem('prodList', JSON.stringify(newVal))
}, {
	deep: true
})

onMounted(() => {
	prodList.value = JSON.parse(localStorage.getItem('prodList')) || []
  delList.value = JSON.parse(localStorage.getItem('delList')) || []
})
</script>

<template>
	<main class="app">
        <header class="header">
            <section class="container">
              <div class="logo">ProdList</div>
              <form class="form" @submit.prevent="addProd">
                <input
                  type="text" 
					        name="content" 
					        class="input" 
					        placeholder="enter new product"
					        v-model="input_content"
                  >
                <input class="btn" type="submit" value="Add prod"/>
              </form>
            </section>
          </header>
          <section class="container">
            <h2>
              <span>Product List</span>
            </h2>
            <ul class="prod-list">

              <li
                v-for="prod in prodList"
                :key="prod.createdAt"
              >
                <div>
                  <input type="checkbox" v-on:change="doCheck(prod, 'need')"/>
                  <span>{{ prod.content }}</span>
                </div>
                <button class="btn-remove" v-on:click="removeProd(prod)">Remove</button>
              </li>

              <ul class="prod-list del-list">
                <li
                  v-for="prod in delList"
                  :key="prod.createdAt"
                  >
                  <div>
                    <input type="checkbox" @change="doCheck(prod, 'complete')" checked>
                    <span>{{ prod.content }}</span>
                 </div>
                  <button class="btn-remove" @click="removeDel(prod, 'complete')">Remove</button>
                </li>
              </ul>

            </ul>
          </section>
	</main>
</template>

