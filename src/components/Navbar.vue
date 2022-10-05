<template>
  <div class="navbar">
    <div @click = "$router.push('/')">Vue 3</div>
    <div class="navbar__btns">
      <my-button @click = "$router.push('/about')">О проекте</my-button>
      <my-button @click = "$router.push('/posts')">Посты</my-button>
      <my-button @click = "$router.push('/store')">Store</my-button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'navbar'
}
</script>

<style scoped>
.navbar{
  height: 50px;
  background-color: lightblue;
  box-shadow: 2px 2px 4px grey;
  display: flex;
  align-items: center;
  padding: 0 15px;
}
.navbar__btns{
  margin-left: auto;
  display: flex;
  gap: 20px;
}
.about,
.posts{
  font-size: 36px;
  color: green;
  text-decoration: none;
}
</style>