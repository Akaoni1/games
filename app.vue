<template>
  <div>
    <NuxtPage />
  </div>
</template>
<style>
@media (prefers-color-scheme: dark) {
  :root {
    --background-color: black;
    --text-color: white;
    --primary-color: #ff5722;
    --primary-hightlight-color: #ff8a65;
    --secondary-color: #ff9800;
    --secondary-hightlight-color: #ffc947;
  }
}

@media (prefers-color-scheme: light) {

  :root {
    --background-color: white;
    --text-color: black;
    --primary-color: #ff5722;
    --primary-hightlight-color: #ff8a65;
    --secondary-color: #ff9800;
    --secondary-hightlight-color: #ffc947;
  }
}

body {
  background-color: var(--background-color);
  color: var(--text-color);
  margin:0;
}
</style>
